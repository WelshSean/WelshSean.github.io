---
layout: post
title: Regular expression reminders for myself
category: regular expressions
tags: [regex]
---
I keep on finding that every time I come back to doing regexes in Python I am almost starting from scratch so decided to make some notes whilst going through  the regexes section of the Google [using python to interact with the operating system](https://www.coursera.org/learn/python-operating-system/home/welcome) course on Coursera.

[Regex101](https://regex101.com) is also useful for testing regexes

[Greedy vs non-greedy](https://docs.python.org/3/howto/regex.html#greedy-versus-non-greedy)

More practice at [regex crossword](https://regexcrossword.com/)
# Python
Always use Python raw strings (r"ddddqd").

# Basics
```python
>>> import re
>>> result=re.search(r"aza", "plaza")
>>> print(result)
<_sre.SRE_Match object; span=(2, 5), match='aza'>
>>>
```
If the regex doesn't match, we get a null object returned

```python
>>> result=re.search(r"ibazes", "plaza")
>>> print(result)
None
```

some more simple examples

```python
>>> print(re.search(r"d.g", "dogma"))
<_sre.SRE_Match object; span=(0, 3), match='dog'>
>>> print(re.search(r"d.g", "gundog"))
<_sre.SRE_Match object; span=(3, 6), match='dog'>
>>> print(re.search(r"d.g", "Gundog", re.IGNORECASE))
<_sre.SRE_Match object; span=(3, 6), match='dog'>
```

## Character Classes

```python
>>> print(re.search(r"[Gg]undog", "Gundog"))
<_sre.SRE_Match object; span=(0, 6), match='Gundog'>
>>> print(re.search(r"[a-z]way", "The end of the highway"))
<_sre.SRE_Match object; span=(18, 22), match='hway'>
>>> print(re.search(r"[a-z]way", "What a way to go"))
None
>>> print(re.search(r"cloud[a-zA-Z0-9]", "cloudY"))
<_sre.SRE_Match object; span=(0, 6), match='cloudY'>
```
^ acts like a not in a character class

```python
>>> print(re.search(r"[^a-zA-Z]", "this contains spaces"))
<_sre.SRE_Match object; span=(4, 5), match=' '>
>>> print(re.search(r"[^a-zA-Z ]", "this contains spaces"))
None
```

an \| acts like an or in regexes

```python
>>> print(re.search(r"dog|cat", "I like dogs"))
<_sre.SRE_Match object; span=(7, 10), match='dog'>
>>> print(re.search(r"dog|cat", "I like cats"))
<_sre.SRE_Match object; span=(7, 10), match='cat'>
>>> print(re.search(r"dog|cat", "I like cats and dogs"))
<_sre.SRE_Match object; span=(7, 10), match='cat'>
```

If we want to get all matches then we need to use the findall method

```python
>>> print(re.findall(r"dog|cat", "I like cats and dogs"))
['cat', 'dog']
```

## Repetition

```python
>>> print(re.search(r"Py.*n", "Pygmalion"))
<_sre.SRE_Match object; span=(0, 9), match='Pygmalion'>
>>> print(re.search(r"Py.*n", "Python programming"))
<_sre.SRE_Match object; span=(0, 17), match='Python programmin'>
>>> print(re.search(r"Py[a-zA-Z]*n", "Python programming"))
<_sre.SRE_Match object; span=(0, 6), match='Python'>
>>> print(re.search(r"o+l+", "goldfish"))
<_sre.SRE_Match object; span=(1, 3), match='ol'>
>>> print(re.search(r"o+l+", "woolly"))
<_sre.SRE_Match object; span=(1, 5), match='ooll'>
>>> print(re.search(r"p?each", "to each their own"))
<_sre.SRE_Match object; span=(3, 7), match='each'>
>>> print(re.search(r"p?each", "to peach their own"))
<_sre.SRE_Match object; span=(3, 8), match='peach'>
```

# Escaping

```python
>>> print(re.search(r"\.com", "mydomain.com"))
<_sre.SRE_Match object; span=(8, 12), match='.com'>
>>> print(re.search(r"\w", "this is my dog"))
<_sre.SRE_Match object; span=(0, 1), match='t'>
>>> print(re.search(r"\w*", "this is my dog"))
<_sre.SRE_Match object; span=(0, 4), match='this'>
>>> print(re.search(r"\w*", "this_is_my_dog"))
<_sre.SRE_Match object; span=(0, 14), match='this_is_my_dog'>
>>> print(re.search(r"\w\s\w", "this is my dog"))
<_sre.SRE_Match object; span=(3, 6), match='s i'>
```

# Capturing Groups

```python
>>> result = re.match(r"^(\w*), (\w*)$", "Lovelace, Ada")
>>> print(result)
<_sre.SRE_Match object; span=(0, 13), match='Lovelace, Ada'>
>>> print(result.groups())
('Lovelace', 'Ada')
>>> print(result[0])
Lovelace, Ada
>>> print(result[1])
Lovelace
>>> print(result[2])
Ada
>>> "{} {}".format(result[2], result[1])
'Ada Lovelace'
```


# Repetition Qualifiers

```python
>>> print(re.search(r"[A-Za-z]{5}", "a ghost"))
<_sre.SRE_Match object; span=(2, 7), match='ghost'>
>>> print(re.search(r"[A-Za-z]{5}", "a scary ghost appeared"))
<_sre.SRE_Match object; span=(2, 7), match='scary'>
>>> print(re.findall(r"[A-Za-z]{5}", "a scary ghost appeared"))
['scary', 'ghost', 'appea']
>>> print(re.findall(r"\b[A-Za-z]{5}\b", "a scary ghost appeared"))
['scary', 'ghost']
>>> print(re.findall(r"\w{5,10}", "I really like strawberries"))
['really', 'strawberri']
>>> print(re.findall(r"\w{5,}", "I really like strawberries"))
['really', 'strawberries']
>>> print(re.findall(r"s\w{,20}", "I really like strawberries"))
['strawberries']
```

# Splitting and Replacing

```python
>>> re.split(r"[.?!]","One sentence. Another sentence! And the last one?")
['One sentence', ' Another sentence', ' And the last one', '']
>>> re.split(r"([.?!])","One sentence. Another sentence! And the last one?")
['One sentence', '.', ' Another sentence', '!', ' And the last one', '?', '']
>>> re.sub(r"[\w.%+-]+@[\w._-]+","[REDACTED]", "Received an email for go_nets95@mydomain.com")
'Received an email for [REDACTED]'
>>> re.sub(r"^([\w .-]*), ([\w .-]*)$", r"\2 \1", "Lovelace, Ada")
'Ada Lovelace'
```
