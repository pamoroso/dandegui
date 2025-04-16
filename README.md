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


#### `OPEN-WINDOW-STREAM &KEY TITLE` [function]

Opens a new window and returns the associated output stream. The function sets the window title to `TITLE` if supplied.


#### `WITH-OUTPUT-TO-WINDOW (VAR &KEY TITLE) &BODY BODY` [macro]

Performs the operations in `BODY` with `VAR` bound to a new window stream.

Creates a new window titled `TITLE` if supplied, binds `VAR` to the `TEXTSTREAM` associated with the window, and executes `BODY` in this context. Returns the value of the last form of BODY. After control leaves the context of `WITH-OUTPUT-TO-WINDOW` the new window no longer accepts output to the associated stream.


#### `WITH-WINDOW-STREAM (VAR STREAM) &BODY BODY` [macro]

Performs the operations in `BODY` with `VAR` bound to a window `STREAM`.

Evaluates the forms in `BODY` in a context in which `VAR` is bound to `STREAM` which must already exist, then returns the value of the last form of BODY.


## Learn more

* [Medley Interlisp](https://interlisp.org)


## Author

DandeGUI is developed by [Paolo Amoroso](https://github.com/pamoroso).


## License

This code is distributed under the MIT license, see the `LICENSE` file.
