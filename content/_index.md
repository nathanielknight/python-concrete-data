<DIV class="slide cover">

# Making data concrete with NamedTuples and DataClasses

## Nat Knight, Green Knight Software Development

</DIV>
<DIV class="slide">

- Independent Software Developer
- He/Him
- Mostly Python
- Different fields
  - Web
  - Bioinformatics
  - Cloud computing
  - Finance
  - Atmospheric Science

</DIV>
<DIV class="slide">

# The plan

- Motivating example
- `collections.namedtuple`
- `typing.NamedTuple`
- `dataclsses.dataclass`
- Sources

</DIV>
<DIV class="slide">

# What kind of problem are we solving?

Analysing a sequence of structured records

- List of dictionaries
- `csv.DictReader`
- Sequence of JSON objects.
- etc.

</DIV>
<DIV class="slide">

# As a concrete example, consider Cats

Cat features:

- Name
- Mass in Kg
- Hair length
- List of attributes

</DIV>
<DIV class="slide">

# Example processing step

Determine if cat is round:

```
def is_round(cat):
    if cat["hair_length"] == "long":
        return cat["mass"] > 6.0
    else:
        return cat["mass"] > 8.0
```

</DIV>
<DIV class="slide">

# Example loading step

Get cats to count

```
import csv

with open("cats.csv") as inputfile:
    cats = list(csv.DictReader(inputfile))
```

</DIV>
<DIV class="slide">

# Example analysis

```
import csv

def is_round(cat):
    if cat["hair_length"] == "long":
        return cat["mass"] > 6.0
    else:
        return cat["mass"] > 8.0


with open("cats.csv") as inputfile:
    cats = list(csv.DictReader(inputfile))

round_cats = 0
for cat in cats:
    if is_round(cat):
        round_cats = round_cats + 1

print("{} cats are round".format(round_cats))
```

</DIV>
<DIV class="slide">

# The abstract approach

Lists or sequences of dictionaries:

Advantages:

- Supports in just about every version of Python
- Doesn't necessarily break if data changes

Disadvantages:

- Opaque; unclear what data looks like
- Assumptions about data are implicit

</DIV>
<DIV class="slide">

# Processing with `collections.namedtuple`

```python
import collections

Cat = collections.namedtuple(
    "Cat",
    ['name', 'mass', 'hair_length', 'attributes'],
)

def is_round(cat):
    if cat.hair_length == "long":
        return cat.mass > 6.0
    else:
        return cat.mass > 8.0
```

</DIV>
<DIV class="slide">

# Loading data with a `collections.namedtuple`

```python
import csv

with open("cats.csv") as inputfile:
    dicts = csv.DictReader(inputfile)
    cats = [Cat(**dct) for dct in dicts]
```

</DIV>
<DIV class="slide">

```
import collections
import csv

Cat = collections.namedtuple(
    "Cat",
    ['name', 'mass', 'hair_length', 'attributes'],
)

def is_round(cat):
    if cat.hair_length == "long":
        return cat.mass > 6.0
    else:
        return cat.mass > 8.0

with open("cats.csv") as inf:
    dicts = csv.DictReader(inputfile)
    cats = [Cat(**xs) for xs in dicts]

round_cats = len([c for c in cats if is_round(c)])
print("{} round cats".format(round_cats))
```

</DIV>
<DIV class="slide">

# `collections.namedtuple`

Supported since Python 2.6

Advantages:

- Immutable by default
- Field names are explicit

Disadvantages:

- Breaks if data changes
- Inheritance support can be problematic

</DIV>
<DIV class="slide">

# `typing.NamedTuple`

```python
import typing

class Cat(typing.NamedTuple):
    name: str
    hair_length: str
    mass: float
    attributes: str

# NOTE: Types have no effect at runtime
```

</DIV>
<DIV class="slide">

# Processing with a `typing.NamedTuple`

```python
import typing

class Cat(typing.NamedTuple):

    name: str
    hair_length: str
    mass: float
    attributes: str

    def is_round(self):
        if self.hair_length == "long":
            return self.mass > 6.0
        else:
            return self.mass > 8.0
```

</DIV>
<DIV class="slide">

# Loading with a `typing.NamedTuple`

```python
import csv

with open("cats.csv") as inputfile:
    dicts = csv.DictReader(inputfile)
    cats = [Cat(**dct) for dct in dicts]
    round_cats = [c for c in cats if c.is_round()]
```

</DIV>
<DIV class="slide">

# `typing.NamedTuple`

In addition to the features of a `collections.namedtuple`:

Advantages:

- More readable

Disadvantages:

- Only supported since Python 3.6

</DIV>
<DIV class="slide">

# `dataclasses.dataclass`

- New in Python 3.7
- Generate methods on a class so you don't have to write them
  - `__init__`
  - `__eq__`
  - `__repr__`
  - etc.

</DIV>
<DIV class="slide">

# Processing with a `dataclasses.dataclass`

```python
import dataclasses

@dataclasses.dataclass
class Cat:
    name: str
    hair_length: str
    mass: float
    attributes: str

    def is_round(self):
        if self.hair_length == "long":
            return self.mass > 6.0
        else:
            return self.mass > 8.0

    def __post_init__(self):
        assert self.hair_length in {"short", "long"}
```

</DIV>
<DIV class="slide">

# Using inheritance with `dataclasses.dataclass`

```python
import dataclasses

@dataclasses.dataclass
class Animal:
    name: str
    mass: float

@dataclasses.dataclass
class Cat(Animal):
    hair_length: str
    attributes: str

    def is_round(self):
        is self.hair_length = "long":
            return self.mass > 6.0
        else:
            return self.mass > 8.0
```

</DIV>
<DIV class="slide">

# `dataclasses.dataclass`

Use case overlaps substantially with named tuples, but:

Advantages:

- Inheritance works reliably

Disadvantages:

- Not immutable by default (but can be frozen)

</DIV>
<DIV class="slide">

# Sources

- [`collections.namedtuple`](https://docs.python.org/3/library/collections.html#collections.namedtuple)
- [`typing.NamedTuple`](https://docs.python.org/3/library/typing.html#typing.NamedTuple)
- [`dataclasses`](https://docs.python.org/3/library/dataclasses.html)
- [Named tuples vs. Data classes on StackOverflow](https://stackoverflow.com/questions/51671699/data-classes-vs-typing-namedtuple-primary-use-cases)
- [Raymond Hettinger - Dataclasses: The code generator to end all code generators - PyCon 2018](https://www.youtube.com/watch?v=T-TwcmT6Rcw)

Get the slides at

    gksd.link/python-concrete-data

Source at

    github.com/neganp/python-concrete-data

A video of the talk is available on [YouTube](https://www.youtube.com/watch?v=UEWp44o_818).

</DIV>
<DIV class="slide">
# Q & A

After the talk, I spoke to some folks at the meetup and answered questions.
Here's a selection of what we talked about.

## What are the performance implications of Named Tuples and Data Classes?

As with all performance advice,
I'll preface this by saying that
performance is a tricky, fickle subject
and the solution to performance problems
is almost always to profile your code,
see what's slow,
and focus on that.

According to their documentation, named tuples should as memory efficient
as a tuple, which [should be more efficient than a class](https://stackoverflow.com/questions/45123238/python-class-vs-tuple-huge-memory-overhead),
but using [`__slots__`](https://docs.python.org/3.7/reference/datamodel.html#slots)
to reduce your class' memory requirements
might be as good or better.

In terms of accessing fields,
[named tuples are slower than classes](https://stackoverflow.com/questions/31199650/why-is-accessing-a-namedtuple-by-field-name-slower-than-accessing-a-classs-memb).

Seriously though, neither tool
should have a huge impact on performance,
so if you're striving to make your code
as performant as possible
you should probably focus on other things.

## How do Named Tuples and Data Classes compare to [Frictionless Data][frictionless]?

[frictionless]: https://frictionlessdata.io/

I hadn't heard of Frictionless Data before. It's a collection of specifications
and tools whose objective is to make it easy to send data between different
programs, systems, and languages.

Frictionless data is a much, much bigger tool that would impact your whole
data collection and analysis project. Named tuples and data classes are much
smaller, more ad-hoc things that just make your Python code a little more
manageable.

## What are some strategies for a data pipeline that adds fields to the data that flows through it?

Named tuples are immutable, and they don't subclass particularly well, so in my experience the
best way to add fields to them is either to embed them in a larger data-structure or create a new
named tuple with the new fields and pass that on instead.

Data classes subclass nicely, which could be a good way to add fields.

In both cases, adding a new field is more work than adding a field to a dictionary, so if you
have data that needs to grow as it flows through the pipeline, you might prefer to stick with
good old dictionaries.

## Where should code for serializing/de-serializing data in these formats live?

As long as the values in their fields are JSON friendly, named tuples transform
beautifully to and from JSON. You can use
[`namedtuple._asdict`](https://docs.python.org/3/library/collections.html#collections.somenamedtuple._asdict)
to get the data into whatever JSON-generating function you're using
(like [`json.dumps`](https://docs.python.org/3.7/library/json.html#json.dumps)), and you can de-serialize
it using your named tuple and
["kwargs expansion"](https://docs.python.org/dev/tutorial/controlflow.html#more-on-defining-functions)
to keep the code concise.

Data classes are just classes, so in their case I'd stick to whatever strategy you use for regular classes.

</DIV>
