<DIV class="slide cover">

# Making data concrete with NamedTuples and DataClasses

## Nat Knight

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
- `csv.DictRedaer`
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

# `collections.namedtuple`

Advantages:

- Immutable by default
- Field names are explicit
- Supported since Python 2.6

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
```

</DIV>
<DIV class="slide">

# Processing with a `typeing.NamedTuple`

```python
import typing

class Cat(typing.NamedTuple):

    # NOTE: doesn't actually check types at run-time

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
  </DIV>
