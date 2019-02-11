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

The above starts a GenServer with name "jabber" which is registered in the
global Registry


#### Fault Tolerance

Fault Tolerance is baked into the Elixir/Erlang Systems and we will achieve it
with Supervision Trees

So our app will start a Supervision Tree , which will start a 
[Dynamic Supervisor](https://hexdocs.pm/elixir/DynamicSupervisor.html)
We need a Dynamic Supervisor as we dont know how many PodCast GenServer we will
be starting when the App Starts.
As and when I add my Favorite PodCast, the Dynamic Supervisor starts a new
Podcast Genserver.

