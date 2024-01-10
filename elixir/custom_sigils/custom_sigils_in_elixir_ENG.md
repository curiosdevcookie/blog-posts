# Custom sigils in Elixir

```elixir
defmodule MySigils do
  def sigil_c(string, []), do: String.upcase(string)
end

import MySigils

iex> ~c{hello world}
"HELLO WORLD"
```

```elixir
defmodule YourProject.SigilCase do
  def sigil_STR(string, 'u'), do: String.upcase(string)
  def sigil_STR(string, 'l'), do: String.downcase(string)
  def sigil_STR(string, 'r'), do: String.reverse(string)
end
```

```elixir
defmodule YourProject.PlanetarySigil do
  @emoji_map %{
    "star" => "⭐",
    "sun" => "🌞",
    "moon" => "🌙",
    "planet" => "🪐",
    "rocket" => "🚀",
  }

  def sigil_EMO(string, _modifiers) do
    String.split(string)
    |> Enum.map(&replace_with_emoji/1)
    |> Enum.join(" ")
  end

  defp replace_with_emoji(word) do
    Map.get(@emoji_map, word, word)
  end
end
```

```elixir
import YourProject.EmojiSigil

iex(92)> ~EMO{hello star hello sun and moon}
"hello ⭐ hello ☀️ and 🌙"
```

<!-- Custom sigil with modifier -->

```elixir
defmodule YourProject.ZooSigil do
  def sigil_ZOO(string, modifiers) do

    repeat_count = String.to_integer(List.to_string(modifiers))


    String.split(string)
    |> Enum.map(&replace_with_animal(&1, repeat_count))
    |> Enum.join(" ")
  end

  defp replace_with_animal(word, repeat_count) do
  emoji=
    case word do
      "cat" -> "🐱"
      "dog" -> "🐶"
      "bird" -> "🐦"
      _ -> word
    end

    String.duplicate(emoji, repeat_count)
  end
end
```

```elixir
import YourProject.ZooSigil

iex(92)> ~~ZOO(cat dog bird)3
"🐱🐱🐱 🐶🐶🐶 🐦🐦🐦"
```
