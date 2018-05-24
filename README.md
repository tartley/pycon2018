% PyCon 2018
% Jonathan Hartley
% May 2018

## PyCon 2018

Cleveland, Ohio.

::: notes

The conference took place last week, in Cleveland, OH.
Attendance was about 3,300.
So it's a smaller conference than languages like Java or C#,
but in keeping as a conference run by the community, for the community,
it tends to make up for that
by having fewer commercially oriented sales pitches,
being instead more dominated by enthusiastic practitioners.
So it's more of a programmer's conference.

If there's anyone listening who isn't familiar with Python,
you can get a taste of it here, at the shortest language overview
I've ever seen:
https://learnxinyminutes.com/docs/python3/

:::

## Python 3.7

Currently in beta, expected final release in June.

::: notes

One big topic of the conference was the imminent Python 3.7 release.
It's currently in final beta, with expected final release in June.

This release adds significant improvement to type annotations,
and introduces dataclasses,
both of which I'll say more about in a minute,

See the many improvements and fixes at

:::

https://docs.python.org/3.7/whatsnew/3.7.html

## Type Annotations

::: notes

_Type annotations_ aren't new to 3.7 - they are a language feature
that has been gradually phased into the language over the last few years.
They receive further improvements and fixes in 3.7, widespread adoption,
and were a central focus of this year's conference in ways they haven't
been previously.

Python has always been strongly typed,
although those types are both _implicit_,
rather than declared explicitly in code,
and they are _dynamic_,
meaning that variables, including function parameters and return values,
can refer to different types over time.

Type annotation lets users voluntarily abandon these features,
in favour of types that are both _explicit_ and _static_.

For example, to declare the type of a variable:

:::

## Type Annotations

To declare a variable's type:

``` python
x : str = "hello"
```

## Type Annotations

Or declare a function's parameters and return type:

``` python
def greeting(name: str) -> str:
    return f"Hello {name}!"
```

## Type Annotations

::: notes

A 'typing' module provides support for more involved declarations,
such as declaring the types of a container's items:

:::

``` python
from typing import List

Vector = List[float]

def scale(scalar: float, vector: Vector) -> Vector:
    return [scalar * num for num in vector]
```

::: notes

The above syntax
and the standard library typing module
are Python 3 only,
but Python 2 users can use an alternative syntax, embedded in comments,
and a port of the typing module is available as a downloadable module on PyPI.
So all of this applies to Python2 just as much as Python3.

For the last few years,
type annotations have been regarded
with some wariness and suspicion.
They make source code more visually noisy,
and they defy Python's dynamic typing.
These have both been core tenets of the language,
and are arguably major reasons why Python is
so pleasant and productive to code in.

However, the community seem to be coming around to the benefits.
This is the first PyCon at which the prevailing consensus
seemed to be in favor of the feature,
rather than reserved or even outright hostile.

There were several talks on the topic
both explorations of static analysis and types,
and retrospectives from real-world use.
Companies such as Instagram and Dropbox
provided talks that reported positively on
introducing type annotations across multi-million line codebases.

This positivity is reinforced by the 3.7 release,
which includes important improvements and fixes,
such as allowing forward references.
These are important in cases such as
when a class contains references to itself.

:::

## Type Annotations

Forward declarations

``` python
class Node:
    def __init__(self, parent):
        self.parent : Node = parent
```

::: notes

Quite deliberately, the interpreter itself
does no runtime checks that values correspond to the declared types.
It attaches no meaning to these annotations,
other than making the annotations available
to third party tools inspecting the code.

One such tool is 'mypy'.
which checks the annotations at 'linting time',
the same time you might run tools like PyLint.

:::

## Type Annotations

MyPy

``` python
def greeting(name: str) -> str:
    return f"Hello {name}!"

print(greeting(123))
```

::: notes

Given a usage which breaks the typing constraints,

:::

```
$ mypy variable.py
type_me.py:4: error: Argument 1 to "greeting" has incompatible
type "int"; expected "str
```

::: notes

it produces an appropriate error message.

The benefits of this are:

:::

## Type Annotations

* Correctness
* Readability
* Performance

::: notes

* Correctness.
  Checking, at lint time, analogous to a static language's compile-time checks.
* Readability.
  When you see a call to a obj.validate() method, if you know
  the type of 'obj' then in many cases you can immediately know exactly which
  validate method is getting called. This is useful for both human readers,
  and for tools such as code editors doing a 'go to definition', which
  previously would often have to provide a list of possible definitions to
  choose between.
* Performance.
  There is speculative potential for this to feed into runtime optimisation.
  * CPython is about 100 times slower than C
  * Averaged across all benchmarks, PyPy the optimised Python interpreter
    written in Python, executes the same source code 7 times faster than
    CPython.
  * Cython, with some work manually annotating C types in Python source code,
    is comparable with C.

PyPy and Cython achieve their speed by trading off dynamic language features
for speed, which they are able to do by nailing down the type of variables,
either using source code annotation, or by inspecting types at runtime. In
theory Python's new type annotations could drive or aid type inferences like
this, providing a run time performance boost.

:::

## Type Annotations

Are optional.

::: notes

A critical part of this whole picture is that type annotations are,
and always will be, entirely optional.

Type annotations can be added partially,
for judiciously chosen sections of code,
or for individual variables,
leaving everything around them dynamic.
It can be done at any point during development.
It doesn't have to happen from the beginning of a project.

This supports the concept of _gradual typing_,
which has an illustrious history,
laying claim to provide the best of both worlds
in the great static versus dynamic holy war.

So the takeaway is that the type annotation experiment
is being considered a success,
and it looks to be a central part of Python's future.

:::

## New NamedTuple Syntax

::: notes

For an example of type annotations being integrated into the language,
Python 3.6 introduced a much improved syntax for namedtuples.

Regular tuples are Python's immutable sequence,
storing a heterogeneous sequence of values
which cannot later be modified.
For those not familiar with Python,
think of these as an array of pointers to objects,
where 'objects' in Python means anything at all,
including other collections, functions or lambdas, classes,
or any built-in type.

:::

## New NamedTuple Syntax

A regular tuple:

``` python
x = (3, "abc", 4.5)
```

::: notes

In practice, this lends itself to the sematics of a struct,
a lightweight alternative to defining a new class.

:::

## New NamedTuple Syntax

Using a regular tuple:

``` python
hue = 45
saturation = 0.1
lightness = 0.9
color = (hue, saturation, lightness)
```

::: notes

Here, the position in the sequence implies a particular meaning.
The first value always represents a hue, the second a saturation,
and the third a lightness.

It makes no sense to try and append or remove items from the tuple.
The values in there are best thought of as attributes of a value type,
not items in a sequence.

Although convenient,
the disadvantage of using tuples in this way is that
addressing individual elements requires a numeric index:

:::

## New NamedTuple Syntax

Addressing fields of a regular tuple:

``` python
saturation = color[1]
```

::: notes

This is cryptic and ugly.

To fix this,
_named_ tuples were introduced to the standard library
a few years ago.

They provide a type of tuple which, amongst other things, is also addressable
by field name:

:::

## New NamedTuple Syntax

Using a named tuple:

``` python
saturation = color.saturation
```

::: notes

This is much better.

However, the wart on using named tuples has always been that they are not
built into the core language, but instead were built on top of it, and
so lack a dedicated syntax for creating the namedtuple type.

Hence, defining a named tuple looks like this:

:::

## New NamedTuple Syntax

Declaring a named tuple:

``` python
from collections import namedtuple

Color = namedtuple('Color', ['hue', 'saturation', 'lightness'])
```

:-(

::: notes

This is clunky and underpowered. It looks nothing like declaration
of a regular class, nor like structures in other languages.

But 3.6 introduced a new way to declare named tuples,
which again requires no dedicated syntax,
but this time leans on type annotations
and is much more acceptable:

:::

## New NamedTuple Syntax

Sane named tuple declaration:

``` python
from typing import NamedTuple

class Color(NamedTuple):
    hue: int
    saturation: float
    lightness: float = 0.5
```

::: notes

Syntactically, this is just a regular class, with some class-level attributes,
and those attributes have type annoations.

But the NamedTuple base class triggers
some metaclass magic to transform this into a namedtuple,
rather than a regular class.

As usual, the Python interpreter does nothing with these type annotations.
But tools like mypy will use them to validate instances of Color.

This far more closely resembles the creation of regular classes,
and also allows for default values on attributes.

It also converges the syntax for creating namedtuples with that for creating
a major new feature of Python3.7, dataclasses.

:::

## DataClasses

::: notes

Namedtuples are very useful as lightweight structs,
but sometimes one needs to define a value type
with different behavior than the underlying tuple provides.

Traditionally at this point in Python one would define a regular class:

:::

## DataClasses

Using a regular class:

``` python
class Employee:
    def __init__(self, name: str, age: int, manager: Employee):
        self.name = name
        self.age = age
        self.manager = manager

# Gives the desired behavior for reference types:
e1 = Employee('alice', 55, None)
e2 = Employee('bob', 45, e1)
e1 == e2 # False
e1 < e2 # Type Error: '<' not supported
```

::: notes

Such a class behaves pretty well out of the box
for _reference_ or _identity_ types. For these types of classes
we don't want two employees to compare equal even if all their attributes
are the same.
Such instances should instead compare based on identity.

Also, we don't want reference types like this to be comparable by inequalities.
It usually makes no sense to ask if one employee is "less than" another.
Hence these will also not be sortable.

This is exactly how regular classes behave out of the box,
so they're perfect for this case.

But if you're defining a _value_ type,
these are commonly not the behaviors you want.

Commonly desired value type semantics, for example
would consider two instances with identical attributes 
to be equal.

:::

## DataClasses

Desired value type behavior:

``` python
# Desireable value type behavior
c1 = Card("Q", "Hearts")
c2 = Card("Q", "Hearts")
c1 == c2 # True
```

::: notes

So, for example, in contrast to regular class behavior,
we'd like playing card instances to compare equal if
they have the same attribute values, not just if they
are the identical instance.

Similarly, many value types have default expectations
with regard to inequalities such as "less than",
and hence ought to be sortable by default.

Also, we'd like sensible value-type hashing behaviour,
so these classes could be used in sets, or as keys in
dictionaries.

Regular classes, tuned as they are for
reference type behaviour, need customizing with custom
methods to provide these behaviors:

:::

## DataClasses

Customizing regular classes with manually written methods:

``` python
def __eq__(self, other):
    return (
        self.rank == other.rank and
        self.suit == other.suit
    )
```

::: notes

This is easily done but is verbose and repetitive boilerplate code.

The solution to this problem is
Data classes, newly introduced in 3.7,

:::

## DataClasses

Defining a data class

``` python
from dataclasses import dataclass

@dataclass
class Card:
    rank: str
    suit: str
```

::: notes

These are designed for the times when you want value type semantics
out of the box, much reducing the need for boilerplate code.

The above Card class supports the sort of comparison operators
you'd generally want by default for a value type. It also provides
a decent string representation, and sensible hashing so that these
instances can be used in sets, and as keys in dictionaries.

As before, data class fields can be given a default value:

:::

## DataClasses

Default field values:


``` python
@dataclass
class Point:
    x: float = 0.0
    y: float = 0.0
    z: float = 0.0

>>> Point(y=1.23)
Point(x=0.0, y=1.23, z=0.0)
```

::: notes

Although mutable by default, a data class can be declared,
at class creation time,
to be immutable:

:::

## DataClasses

Immutable instances:

``` python
@dataclass(frozen=True)
class Point:
    x: float
    y: float
    z: float

>>> p = Point(1, 2, 3)
>>> p.z = 5
dataclasses.FrozenInstanceError: cannot assign to field 'name'
```

::: notes

Similarly, other creation parameters can control whether instances are
comparable & sortable, hashable, and which fields are significant in
comparisons and in the string representation.

Features like this have been battle-tested for a long time in existing
3rd-party libraries such as 'attrs', but this was suffering from a
proliferation of almost-identical competitors, so it makes sense for this sort
of feature to finally be embraced and supported as part of the standard
library.

:::

## Education & outreach

::: notes

Education and outreach to the disadvantaged
has long been an explicit focus of PyCon,
with dedicated talks and sessions on the topic.
Teachers and other educators attend the conference and mix
freely with programmers,
sparking many productive and fascinating conversations
in hallways and over lunch.

Presentations included a talk by a 13 year old student about a
visual programming language he had created in Python, inspired by Scratch
but using Pythonic syntax.

One keynote focussed on the experiences of a Detroit public
librarian, teaching Python to disadvantaged school children,
and there wasn't a dry eye in the house as she described the
ferocious determination the kids exhibited, overcoming huge
obstacles in their lives in order to attend, and the life-chaning
effects the class had on many of them.

:::

## Inclusiveness & diversity

::: notes

Another regular feature of the Pycon conference and the Python
community as a whole is the attitude of radical inclusiveness.
Only a few years ago
the number of female attendees was around 5%, but
with explicit efforts to address this systematic bias, we've
been able to increase that to nearer 20%, and fully half of
the talks, keynotes, and conference organising is done by women,
trans and nonbinary folks.
There's still a long way to go, but, to me, it's absolutely joyful to
see that the problem is tractable, and given pro-active efforts,
we're getting better year on year.

The diversity of diverse values

The word has spread that Python welcomes everybody, and the
common refrain, on lips and on T-shirts, was that people
"Came for the language, stayed for the community."

:::

## All on Youtube

::: notes

All talks (30/45 minutes) and tutorials (3 hours) are now on YouTube:

:::

https://www.youtube.com/channel/UCsX05-2sVSH7Nx3zuk3NYuQ/videos

Search 'PyCon 2018'

::: notes

If you are travelling home from a conference, unsure of your internet
connectivity, you can sign up for YouTube Red (soon to be called "Premium"),
for a month free trial, and can then download videos to your phone.

Remember also that youtube can playback videos at x1.5 or even x2 speed.

Combine those two features, you can make decent headway through all the
talks you missed while flying home from the conference.

Some particular highlights of the talks for me were:

### Language features

Elegant solutions for everyday python problems, by Nina Zakherenko
(ie. magic methods, partial methods, context managers, decorators, namedtuples)
https://www.youtube.com/watch?v=WiQqqB9MlkA

Dataclasses by Raymond Hettinger
https://www.youtube.com/watch?v=T-TwcmT6Rcw&t=1377s

Effortless logging: A deep dive into the logging module by Mario Corchero
https://www.youtube.com/watch?v=Pbz1fo7KlGg&t=1134s

HOWTO write a function, by Jack Diederich
(A thirty minute code review, no holds barred. Spot on.)
https://www.youtube.com/watch?v=rrBJVMyD-Gs&t=933s

#### Type annotations

Types, deeper static analysis, and you. By Pieter Hooiumeijer
https://www.youtube.com/watch?v=hWV8t494N88

Type-checked Python in the real world by Carl Meyer
https://www.youtube.com/watch?v=pMgmKJyWKn8

https://www.youtube.com/watch?v=0c46YHS3RY8
Clearer Code at Scale: Static Types at Zulip and Dropbox, by Greg Price

### CompSci

Big-O: How code slows and data grows by Ned Batchelder
https://www.youtube.com/watch?v=duvZ-2UK0fc

### Deployment, Systems, DevOps, Containers

Systemd: why you should care as a Python programmer, by Alvaro Leiva Geisse
https://www.youtube.com/watch?v=ZUX9Fx8Rwzg

How Netflix does failovers in 7 minutes flat.
(He means switching NetFlix's audience, 1/3 of the internet's
traffic at peak, from using regions A and B, to using B and C.)
https://www.youtube.com/watch?v=iQI56-up3Yk&t=1673s

How to write deployment friendly applications, by Hynek Schlawack
https://www.youtube.com/watch?v=wuCpCkrfeMs&t=568s

A Python-flavored introduction to containers and kubernetes, by Ruben Orduz & Nolan Brubaker
https://www.youtube.com/watch?v=kx-048qE-TI&t=6s

### Async

Trio: Async concurrency for mere mortals, by Nathaniel Smith
(Interesting new approach built on top of asyncio, using new
primitives which seem to make async easier to understand,
more correct, and with dramatically less code.)
https://www.youtube.com/watch?v=oLkfnc_UMcE

Thinking outside the GIL with AsyncIO and Multiprocessing, by John Reese
https://www.youtube.com/watch?v=0kXaLh8Fz3k

### Data wrangling, machine learning, numerical:

Performance Python: seven strategies for optimizing numerical code by Jake VanderPlas
https://www.youtube.com/watch?v=zQeYx87mfyw

A practical guide to Singular Value Decomposition by Daniel Pyrathon.
https://www.youtube.com/watch?v=d7iIb_XVkZs

### Packaging

Pipenv: The future of Python dependency management, by Kenneth Reitz
https://www.youtube.com/watch?v=GBQAKldqgZs

### Workflow

Solve your problem with sloppy Python, by Larry Hastings
(A modest sounding topic, but Larry is smart and gets-things-done,
we can all learn a few things from him.)
https://www.youtube.com/watch?v=Jd8ulMb6_ls

:::

