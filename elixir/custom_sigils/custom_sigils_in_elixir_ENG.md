# Sigil Sorcery: Custom Sigils in Elixir

![Custom Sigils in Elixir created by Dalle](<images/DALL·E 2024-01-09 08.12.02 - A mystical and enchanting image representing 'Sigil Sorcery_ Custom Sigils in Elixir and Phoenix', without a star in the background and emphasizing pu 2.png>)

## Table of Contents

- [Introduction: The Magic of Sigils](#introduction-the-magic-of-sigils)
- [Feeling creative? The process of creating your own sigils](#feeling-creative-the-process-of-creating-your-own-sigils)
  - [How to Build It: A Step-by-Step Recipe](#how-to-build-it-a-step-by-step-recipe)
  - [Examples of Custom Sigils without a modifier](#examples-of-custom-sigils-without-a-modifier)
    - [Example 1 without a modifier](#example-1-without-a-modifier)
    - [Example 2 without a modifier](#example-2-without-a-modifier)
  - [Examples of Custom Sigils with a modifier](#examples-of-custom-sigils-with-a-modifier)
    - [Example 1 with modifier](#example-1-with-modifier)
    - [Example 2 with modifier](#example-2-with-modifier)
- [Conclusion: Embrace the Sigil Sorcery](#conclusion-embrace-the-sigil-sorcery)

## Introduction: The Magic of Sigils

Elixir sigils are general-purpose tools for handling common data types. Examples of built-in sigils include strings `(~s)`, regular expressions `(~r)`, dates `(~D)`, and lists of words `(~w)`. But, as you might have guessed, Elixir's flexibility doesn't stop here; Elixir allows us to create custom sigils for specific needs, and that is what we’re doing now.

## Feeling creative? The process of creating your own sigils

Conceptually, a sigil is a function that takes arguments like functions do. Sigil function names follow a specific syntax, as we can see below (cf. step 1). Unlike in other functions, we are not limited to the usual delimiters () around the function's parameters but have 8 different delimiters we can freely choose from (cf. step 2). Finally, the parameters of a sigil function comprise the input string and a list of modifiers (cf. step 3).

### How to Build It: A Step-by-Step Recipe

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
What we do here is take the input string, upcase it, and reverse it.

```bash
import YourProject.SigilSorcery

iex(92)> ~UPREV(hello)
"OLLEH"
```

#### Example 2 without a modifier

```elixir
defmodule YourProject.EmojiSigil do
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

What we do here is take the input string, split it into a list of words, and replace each word with an emoji if it exists in our emoji map. If the word is not in our map, we return the word as is. Then, we return the list of emojis joined into a string.

```bash
import YourProject.EmojiSigil

iex(92)> ~EMO{star sun moon planet rocket}
"⭐ ☀️ 🌙 🪐 🚀"
iex(93)> ~EMO{hello star hello sun and moon}
"hello ⭐ hi ☀️ and see you 🌙"
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

What we do here is create three function clauses, each of which takes a string and a modifier. If the modifier is 'u', we upcase the string. If the modifier is 'l', we downcase the string. If the modifier is 'r', we reverse the string.

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

What we do here is take the input string, split it into a list of words, and replace each word with an emoji if it exists in our emoji map. If the word is not in our map, we return the word as is. Then we return the emoji repeated n times, where n is the modifier passed to the sigil function. Finally, we join the list of emojis into a string.

```bash
import YourProject.ZooSigil

iex(92)> ~ZOO(cat dog bird)3
"🐱🐱🐱 🐶🐶🐶 🐦🐦🐦"
```

## Conclusion: Embrace the Sigil Sorcery

Sigils are handy little tools; they’re a mix between optimization of efficiency and a playground for creativity. So, whether you’re regexing, listifying, or just having fun building your own sigils, sigils are a worthy addition to your coding journey. Go ahead, wield these spells with a smile, and watch your Elixir code transform from ordinary to enchanting 🧙🏼☺️.

### [top ⇧](#table-of-contents)
