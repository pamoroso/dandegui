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


## Usage

### API reference

The functionality of DandeGUI is accessible via the following functions and macros in the `DANDEGUI` package nicknamed `GUI`.

Whenever a stream is passed or returned it is intended as an Interlisp `TEXTSTREAM` to which text may be printed by any output function that takes a stream as an argument such as with `FORMAT`, `PRIN1`, and so on. However, these functions must be called only using the DandeGUI output context macros and not outside ot them.

`OPEN-WINDOW-STREAM &KEY TITLE` (function): Opens a new window and returns the associated output stream. The function sets the window title to `TITLE` if supplied.


## Learn more

* [Medley Interlisp](https://interlisp.org)


## Author

DandeGUI is developed by [Paolo Amoroso](https://github.com/pamoroso).


## License

This code is distributed under the MIT license, see the `LICENSE` file.
