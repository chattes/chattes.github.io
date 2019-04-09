---
layout: post
title: Elixir Notes 
---

### Elixir Tips and tricks

###### Filtering a Collection with Pattern Matching


``` Elixir

iex(33)> test

[ok: %{"a" => "one"}, error: %{"b" => "two"}]

iex(34)> Enum.filter(test, &match?({:ok, _},&1))

[ok: %{"a" => "one"}]

```

Instead of match


``` Elixir


    test |> Enum.filter(fn

      {:ok, _value} -> true

      _ -> false

    end)

```


###### Filter and Map and Extract nth Element of Tuple



```Elxir

iex(43)> test

[ok: %{"a" => "one"}, error: %{"b" => "two"}]

iex(44)> test |> Enum.filter(&match?({:ok, _},&1)) |> Enum.map(&(&1 |> elem(1)))

[%{"a" => "one"}]

```



###### Executing Anonymous function in a pipeline



```

    data

    |> Enum.map(&elem(&1, 1))

    |> write_poi

    |> (fn

          {:ok, data} -> IO.puts("Wrote #{Enum.count(data)} records succesfully")

          _ -> IO.puts("Failed to write records to File")

        end).()

```


###### Extracting Data One wants from JSON Responses. Use Sigils!



```

  @expected_fields ~w(

    login id avatar_url gravatar_id url html_url followers_url

    following_url gists_url starred_url subscriptions_url

    organizations_url repos_url events_url received_events_url type

    site_admin name company blog location email hireable bio

    public_repos public_gists followers following created_at updated_at

  )


  def process_request_url(url) do

    "https://api.github.com" <> url

  end


  def process_response_body(body) do

    body

    |> Poison.decode!

    |> Map.take(@expected_fields)

    |> Enum.map(fn({k, v}) -> {String.to_atom(k), v} end)

  end

end

```

###### Check Current Evironment in IEX

```

Mix.env

```

#### Phoenix

Generate Routes

###### Add Routes to router.ex and then run

```
mix phx.routes
```


###### Generating New Controllers

```
mix phx.gen.json Podder Podcast podcasts podcast_name:string  --context-app podder --no-schema
```

*Generates a New Controller with name Podder.Podcast with Context podder. Since we don’t have any DB so no-schema.*

