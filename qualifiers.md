# Qualifiers

## Zero or more

We've seen that regex let you concatenate multiple patterns that can match sequences of characters. Sometimes, you want to repeat a pattern. For instance, to match all sequences of three digits, you can use the regex `/\b\d\d\d\b/` - here we repeat the `\d` shortcut for three times.

Suppose, though, you need to find all sequences of three or more digits. How would you code this? You might try something like:

```
/\b(\d\d\d\d\d\d|\d\d\d\d\d|\d\d\d\d|\d\d\d)\b/

```

but this matches 3-6 digits, not three or more, and it's already hard to read. You could keep adding additional sequences of `\d`s until you reach some maximum level, but it is limiting and may cause problems in the future.

You could try matching `/\d\d\d/`. It will certainly get all three-digit and longer numbers, but it mixes in results like `XY321Z`.

Regex engines provide a variety of quantifiers that you can use to match sequences. The quantifier that gets used most frequently is `*` ; it matches zero or more occurrences of the pattern to its left. For example, try `/\b\d\d\d\d*\b/` against these strings in Rubular:

```ruby
Four and 20 black birds
365 days in a year, 100 years in a century.
My phone number is 222-555-1212.
My serial number is 345678912.
```

Highlights:

`365`, `100`, `222`, `555`, `1212` , `345678912` .

The way you read that regex is that you want to match three consecutive digits beginning at a word boundary, followed by any number of digits, and then another word boundary. The engine reads the regex as six sub-patterns:

[Untitled](https://www.notion.so/aa60953fb5c944d3b2b255f1cf80849a)

One thing to watch out for is that "zero or more" truly means zero or more. The regex `/x*/` matches every string, even an empty string, or a string that contains no `x`s anywhere. If you try this pattern in Rubular, it matches between every character.

When talking about regular expressions that match zero-length strings, imagine an arrow that starts out pointing to the beginning of the string, prior to the first character. When the regex engine goes to work, it moves this imaginary arrow to the right one character at a time until it either finds a match or determines that there is no match. The arrow never points directly at a character, but always points between each pair of characters, and matches typically occur against the character to the right of the arrow. (There are a few exceptions that match the character to the left as well, such as `\b`.)

When you try to match `/x/` for instance, the regex engine looks to the character to the right of the arrow position. If it sees an `x`, it matches. Otherwise, it advances the arrow one position to the right, and again tries to match starting with the next character.

This is why something like `/x*/` matches wherever in the string you're at - with the arrow pointing between characters, the regex is free to say "Nope. There are no `x`'s between me and the next character, so it's a match."

Another way to see this is to try the regex `/co*t/` against these strings:

```ruby
ct
cot
coot
cooot
```

The regex matches every one of these strings, including the one without the letter `o`.

Note that the quantifier always applies to one pattern; the pattern it finds to the left of the quantifier. If necessary, you can use grouping parentheses to define the pattern to which you want to apply the `*`. For instance, try `/1(234)*5/` against:

```ruby
15
12345
12342342345
1234235
```

You should see that the engine treats `(234)` as a single pattern, so the regex matches anywhere zero or more occurrences of `234` separate `1` and `5`.

## One or more

The `+` quantifier is nearly identical to the `*` quantifier, but, instead of matching zero or more occurrences of something, it matches one or more occurrences of that thing.

## Zero or One

Sometimes, you need an optional pattern in a regex; that is, a pattern that either occurs once or doesn't occur at all. For these situations, you need the `?` quantifier. As with `*` and `+`, `?` applies to the pattern to its left.

Suppose you need to test whether a string contains the words `cot` or `coot`, but don't want to match against `ct` or `cooot`. In this case, you can use `/coo?t/`, which matches a `c` followed by an `o` followed by an optional `o` followed by a `t`.

One place you might use a `?` would be a pattern where you are trying to match a date whose components may or may not include - separator characters. For instance, you have dates formatted as both `20170111` or `2017-01-11`. To match such dates, you can use the regex `/\b\d\d\d\d-?\d\d-?\d\d\b/`. This matches:

```ruby
20170111
2017-01-11
2017-0111
201701-11
```

but not `2017/01/11`

Note that `?` has the same behavior subtlety as `*`; it matches zero occurrences. Thus, `/h?/` matches each of these strings:

```ruby
his
is
ish
```

## Range

The `*`,  `+`, and `?` quantifiers match repeated sequences. They may provide all the regex functionality you need. However, sometimes you need to specify the repeat count more precisely. For example, you may want to test a phone number to see if contains precisely ten digits, or perhaps you want to look at all words that contain at least seven characters, or you want words that are 5-8 characters long. It's possible to do all this with the patterns and quantifiers you've already learned, but it will be tedious and messy. That's where the range quantifier comes in.

The range quantifier consists of a pair of curly braces, `{}`, with one or two numbers and an optional comma between the braces:

- `p{m}` matches precisely `m` occurrences of the pattern `p`.
- `p{m,}` matches `m` or more occurrences of `p`.
- `p{m,n}` matches `m` or more occurrences of `p`, but not more than `n`.

Let's go through the examples we talked about above.

If you need to test a string to see if it contains precisely ten consecutive digits (perhaps it represents a US-style phone number), you can try it with the regex `/\b\d{10}\b/` and these strings:

```ruby
2225551212 1234567890 123456789 12345678900
```

You should see that this regex matches the first two numbers: they have ten digits each.

To match numbers that are at least three digits in length, we can use `/\b\d{3,}\b/`. Try it with these strings:

```ruby
Four and 20 black birds
365 days in a year, 100 years in a century.
My phone number is 222-555-1212.
My serial number is 345678912.
```

If you want to match words of 5-8 letters, use `/\b[a-z]{5,8}\b/i`:

```ruby
Bizarre
a
one two three four five six seven eight nine
sensitive
dropouts 
```

## Greediness

The quantifiers we've discussed thus far are **greedy**: they always match the longest possible string they can. For instance, try matching `/a[abc]*c/` against `xabcbcbacy`. You should see that this pattern matches `abcbcbac`, not `abc` or `abcbc` both of which could match the pattern, but are shorter than the final match string. This aspect of regex isn't often a concern, but when it is, it can be highly confusing if you aren't familiar with greediness.

In most cases, greediness is what you want. However, sometimes it isn't, and you need to match the fewest number of characters possible; we call this a **lazy** match. In Ruby and JavaScript, you can request a lazy match by adding a `?` after the main quantifier. For example, `/a[abc]*?c/` matches `abc` and `ac` in `xabcbcbacy`.

Link for lazy vs greedy: [https://d186loudes4jlv.cloudfront.net/regex/files/greedy-vs-lazy.pdf](https://d186loudes4jlv.cloudfront.net/regex/files/greedy-vs-lazy.pdf)