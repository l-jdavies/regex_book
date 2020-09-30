# Character classes

Character classes are patterns that let you specify a set of characters that you want to match.

## Set of characters

Character class patterns use a list of characters between square brackets, e.g., `/[abc]/`. Such a pattern matches a single occurrence of any of the characters between the brackets.

```ruby
str = "Four score + seven"

str.match(/[FX]/)
# => MatchData "F"

str.match(/[e+]/)
# => MatchData "e"

str.match(/[abAB]/)
# => nil

str.match(/[*+]/)
# => MatchData "+"
```

Character class patterns come in handy in all kinds of situations. For example, if a program wants a user to choose between five different options by entering a number between 1 and 5, you can validate that input with the regex `/[12345]/`. Likewise, you can validate a `y/n` prompt response with `/[nyNY]/`

Single-character classes (e.g., `/[a]/`) are possible and even useful, though we won't get into that here. Don't automatically remove the brackets if you encounter one in code you're working on: it's probably there for a reason.

Character classes also come in handy when you need to check for uppercase and lowercase letters, but can't use the `i` flag to make the entire regex case insensitive. For example, `/[Hh]oover/` matches `Hoover` or `hoover`, but not `HOOVER`. 

When writing character classes, it's good practice to group characters by type: digits, uppercase letters, lowercase letters, whitespace, and non-alphanumeric characters. You can arrange the groups in any order, though typically the non-alphanumerics come first or last in the character class. This practice aids readability.

Recall that you can concatenate any patterns, and that includes character classes. We did so earlier with `/[Hh]oover/`. You can also concatenate character classes. The regex `/[abc][12]/` matches any two characters where the first character is an `a`, `b`, or `c`, and the second is a `1` or a `2`.

```ruby
"a2".match(/[abc][12]/)
# => MatchData "a2"

"Model 640c1".match(/[abc][12]/)
# => MatchData "c1"

"a1 a2 a3 b1 b2 b3 c1 c2 c3 d1 d2 d3".match(/[abc][12]/)
# => MatchData "a1"
```

Note that `"a1 a2 a3 b1 b2 b3 c1 c2 c3 d1 d2 d3".match(/[abc][12]/)` only returns the first match (`a2` `b1` `b2` `c1` `c2` ) are also matches.

Earlier, we used both `*` and `+` in our character classes; this deserves a bit of explanation. Recall that we said that `*` and `+` are meta-characters, and require a backslash-escape to retain their literal meaning? Well, we told a small lie. In fact, the number of meta-characters dwindles to a handful inside a character class: `^ \ -` 

You can escape any of the special characters, even if you don't have to. Thus, `/[\*\+]/`  is an acceptable, albeit less readable, equivalent to `/[*+]/`.

## Range of characters

Sometimes, you'll find that your character class is a natural sequence of characters, such as the letters `a` through `z`. You can abbreviate these ranges inside character classes by specifying the starting character, a hyphen (`-`), and the last character. Thus, `/[a-z]/` matches any lowercase alphabetic character, `/[j-p]/` limits that to the letters `j` through `p`, while `/[0-9]/` matches any decimal digit. You can even combine ranges; suppose you need to match hexadecimal digits. If so, the following method could come in handy:

```ruby
def hex_digit?(char)
  char.match(/[0-9A-Fa-f]/)
end
```

While it is possible to construct ranges that cover non-alphanumeric characters, do not do this. Stick to the alphanumeric characters. Also, don't try to combine lowercase and uppercase alphabetic characters in a single range: /[A-z]/ does not do what you probably think it does.

## Negated classes

Another useful feature of character class ranges is range negation. Negations look like ordinary character classes, except the first character between the brackets is a caret (`^`). The negated class matches all characters not identified in the range.

At its simplest, you can have a negated character range for one character. For example, try `/[^y]/` with these strings:

```ruby
yes
a
by
+/-
ABCXYZ
y
yyyyy
yyayy
```

As you can see, Rubular highlights everything in these strings except the `y` characters.

More generally, you can negate multiple characters. For instance, the pattern `/[^aeiou]/` matches any character but `a`, `e`, `i`, `o`, or `u`. Importantly, this example shows that any character means precisely that. Rubular highlights all the uppercase letters, lowercase consonants, numerics, spaces, and punctuation. It highlights everything but the lowercase vowels. Don't forget this, or you may one day end up learning a lesson the hard way.

```ruby
text = 'xyx'
puts 'matched' if text.match(/[^x]/)
# matched
```

Why do this output `matched`? Rubular (and Scriptular) show you which characters match each regex; what it doesn't show explicitly is that match returns a truthy value when there is a match anywhere in the string. Though Rubular shows `/[^x]/` matching the `y` in `xyx` and nothing else, `text.match` is still truthy.