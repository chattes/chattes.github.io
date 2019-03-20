---
layout: post
title: Installed Elixir---> NOW WHAT? 
---

Sat Feb  9 19:44:11 2019

#### Load up you App in REPL(iex)

So I have created a new app. Easy peezy.

```Elixir 
mix new podder --sup 
```

Although TDD is proposed , I like to play around a bit with what I am building 
in the *REPL*
Loading the App created in REPL like this

```Elixir
iex -S mix
```
This loads the Application in the REPL.
We can reload with command 
>recompile

#### What will we create

We will create a PodCast app that gets the data from Various Podcasts.
We use Genserver per Podcast name. It will fetch data from the API and then
store data fetched in ETS for each Podcast.

#### GenServer

Genserver starts an Elixir Process and holds its own state.
It has a set of callbacks which can be used to Apply the business Logic.

We start a Genserver per Podcast. I want to name each Genserver as I dont
want to deal with PIDs. For that I use the *:global* atom to store the name
in the Global Module.

```
  def start_link(state, name) do
    GenServer.start_link(__MODULE__, state, name: {:global, name})
  end
```	

From iex start the Genserver as:

```
iex(1)> Podder.start_link(%{}, "jabber")
{:ok, #PID<0.134.0>}
iex(2)> Podder.get_episodes({:global, "jabber"})
%{}
iex(3)> 
```

The above starts a GenServer with name *jabber* which is registered in the
global Registry


#### Observer

Fire up an Elixir Shell and 
``` 
:observer.start 
```


#### Fault Tolerance

Fault Tolerance is baked into the Elixir/Erlang Systems and we will achieve it
with Supervision Trees

So our app will start a Supervision Tree , which will start a 
[Dynamic Supervisor](https://hexdocs.pm/elixir/DynamicSupervisor.html)
We need a Dynamic Supervisor as we dont know how many PodCast GenServer we will
be starting when the App Starts.
As and when I add my Favorite PodCast, the Dynamic Supervisor starts a new
Podcast Genserver.

#### More About GenServers

Lets look a little bit more about GenServers

According to Docs 
> A GenServer is a process like any other Elixir process and it can be used to keep state, execute code asynchronously and so on.

So the basic structure of our App is as Follows

We have multiple users who will register into the App and subscribe to their
Favorite Podcasts.
For each user we spin up a Genserver using Dynamic Supervisor as mentioned
above.
Each GenServer holds some State, like the Ids of the Podcasts I am Subscribed
to.
It also provides us an interface to search podcasts using wildcard search.

*Note: We use a third party API to get our Podcast Data*

###### Local Registry

All the GenServers we spin up for each user returns us a PID, with which we can
interact with the GenServer. Remembering and storing PIDs is not something I
would like to do, hence we use the Local Process Registry to give the GenServer
a name and basically get the internal pid of the GenServer using the unique
name.

Basically when starting the GenServer we start it with the via module

``` 
GenServer.start_link(__MODULE__, state, name: via_tuple(user_name)) 
```

Note the via_tuple it takes the Name and stores it in the Registry

```
defp via_tuple(name), do: {:via, Registry, {Podder.Registry, name}}
```

We can get the PID of the process as below

```
def whereis(name: name), do: Registry.whereis_name({Podder.Registry, name})
```

We also need to start the Registry, so in our main Application we start the
Registry under a Supervisor 

```
  def start(_type, _args) do
    # List all child processes to be supervised
    children = [
      # Starts a worker by calling: Podder.Worker.start_link(arg)
      # {Podder.Worker, arg},

      %{
        id: Registry,
        start: {Registry, :start_link, [[keys: :unique, name: Podder.Registry]]}
      },
      %{
        id: Podder.DynamicSupervisor,
        start: {Podder.DynamicSupervisor, :start_link, []}
      }
    ]

    # See https://hexdocs.pm/elixir/Supervisor.html
    # for other strategies and supported options
    opts = [strategy: :one_for_one]
    Supervisor.start_link(children, opts)
  end
	```
The above code starts a Local Process registry and a Dynamic Supervisor when our
Application starts.


###### Genserver callbacks

###### handle_call

When our server needs to return something back to the caller we use the
handle_call.
For our case we use it search for Podcasts and return the search results, we
dont have to modify the state, so we can write like.

```

  def handle_call({:search, query}, from, state) do
    response =
      with {:ok, result} <- Podder.ListenProvider.API.search_podcasts(query) do
        result
        |> Map.get("podcasts")
        |> Enum.map(fn x -> Map.take(x, @fields_podcasts) end)
      else
        _ -> %{}
      end

    {:reply, response, state}
  end

	```

	See the return from the function, we query the API( I had used Task.Async to
	query the API before, but thats an overkill)

	Return the response and keep the same state as before. This completes the
	Query interface

	*Note: Our Genserver will be blocked till the Query from the external API
	returns*

####### handle_cast
	We will use this only to change the state and not return any response back to
	the caller

	```
	  def handle_cast({:save, items}, state) do
    my_items =
      cond do
        is_list(items) -> items
        true -> [items]
      end

    new_pods = Map.get(state, "podcasts") |> Enum.concat(my_items)
    new_state = Map.put(state, "podcasts", new_pods)
    {:noreply, new_state}
   end
	```

	Here we take an item , item is podcast or multiple podcasts that the user has
	subscribed.
	We change our state with new Podcasts the user has subscribed. Note there is
	nothing returned back to the calling process and our Genserver is not blocked.

####### handle_info
	
	Usually we can run periodic tasks here. We can see later about this.


