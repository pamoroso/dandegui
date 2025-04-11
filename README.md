# DandeGUI

DandeGUI (pronounced as "dandy guy") is a Medley Interlisp library of GUI elements and facilities for programs that output simple text and graphics.

The library, which is written in and exposes its functionality as Common Lisp, provides windows for stream-based and graphical output which automaticaly handle repainting, resizing, and scrolling. DandeGUI captures typical GUI patterns of the Medley environment such as printing text to a new window instead of an Exec.


## Installation

Download the file `DANDEGUI` from the project repo, copy it to a file system location your Medley system has access to, and optionally compile the source by evaluating the following expression at a Common Lisp Exec such as the XCL Exec:

```
(compile-file "DANDEGUI")
```

Next, to load the program evaluate:

```lisp
(load "DANDEGUI.DFASL")
```

or:

```lisp
(il:filesload dandegui)
```


## Learn more

* [Medley Interlisp](https://interlisp.org)


## Author

DandeGUI is developed by [Paolo Amoroso](https://github.com/pamoroso).


## License

This code is distributed under the MIT license, see the `LICENSE` file.
