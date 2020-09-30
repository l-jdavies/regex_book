# Character class shortcuts

As we saw in the previous chapter, character class patterns match against a set of characters. We use these simple patterns a lot; in fact, you'll find that nearly every regex you use contains at least one character class pattern. It may not look like the character classes you have already seen, though; we use some character classes so often that most regex engines have built-in shortcuts. 

## Any character

The most commonly needed character class is the class that represents any character; if you don't care whether a character is alphanumeric, punctuation, whitespace, some control character, or something else entirely, you need a character class that represents any character. That's where the `.` (a period) meta-character comes in. By default, `/./` does not match newline characters, which is useful: you typically don't want them. Use the `/m` (multiline) option when `.` should match newlines.

## Whitespace

Two additional character class shortcuts that are frequently needed are `\s` for whitespace characters, and `\S` for non-whitespace characters. By definition, the whitespace characters are the space (' '), tab ('\t'), vertical tab ('\v'), carriage return ('\r'), line feed ('\n'), and form feed ('\f'). Thus, `/\s/` is equivalent to `/[ \t\v\r\n\f]/`, while `/\S/` is equivalent to `/[^ \t\v\r\n\f]/`.

```ruby
puts 'matched 1' if 'Four score'.match(/\s/)
# matched 1

puts 'matched 2' if "Four\tscore".match(/\s/)
# matched 2

puts 'matched 3' if "Four-score\n".match(/\s/)
# matched 3

puts 'matched 4' if "Four-score".match(/\s/)
# nil
```

```ruby
puts 'matched 1' if 'a b'.match(/\S/)
# matched 1

puts 'matched 2' if " \t\n\r\f\v".match(/\S/)
# nil
```

You can use `\s` and `\S` both in and out of square brackets. Outside square brackets, e.g., `/\s/`, it stands for one of the whitespace characters. Inside square brackets, e.g., `/[a-z\s]/` , they represent an alternative to the other members of the class. Here, for instance, it represents any lowercase alphabetic character or any whitespace character.

## Digits and Hex Digits

The decimal digits, 0-9, and the hexadecimal digits, `0-9`, `A-F`, and `a-f`, also show up often in character classes, so we have shortcuts for them:

[Untitled](Character%20class%20shortcuts%2091b621481abe401d9bf2e89eb75c51a6/Untitled%20Database%2066ee9ef0716d42f5b0901cac35b3cbf6.csv)

## Word characters

This last pair of shortcuts may be confusing. `/\w/` matches "word characters", while `/\W/` matches "non-word characters". At first glance, most people assume that word characters are alphabetic. In fact, the definition of word characters for `\w` is a bit broader than that; they include all alphabetic characters (a-z, A-Z), all decimal digits (0-9), and, oddly, an underscore (_). Avoid trouble and commit this to memory.