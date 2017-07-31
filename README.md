# Python for .NET

Python for .NET (`pythonnet`) is a package that gives Python programmers
nearly seamless integration with the .NET 4.0+ Common Language Runtime
(CLR) on Windows and Mono runtime on Linux and OSX. Python for .NET
provides a powerful application scripting tool for .NET developers.
Using this package you can script .NET applications or build entire
applications in Python, using .NET services and components written in
any language that targets the CLR (C#, VB.NET, F#, C++/CLI).

Note that this package does _not_ implement Python as a first-class CLR
language - it does not produce managed code (IL) from Python code. Rather,
it is an integration of the CPython engine with the .NET or Mono runtime.
This approach allows you to use CLR services and continue to use existing
Python code and C-API extensions while maintaining native execution
speeds for Python code. If you are interested in a pure managed-code
implementation of the Python language, you should check out the
[IronPython][1] project, which is in active development.

Python for .NET is currently compatible and tested with Python releases
`2.7`, `3.3`, `3.4`, `3.5`, and `3.6`.
Current releases are available at the [Python for .NET website][2].
To subscribe to the [Python for .NET mailing list][3] or read the
[online archives][4] of the list, see the [mailing list information][3]
page. Use the [Python for .NET issue tracker][55] to report issues.

-   This page provides a detailed overview of Python for .NET,
    as well as some basic usage examples. Many other examples can be
    found in the demos and unit tests for the package.
-   Checkout the [PythonNet][77] code from github.
-   [Download releases][6] for various versions of Python and CLR.

## Installation

Python for .NET is available as a source release on [GitHub][5] and as a
binary wheel distribution for all supported versions of Python and the
common language runtime from the [Python Package Index][6].

The source release is a self-contained "private" assembly. Just unzip the
package wherever you want it, cd to that directory, build the solution
`python setup.py build_ext --inplace`. Once you start up Python or
IPython interpreter in this directory or append this directory to
`sys.path`, then after `import clr` statement .NET assemblies can
be used.
You can also run `nPython.exe` (`mono nPython.exe` on `*nix`) to check
how python can be embedded in console .NET application. Note that the
source release does not include a copy of the CPython runtime, so you will
need to have installed Python on your machine before using the source release.

**Running on Linux/Mono:** Unit testing shows that PythonNet will run
under [Mono][7], though the Mono runtime is less supported so there still
may be problems.

## Getting Started

A key goal for this project has been that Python for .NET should
"work just the way you'd expect in Python", except for cases that are
.NET specific (in which case the goal is to work "just the way
you'd expect in C#"). In addition, with the IronPython project having
established a community, it is my goal that code written for IronPython
run without modification under Python for .NET.

If you already know Python, you can probably finish this readme and then
refer to .NET docs to figure out anything you need to do. Conversely if
you are familiar with C# or another .NET language, you probably just need
to pick up one of the many good Python books or read the Python tutorial
online to get started.

A good way to start is to interactively explore .NET usage in python
interpreter by following along with the examples in this document. If you
get stuck, there are also a number of demos and unit tests located in the
source directory of the distribution that can be helpful as examples.

## Importing Modules

Python for .NET allows CLR namespaces to be treated essentially as
Python packages.

```python
from System import String
from System.Collections import *
```

Types from any loaded assembly may be imported and used in this manner.
To load an assembly, use the `AddReference` function in the `clr` module:

```python
import clr
clr.AddReference("System.Windows.Forms")
from System.Windows.Forms import Form
```

**Note** Earlier releases of Python for .NET relied on "implicit loading"
to support automatic loading of assemblies whose names corresponded to an
imported namespace. Implicit loading still works for backward compatibility,
but will be removed in a future release so it is recommended to use
the `clr.AddReference` method.

Python for .NET uses the PYTHONPATH (sys.path) to look for assemblies
to load, in addition to the usual application base and the GAC. To ensure
that you can implicitly import an assembly, put the directory containing
the assembly in `sys.path`.

## Using Classes

Python for .NET allows you to use any non-private classes, structs,
interfaces, enums or delegates from Python. To create an instance of a
managed class, you use the standard instantiation syntax, passing a set
of arguments that match one of its public constructors:

```python
from System.Drawing import Point

p = Point(5, 5)
```

In most cases, Python for .NET can determine the correct constructor to
call automatically based on the arguments. In some cases, it may be necessary
to call a particular overloaded constructor, which is supported by a special
`__overloads__` attribute, which will soon be deprecated in favor of
iPy compatible "Overloads", on a class:

```python
from System import String, Char, Int32

s = String.Overloads[Char, Int32]('A', 10)
s = String.__overloads[__Char, Int32]('A', 10)
```

## Using Generics

Pythonnet also supports generic types. A generic type must be bound to
create a concrete type before it can be instantiated. Generic types support
the subscript syntax to create bound types:

```python
from System.Collections.Generic import Dictionary
from System import *

dict1 = Dictionary[String, String]()
dict2 = Dictionary[String, Int32]()
dict3 = Dictionary[String, Type]()
```

When you pass a list of types using the subscript syntax, you can also
pass a subset of Python types that directly correspond to .NET types:

```python
dict1 = Dictionary[str, str]()
dict2 = Dictionary[str, int]()
dict3 = Dictionary[str, Decimal]()
```

This shorthand also works when explicitly selecting generic methods or
specific versions of overloaded methods and constructors (explained later).

You can also subclass managed classes in Python, though members of the
Python subclass are not visible to .NET code. See the `helloform.py` file
in the `/demo` directory of the distribution for a simple Windows Forms
example that demonstrates subclassing a managed class.

## Fields And Properties

You can get and set fields and properties of CLR objects just as if they
were regular attributes:

```python
from System import Environment

name = Environment.MachineName
Environment.ExitCode = 1
```

## Using Indexers

If a managed object implements one or more indexers, you can call the
indexer using standard Python indexing syntax:

```python
from System.Collections import Hashtable

table = Hashtable()
table["key 1"] = "value 1"
```

Overloaded indexers are supported, using the same notation one would
use in C#:

```python
items[0, 2]

items[0, 2, 3]
```

## Using Methods

Methods of CLR objects behave generally like normal Python methods.
Static methods may be called either through the class or through an
instance of the class. All public and protected methods of CLR objects
are accessible to Python:

```python
from System import Environment

drives = Environment.GetLogicalDrives()
```

It is also possible to call managed methods `unbound`
(passing the instance as the first argument) just as with Python methods.
This is most often used to explicitly call methods of a base class.

**Note** There is one caveat related to calling unbound methods: it is
possible for a managed class to declare a static method and an instance
method with the same name. Since it is not possible for the runtime to know
the intent when such a method is called unbound, the static method will
always be called.

The docstring of CLR a method (**doc**) can be used to view the signature
of the method, including overloads if the CLR method is overloaded.
You can also use the Python `help` method to inspect a managed class:

```python
from System import Environment

print(Environment.GetFolderPath.__doc__)

help(Environment)
```

## Overloaded and Generic Methods

While Python for .NET will generally be able to figure out the right
version of an overloaded method to call automatically, there are cases
where it is desirable to select a particular method overload explicitly.

Methods of CLR objects have an `__overloads__`, which will soon be
deprecated in favor of iPy compatible Overloads, attribute that can be
used for this purpose:

```python
from System import Console

Console.WriteLine.Overloads[bool](true)
Console.WriteLine.Overloads[str]("true")
Console.WriteLine.__overloads[__int](42)
```

Similarly, generic methods may be bound at runtime using the subscript
syntax directly on the method:

```python
someobject.SomeGenericMethod[int](10)
someobject.SomeGenericMethod[str]("10")
```

## Delegates And Events

Delegates defined in managed code can be implemented in Python.
A delegate type can be instantiated and passed a callable Python object
to get a delegate instance. The resulting delegate instance is a true
managed delegate that will invoke the given Python callable when it
is called:

```python
def my_handler(source, args):
    print('my_handler called!')

# instantiate a delegate
d = AssemblyLoadEventHandler(my_handler)

# use it as an event handler
AppDomain.CurrentDomain.AssemblyLoad += d
```

Multicast delegates can be implemented by adding more callable objects to
a delegate instance:

```python
d += self.method1
d += self.method2
d()
```

Events are treated as first-class objects in Python, and behave in many
ways like methods. Python callbacks can be registered with event attributes,
and an event can be called to fire the event.

Note that events support a convenience spelling similar to that used in C#.
You do not need to pass an explicitly instantiated delegate instance to an
event (though you can if you want). Events support the `+=` and `-=`
operators in a way very similar to the C# idiom:

```python
def handler(source, args):
    print('my_handler called!')

# register event handler
object.SomeEvent += handler

# unregister event handler
object.SomeEvent -= handler

# fire the event
result = object.SomeEvent(...)
```

## Exception Handling

You can raise and catch managed exceptions just the same as you would
pure-Python exceptions:

```python
from System import NullReferenceException

try:
    raise NullReferenceException("aiieee!")
except NullReferenceException as e:
    print(e.Message)
    print(e.Source)
```

## Using Arrays

The type `System.Array` supports the subscript syntax in order to
make it easy to create managed arrays from Python:

```python
from System import Array

myarray = Array[int](10)
```

Managed arrays support the standard Python sequence protocols:

```python
items = SomeObject.GetArray()

# Get first item
v = items[0]
items[0] = v

# Get last item
v = items[-1]
items[-1] = v

# Get length
l = len(items)

# Containment test
test = v in items
```

Multidimensional arrays support indexing using the same notation one
would use in C#:

```python
items[0, 2]

items[0, 2, 3]
```

## Using Collections

Managed arrays and managed objects that implement the IEnumerable
interface can be iterated over using the standard iteration
Python idioms:

```python
domain = System.AppDomain.CurrentDomain

for item in domain.GetAssemblies():
    name = item.GetName()
```

## Using COM Components

Using Microsoft-provided tools such as `aximp.exe` and `tlbimp.exe`,
it is possible to generate managed wrappers for COM libraries. After
generating such a wrapper, you can use the libraries from Python just
like any other managed code.

Note: currently you need to put the generated wrappers in the GAC, in
the PythonNet assembly directory or on the PYTHONPATH in order to load them.

## Type Conversion

Type conversion under Python for .NET is fairly straightforward - most
elemental Python types (string, int, long, etc.) convert automatically to
compatible managed equivalents (String, Int32, etc.) and vice-versa.
Note that all strings returned from the CLR are returned as unicode.

Types that do not have a logical equivalent in Python are exposed as
instances of managed classes or structs (System.Decimal is an example).

The .NET architecture makes a distinction between `value types` and
`reference types`. Reference types are allocated on the heap, and value
types are allocated either on the stack or in-line within an object.

A process called `boxing` is used in .NET to allow code to treat a value
type as if it were a reference type. Boxing causes a separate copy of the
value type object to be created on the heap, which then has reference
type semantics.

Understanding boxing and the distinction between value types and reference
types can be important when using Python for .NET because the Python
language has no value type semantics or syntax - in Python
"everything is a reference".

Here is a simple example that demonstrates an issue. If you are an
experienced C# programmer, you might write the following code:

```python
items = System.Array.CreateInstance(Point, 3)
for i in range(3):
    items[i] = Point(0, 0)

items[0].X = 1 # won't work!!
```

While the spelling of `items[0].X = 1` is the same in C# and Python,
there is an important and subtle semantic difference.
In C# (and other compiled-to-IL languages), the compiler knows that
Point is a value type and can do the Right Thing here, changing the
value in place.

In Python however, "everything's a reference", and there is really no
spelling or semantic to allow it to do the right thing dynamically.
The specific reason that `items[0]` itself doesn't change is that when
you say `items[0]`, that getitem operation creates a Python object that
holds a reference to the object at `items[0]` via a GCHandle.
That causes a ValueType (like Point) to be boxed, so the following
setattr (`.X = 1`) _changes the state of the boxed value,
not the original unboxed value_.

The rule in Python is essentially:

> the result of any attribute or item access is a boxed value

and that can be important in how you approach your code.

Because there are no value type semantics or syntax in Python,
you may need to modify your approach. To revisit the previous example,
we can ensure that the changes we want to make to an array item
aren't "lost" by resetting an array member after making changes to it:

```python
items = System.Array.CreateInstance(Point, 3)
for i in range(3):
    items[i] = Point(0, 0)

# This _will_ work. We get 'item' as a boxed copy of the Point
# object actually stored in the array. After making our changes
# we re-set the array item to update the bits in the array.

item = items[0]
item.X = 1
items[0] = item
```

This is not unlike some of the cases you can find in C# where you have to
know about boxing behavior to avoid similar kinds of `lost update` problems
(generally because an implicit boxing happened that was not taken
into account in the code).

This is the same thing, just the manifestation is a little different
in Python. See the .NET documentation for more details on boxing and the
differences between value types and reference types.

## Embedding Python

**Note:** because Python code running under Python for .NET is inherently
unverifiable, it runs totally under the radar of the security infrastructure
of the CLR so you should restrict use of the Python assembly to trusted code.

The Python runtime assembly defines a number of public classes that
provide a subset of the functionality provided by the Python C-API.

These classes include PyObject, PyList, PyDict, PyTuple, etc.
You can review the nPython.exe source code in in "Console.csproj" project
for example of embedding CPython in console .NET app. Please refer to
this README GitHub page for new simplified embedding API:

[README.md][8]

At a very high level, to embed Python in your application you will need to:

-   Reference Python.Runtime.dll in your build environment
-   Call PythonEngine.Intialize() to initialize Python
-   Call PythonEngine.ImportModule(name) to import a module

The module you import can either start working with your managed app
environment at the time its imported, or you can explicitly lookup and
call objects in a module you import.

For general-purpose information on embedding Python in applications,
use www.python.org or Google to find (C) examples. Because
Python for .NET is so closely integrated with the managed environment,
you will generally be better off importing a module and deferring to
Python code as early as possible rather than writing a lot of managed
embedding code.

**Important Note for embedders:** Python is not free-threaded and
uses a global interpreter lock to allow multi-threaded applications
to interact safely with the Python interpreter.
Much more information about this is available in the Python C-API
documentation on the www.python.org Website.

When embedding Python in a managed application, you have to manage the
GIL in just the same way you would when embedding Python in
a C or C++ application.

Before interacting with any of the objects or APIs provided by the
Python.Runtime namespace, calling code must have acquired the Python
global interpreter lock by calling the `PythonEngine.AcquireLock` method.
The only exception to this rule is the `PythonEngine.Initialize` method,
which may be called at startup without having acquired the GIL.

When finished using Python APIs, managed code must call a corresponding
`PythonEngine.ReleaseLock` to release the GIL and allow other threads
to use Python.

The AcquireLock and ReleaseLock methods are thin wrappers over the
unmanaged `PyGILState_Ensure` and `PyGILState_Release` functions from
the Python API, and the documentation for those APIs applies to
the managed versions.

## License

Python for .NET is released under the open source MIT License.
A copy of the license is included in the distribution,
or you can find a copy of the [license online][9].

Some distributions of this package include a copy of the C Python dlls and
standard library, which are covered by the [Python license][10].

[1]: http://www.ironpython.com

[2]: http://pythonnet.github.io/

[3]: http://mail.python.org/mailman/listinfo/pythondotnet

[4]: http://mail.python.org/pipermail/pythondotnet/

[5]: https://github.com/pythonnet/pythonnet/releases

[6]: https://pypi.python.org/pypi/pythonnet

[7]: http://www.go-mono.com

[8]: https://github.com/pythonnet/pythonnet/blob/master/README.md

[9]: http://pythonnet.github.io/LICENSE

[10]: http://www.python.org/license.html

[55]: http://github.com/pythonnet/pythonnet/issues

[77]: http://github.com/pythonnet/pythonnet
