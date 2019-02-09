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








