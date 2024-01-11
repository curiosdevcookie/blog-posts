# Sigil Sorcery: Custom Sigils in Elixir

Elixir sigils are general-purpose tools for handling common data types. Examples of built-in sigils include strings `(~s)`, regular expressions `(~r)`, dates `(~D)`, and lists of words `(~w)`. But, as you might have guessed, Elixir's flexibility doesn't stop here; Elixir allows us to create custom sigils for specific needs, and that is what we’re doing now.

## Feeling creative? The process of creating your own sigils

Conceptually, a sigil is a function that takes arguments like functions do. Sigil function names follow a specific syntax, as we can see below (cf. step 1). Unlike in other functions, we are not limited to the usual delimiters () around the function's parameters but have 8 different delimiters we can freely choose from (cf. step 2). Finally, the parameters of a sigil function comprise the input string and a list of modifiers (cf. step 3).

### How to build It: A Step-by-Step Recipe

Step 1: Define a function named sigil_x (replace 'x' with your character). The sigil name should be either a one-letter lowercase letter or a sequence of uppercase letters.

Step 2: Pick a delimiter of your choosing: `~r(hello)` `~r/hello/` `~r|hello|` `~r"hello"` `~r'hello'` `~r[hello]` `~r{hello}` `~r<hello>`. Pro tip: Although `~s/https:\/\//` and `~s(https://)` yield the same result, I find the latter expression to be (far) more readable.

```bash
# Different syntax, same result:
iex(14)> ~s/https:\/\//
"https://"

iex(15)> ~s(https://)
"https://"
```

Step 3: A sigil function takes two arguments: your input and a list of modifiers. Modifiers are optional. So we get the following function signature: `sigil_x(string, modifiers \\ [])`.

Step 4: Use your sigil function in your template or in iex like so: `<%= ~x(string) %>` and with a modifier: `<%= ~x(string)m %>`.

### Examples of Custom Sigils without a modifier

Without a modifier, our sigil function will simply return the modified input string. Let's see how this works in practice:

#### Example 1 without a modifier

```elixir
defmodule YourProject.SigilSorcery do
  def sigil_UPREV(string, _modifier) do
    String.upcase(string)
    |> String.reverse()
  end
end
```

Boom, you’ve got your personalized sigil!

```bash
import YourProject.SigilSorcery

iex(92)> ~UPREV(hello)
"OLLEH"
```

#### Example 2 without a modifier

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

```bash
import YourProject.EmojiSigil

iex(92)> ~EMO{hello star hello sun and moon}
"hello ⭐ hello ☀️ and 🌙"
iex(93)> ~EMO{star sun moon planet rocket}
"⭐ ☀️ 🌙 🪐 🚀"
```

### Examples of Custom Sigils with a modifier

Modifiers are optional. If you want to use them, you need to define them in the function signature. Modifiers are passed as a list of strings. As mentioned above, You call this function like so: `~x(string)m`. Let's see how this works in practice:

#### Example 1 with modifier

```elixir
defmodule YourProject.SigilCase do
  def sigil_STR(string, 'u'), do: String.upcase(string)
  def sigil_STR(string, 'l'), do: String.downcase(string)
  def sigil_STR(string, 'r'), do: String.reverse(string)
end
```

```bash
import YourProject.SigilCase

iex(92)> ~STR(hello)u
"HELLO"

iex(93)> ~STR(HellO)l
"hello"

iex(94)> ~STR(hello)r
"olleh"
```

#### Example 2 with modifier

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

```bash
import YourProject.ZooSigil

iex(92)> ~~ZOO(cat dog bird)3
"🐱🐱🐱 🐶🐶🐶 🐦🐦🐦"
```

## Conclusion: Embrace the Sigil Sorcery

Sigils are handy little tools; they’re a mix between optimisation of efficiency and a playground for creativity. So, whether you’re regexing, listifying, or just having fun, sigils are a worthy addition to your coding journey. Go ahead, wield these spells with a smile, and watch your Elixir code transform from ordinary to enchanting ☺️.
