# Python.NET

Python.NET (`pythonnet`) is a package that gives Python programmers
nearly seamless integration with .NET Framework, .NET Core  and Mono
runtime on Windows, Linux and macOS. Python.NET
provides a powerful application scripting tool for .NET developers.
Using this package you can script .NET applications or build entire
applications in Python, using .NET services and components written in
any language that targets the CLR (C#, VB.NET, F#, C++/CLI).

Note that this package does _not_ implement Python as a first-class CLR
language - it does not produce managed code (IL) from Python code. Rather,
it is an integration of the CPython engine with a .NET runtime.
This approach allows one to use CLR services and continue to use existing
Python code and C-API extensions while maintaining native execution
speeds for Python code. If you are interested in a pure managed-code
implementation of the Python language, you should check out the
[IronPython][ipy] project, which is in active development.

Python.NET is currently compatible and tested with Python releases
3.7 - 3.11.

To subscribe to the [Python.NET mailing list][ml] or read the
[online archives][ml-arch] of the list, see the [mailing list information][ml]
page. Use the [Python.NET issue tracker][gh-issues] to report issues.

-   This page provides a detailed overview of Python.NET,
    as well as some basic usage examples. Many other examples can be
    found in the demos and unit tests for the package.
-   Checkout the [Python.NET][repo] code from github.
-   [Download releases][gh-release]

## Installation

Python.NET is available as a source release on [GitHub][gh-release] and as a
single binary wheel distribution for all supported versions of Python and 
from the [Python Package Index][pypi]. A [Nuget package][nuget] is provided
to embed Python into .NET.

## Documentation

Documentation for Python.NET is available here:
- [Documentation for Python.NET][doc]
    - [Embedding .NET into Python][doc-py]
    - [Embedding Python into .NET][doc-net]

Documentation for the lower-level [`clr-loader`](cl) library is available here:
[Documentation for `clr-loader`][doc-cl]

## License

Python.NET is released under the open source MIT License.
A copy of the license is included in the distribution,
or you can find a copy of the [license online][license].

## .NET Foundation

This project is supported by the [.NET Foundation](https://dotnetfoundation.org).

[ipy]: https://ironpython.net/

[ml]: https://mail.python.org/mailman3/lists/pythonnet.python.org/

[ml-arch]: https://mail.python.org/archives/list/pythonnet@python.org/

[gh-release]: https://github.com/pythonnet/pythonnet/releases

[pypi]: https://pypi.python.org/pypi/pythonnet

[nuget]: https://www.nuget.org/packages/pythonnet

[license]: https://pythonnet.github.io/LICENSE

[doc]: https://pythonnet.github.io/pythonnet/

[doc-py]: https://pythonnet.github.io/pythonnet/python.html

[doc-net]: https://pythonnet.github.io/pythonnet/dotnet.html

[doc-cl]: https://pythonnet.github.io/clr-loader/

[gh-issues]: https://github.com/pythonnet/pythonnet/issues

[repo]: https://github.com/pythonnet/pythonnet
