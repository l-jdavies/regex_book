# Using regex in Ruby

Oddly, the `Regexp` (Ruby) and `RegExp` (JavaScript) classes don't provide the regex methods you'll use most often. Instead, the `String` class does.

## Matching strings

We've already seen match in some of our examples. This method returns a value that indicates whether a match occurred, and what substrings matched. This return value is "truthy"; you can test it in a conditional expression in either Ruby or JavaScript to determine whether a given string matched a regex. At its most basic, we use it like this:

```ruby
fetch_url(text) if text.match(/\Ahttps?:\/\/\S+\z/)
```

We won't discuss the return value of `match` in detail -- see the documentation instead. For now, match returns an Array that contains the string we matched against, along with the capture groups defined in the regex. If we name this Array `capture`, then `capture[0]` represents the entire matched portion of text, while `capture[1]`, `capture[2]`, etc. correspond to the capture groups. (We discuss capture groups below.). If the regex doesn't match text, then Ruby returns `nil`, while JavaScript returns `null`.

In Ruby, the return value of match isn't an Array, but a `MatchData` object that responds to `[0]`, `[1`, `[2]`, and so on. You cannot apply most Array methods to this object directly.

## Splitting strings

Applications that process text often must analyze data comprised of records and fields delimited by some special characters or delimiters. A typical format has records separated by newlines, and fields delineated by tabs. Such data often needs parsing before you can use it in your program; the `split` method is an often-useful parsing tool.

`split` is frequently used with a simple string as a delimiter:

```ruby
record = "xyzzy\t3456\t334\tabc"
fields = record.split("\t")
# -> ['xyzzy', '3456', '334', 'abc']
```

```ruby
record = "xyzzy  3456  \t  334\t\t\tabc"
fields = record.split(/\s+/)
# -> ['xyzzy', '3456', '334', 'abc']
```

## Capture groups

Before moving on to the final methods in our whirlwind tour, we need to first talk about **capture groups**. (Note that regex also have **non-capture groups** but we won't cover them here.) You've already encountered these before, though we called them something different at the time: grouping parentheses. We didn't mention it at the time, but these meta-characters have another function: they provide capture and non-capture groups.

Capture groups capture the matching characters that correspond to part of a regex. You can reuse these matches later in the same regex, and when constructing new values based on the matched string.

We'll start with a simple example. Suppose you need to match quoted strings inside some text, where either single or double quotes delimit the strings. How would you do that using the regex patterns you know? You might consider:

```
/['"].+?['"]/

```

as your first attempt to match quotes, but, you'll soon find that it also matches mixed single and double quotes. This may not be what you want. Instead, you need a way to capture the opening quote and reuse that character for the closing quote. It's time to call on capture groups:

```
/(['"]).+?\1/

```

Here the group captures the part of the string that matches the pattern between parentheses; in this case, either a single or double quote. We then match one or more of any other character and end with a `\1`: we call this sequence a **backreference** - it references the first capture group in the regex. If the first group matches a double quote, then `\1` matches a double quote, but not a single quote.

A regex may contain multiple capture groups, numbers from left to right as groups 1, 2, 3, and so on, up to 9. As you might expect, the backreferences are `\1`, `\2`, `\3`, ..., and `\9`.

## Transformations in Ruby

Transforming a string with regex involves matching that string against the regex, and using the results to construct a new value. In Ruby, we typically use `String#sub` and `String#gsub`. `#sub` transforms the first part of a string that matches a regex, while `#gsub` transforms every part of a string that matches.

We can use backreferences in the replacement string (the second argument):

```ruby
text = %(We read "War of the Worlds".)
puts text.sub(/(['"]).+\1/, '\1The Time Machine\1')
# prints: We read "The Time Machine".

```

One thing to note here is that if you double quote the replacement string, you need to double up on the backslashes:

```ruby
puts text.sub(/(['"]).+\1/, "\\1The Time Machine\\1")

```

When possible, try to use single quotes to avoid **leaning toothpick syndrome**.