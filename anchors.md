# Anchors

Anchors provide a way to limit how a regex matches a particular string by telling the regex engine where matches can begin and where they can end.

Anchors are a bit strange in the world of regex; they don't match any characters. What they do is ensure that a regex matches a string at a specific place: the beginning or end of the string or end of a line, or on a word or non-word boundary.

## Start/End of line

The `^` and `$` meta-characters are anchors that fix a regex match to the beginning (`^`) or ending (`$`) of a line of text. In Ruby, there's some subtlety to that definition which we will circle back to in the next subsection; for now, though, you can think of it as meaning that `^` and `$` anchor a regex to the beginning or end of a string.

Try this regex, `/^cat/` against these strings:

```ruby
cat
catastrophe
wildcat
I love my cat
<cat>
```

`cat` is highlighted in Rubular in the first two strings. This example demonstrates that `^` forces the cat pattern to match at the beginning of each line.

`/cat$/` against the same strings matches the first, third and fourth lines.

`/^cat$/` will highlight the first line only.

## Lines vs Strings

As we mentioned above, there's some subtlety involved with how `^` and `$` work in Ruby. This subtlety arises when the string you are attempting to match contains one or more newline characters that aren't the last character in the string. For example, consider this code:

```ruby
TEXT1 = "red fish\nblue fish"
puts "matched red" if TEXT1.match(/^red/)
# matched red

puts "matched blue" if TEXT1.match(/^blue/)
# matched blue
```

Both `matched red` and `matched blue` since `^` anchors the regex to the beginning of each line in the string, not the beginning of the string. For Ruby's purposes, each new line occurs after a `\n` character, with the beginning of the string marking the beginning of the first line. The line runs through - and includes - the next `\n` character. If no more `\n` characters are available, the last line runs through to the end of the string.

Same principle applies for `$` :

```ruby
TEXT2 = "red fish\nred shirt"
puts "matched fish" if TEXT2.match(/fish$/)
# matched fish

puts "matched shirt" if TEXT2.match(/shirt$/)
# matched shirt
```

Note in particular that even though the first line in the string ends with a `\n`, `fish` is still said to occur at the end of the line. `$` doesn't care if there is a `\n` character at the end, provided there is no more than one.

## Start/End of String

It's not too often that you'll encounter situations where you need to match multi-line strings as shown in the previous sub-section, but they do arise. More often, though, you must match at the beginning or end of the string, not the line. For these matches, use the `\A`, `\Z`, and `\z` anchors (note that there is no `\a` anchor).

The `\A` anchor ensures that a regex matches at the beginning of the string, while `\Z` and `\z` match at the end of the string. The difference between `\Z` and `\z` is somewhat subtle and seldom of concern: `\z` always matches at the end of a string, while `\Z` matches up to, but not including, a newline at the end of the string. As a rule, use `\z` until you determine that you need `\Z`.

```ruby
TEXT3 = "red fish\nblue fish"
TEXT4 = "red fish\nred shirt"

puts "matched red" if TEXT3.match(/\Ared/)
# matched red

puts "matched blue" if TEXT3.match(/\Ablue/)
# => nil

puts "matched fish" if TEXT4.match(/fish\z/)
# => nil

puts "matched shirt" if TEXT4.match(/shirt\z/)
# matched shirt
```

## Word boundaries

The last two anchors anchor regex matches to word boundaries (`\b`) and non-word boundaries (`\B`). For these anchors, words are sequences of word characters (`\w`), while non-words are sequences of non-word characters (`\W`). A word boundary occurs:

- between any pair of characters, one of which is a word character and one which is not.
- at the beginning of a string if the first character is a word character.
- at the end of a string if the last character is a word character.

A non-word boundary matches any place else:

- between any pair of characters, both of which are word characters or both of which are not word characters.
- at the beginning of a string if the first character is a non-word character.
- at the end of a string if the last character is a non-word character.

For instance:

```
Eat some food.
```

Here, word boundaries occur before the `E`, `s`, and `f` at the start of the three words, and after the `t`, `e`, and `d` at their ends. Non-word boundaries occur elsewhere, such as between the `o` and `m` in `some`, and following the `.` at the end of the sentence.

To anchor a regex to a word boundary, use the `\b` pattern. For example, to match 3 letter words consisting of "word characters", you can use `/\b\w\w\w\b/`. Try it with:

```ruby
One fish,
Two fish,
Red fish,
Blue fish.
123 456 7890
```

It's rare that you must use the non-word boundary anchor, `\B`. Here's a somewhat contrived example you can try. Try the regex `/\Bjohn/i` against these strings:

```ruby
John Silver
Randy Johnson
Duke Pettijohn
Joe_Johnson
```

The regex matches `john` in the last two strings, but not the first two.

`\b` and `\B` do not work as word boundaries inside of character classes (between square brackets). In fact, `\b` means something else entirely when inside square brackets: it matches a backspace character.
