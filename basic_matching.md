# Basic matching

In Ruby we can use the `match` method and pass in regex.

To match a specific alphanumeric character we place the pattern between two slashes `/<pattern>/` 

```ruby
str = 'cast'
print "matched 's'" if str.match(/s/)
print "matched 'x'" if str.match(/x/)
```

## Meta-characters

The following characters are meta-characters and need to be escaped if you want to use them literally:

```ruby
$ ^ * + ? . ( ) [ ] { } | \ /
```

For example, to match `?` in these strings:

```ruby
str = '?'
str1 = "What's up, doc?"
str3 = 'Silence!'
str4 = "What's that?"

str1.match(/\?/)  # Regexp#match method
# => #<MatchData "?">

"What's up, doc?".match(/\?/) # String#match method
# => #<MatchData "?">
```

## Concatentation

It's possible to concatenate two or more patterns. The regex `/cat/`, for instance, consists of the concatenation of the `c`, `a`, and `t` patterns, and matches any string that contains a `c` followed by an `a` followed by a `t`.

The fact that we use a fancy name like concatenation should give you a hint that more is going on here than meets the eye. The patterns we concatenated are simple; they each match a single, specific character. We aren't limited to these simple patterns though; in fact, you can concatenate any pattern to another to produce a larger regex. There are no practical limits to the number of concatenations you perform other than the physical limitations of your hardware.

This fundamental idea is one of the more important concepts behind regex; patterns are the building blocks of regex, not characters or strings. You can construct complex regex by concatenating a series of patterns, and you can analyze a complex regex by breaking it down into its component patterns.

## Alternation

The `|` symbol can be used to match one of several sub-patterns, it functions as 'or':

```ruby
"The lazy cat.".match(/cat|dog/)
#=> MatchData "cat"

"The dog barks.".match(/cat|dog/)
# => MatchData "dog"
```

## Control character escapes

Most modern computing languages use control character escapes in strings to represent characters that don't have a visual representation.

`\n` = line feed

`\r` = carriage return

`\t` = tab

```ruby
text = "\t my tabbed text"
puts "has tab" if text.match(/\t/)
# has tab
```

## Ignoring case

As default, regex is case sensitive. You can change this default behaviour by adding `i` after the closing `/`

```ruby
"I love Launch School!".match(/launch/i)
# MatchData "launch"

"LAUNCH SCHOOL! Gotta love it!".match(/launch/i)
# MatchData "LAUNCH"
"launchschool.com".match(/launch/i)
# MatchData "launch"
```