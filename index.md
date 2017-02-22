# Python for .NET

Python for .NET (`pythonnet`) is a package that gives Python programmers
nearly seamless integration with the .NET 4.0+ Common Language Runtime
(`CLR`) on Windows and Mono runtime on Linux and OSX. Python for .NET
provides a powerful application scripting tool for .NET developers.
Using this package you can script .NET applications or build entire
applications in Python, using .NET services and components written in
any language that targets the CLR (C#, VB.NET, F#, C++/CLI).

Note that this package does _not_ implement Python as a first-class CLR
language - it does not produce managed code (IL) from Python code. Rather,
it is an integration of the CPython engine with the .NET or Mono runtime.
This approach allows you to use CLR services and continue to use existing
Python code and C-based extensions while maintaining native execution
speeds for Python code. If you are interested in a pure managed-code
implementation of the Python language, you should check out the
[IronPython][1] project, which is in active development.

Python for .NET is currently compatible with Python releases
`2.7`, `3.3`, `3.4`, `3.5` and `3.6`.
To subscribe to the [Python for .NET mailing list][2] or read the
[online archives][3] of the list, see the [mailing list information][2]
page. You can also send questions or comments to me at
[brian.d.lloyd@gmail.com][4] or use the [Python for .NET issue tracker][5]
to report issues.

-   The [README][6] provides a detailed overview of Python for .NET,
    as well as some basic usage examples. Many other examples can be
    found in the demos and unit tests for the package.
-   Checkout the [PythonNet][7] code from github.
-   [Download releases][8] for various versions of Python and CLR.

[1]: http://www.ironpython.com

[2]: http://mail.python.org/mailman/listinfo/pythondotnet

[3]: http://mail.python.org/pipermail/pythondotnet/

[4]: mailto:brian.d.lloyd@gmail.com

[5]: http://github.com/pythonnet/pythonnet/issues

[6]: http://pythonnet.github.io/readme.html

[7]: http://github.com/pythonnet/pythonnet

[8]: https://pypi.python.org/pypi/pythonnet
