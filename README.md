# DandeGUI

DandeGUI (pronounced "dandy guy") is a Medley Interlisp library of GUI elements and facilities for programs that output simple text and graphics.

![A text output window created with DandeGUI on Medley Interlisp and the Lisp code that generated it.](https://raw.githubusercontent.com/pamoroso/dandegui/main/dandegui.png)

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

The functions and macros of DandeGUI create read-only, resizable windows with scrollable content and allow to send output to these windows. Any text in the windows may be selected and copied via the usual facilities of the Medley environment.


### Examples

Suppose you want to display a table of square roots in new window. The table contains a row with the headings of two columns, one for a sequence of numbers and the other for the corresponding square roots. The simplest way to do this is to use the DandeGUI macro `WITH-OUTPUT-TO-WINDOW`:

```lisp
(gui:with-output-to-window (stream :title "Table of square roots")
  (format stream "~&Number~40TSquare Root~2%")
  (loop
    for n from 1 to 30
    do (format stream "~&~4D~40T~8,4F~%" n (sqrt n))))
```

This code produces the main window in the screenshot above. The variable `stream` is bound to the stream associated to a newly created window with title supplied by `:title`. The calls to `format` populate the table by printing to `stream`.

Since `stream` is not accessible outside of the scope of the macro `WITH-OUTPUT-TO-WINDOW` works best with one-off output you don't need to append to. `WITH-WINDOW-STREAM` is a better option if instead you generate the output in successive steps; from different parts of the program; or interleave output to more than one window:

```lisp
(defun print-output1 (stream)
  (with-window-stream (str stream)
    (format str "...")
  
    ;; ...
  
    (format str "...")))

(defun print-output2 (stream)
  (with-window-stream (str stream)
    (format str "...")
  
    ;; ...
  
    (format str "...")))

(defun do-something-else ()
  ;; ...
  )

(defun main-program ()
  (let ((stream (gui:open-window-stream :title "Output Window")))
    (print-output1 stream)
    (do-something-else)
    (print-output2 stream)))

(main-program)
```

The function `OPEN-WINDOW-STREAM` creates a new window with title supplied by`:TITLE` and returns the associated output stream which output functions can print to. The functions must be wrapped in the context `WITH-WINDOW-STREAM` establishes by binding a variable to the appropriate stream. 

Like any other open stream the value of `OPEN-WINDOW-STREAM` may be closed when no longer in use, for example with `CLOSE`.


### Demos

The file `GUIDEMO` provides sample code to demonstrate the features of DandeGUI and how to use them.

To run the demos first load the file with `(LOAD 'GUIDEMO)`, which is not necessary to compile. If the compiled file `DANDEGUI.FASL` isn't already loaded `GUIDEMO` will load it assuming it's in the same directory or somewhere in the load path.

`GUIDEMO` exports the following functions from the `GUIDEMO` package.


#### `SQRT-TABLE &OPTIONAL N` [function]

Displays in a new text window a table of the square roots of the integers up to `N`, or a default value if `N` is not supplied.


### API reference

The functionality of DandeGUI is accessible via the following functions and macros in the `DANDEGUI` package nicknamed `GUI`.

Whenever a stream is passed or returned it is intended as an Interlisp `TEXTSTREAM` to which text may be printed by any output function that takes a stream as an argument such as with `FORMAT`, `PRIN1`, and so on. However, these functions must be called only using the DandeGUI output context macros and not outside ot them.


#### `OPEN-WINDOW-STREAM &KEY TITLE` [function]

Opens a new window and returns the associated output stream. The function sets the window title to `TITLE` if supplied.


#### `WINDOW-TITLE STREAM` [function]

Returns the title associated with `STREAM` or sets it if called from `SETF`.


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
