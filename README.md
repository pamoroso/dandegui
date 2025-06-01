# DandeGUI

DandeGUI (pronounced "dandy guy") is a Medley Interlisp library of GUI elements and facilities for programs that output simple text and graphics.

![Some text and graphics output windows created with DandeGUI on Medley Interlisp.](https://raw.githubusercontent.com/pamoroso/dandegui/main/dandegui.png)

The library, which is written in and exposes its functionality as Common Lisp, provides windows for stream-based text and graphical output which automaticaly handle repainting, resizing, and scrolling. DandeGUI captures typical GUI patterns of the Medley environment such as printing text to a new window instead of an Exec.


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

The facilities of DandeGUI create read-only, resizable windows with scrollable content and allow to send output to these windows. Any text in text windows may be selected and copied via the usual facilities of the Medley environment.


### Examples

The functions and macros of DandeGUI set up and control windows to which programs can send text or graphical via the Lisp streams associated with the windows.


#### Printing text

Suppose you want to display a table of square roots in a new window. The table contains a row with the headings of two columns, one for a sequence of numbers and the other for the corresponding square roots. The simplest way to do this is to use the DandeGUI macro `WITH-OUTPUT-TO-WINDOW`:

```lisp
(GUI:WITH-OUTPUT-TO-WINDOW (STREAM :TITLE "Table of square roots")
  (FORMAT STREAM "~&Number~40TSquare Root~2%")
  (LOOP
    FOR N FROM 1 TO 30
    DO (FORMAT STREAM "~&~4D~40T~8,4F~%" N (SQRT N))))
```

This code produces one of the windows in the screenshot above. The variable `stream` is bound to the stream associated with a newly created window with title supplied by `:TITLE`. The calls to `FORMAT` populate the table by printing to `STREAM`.


#### Changing text style

The macro `WITH-TEXT-STYLE` lets you change the attributes of the text printed to a window such as the family and face of the font. Let's modify the square root table example to call `WITH-TEXT-STYLE` to print the column heading in a 12 points sans serif bold font:

```lisp
(GUI:WITH-OUTPUT-TO-WINDOW (STREAM :TITLE "Table of square roots")
  (GUI:WITH-TEXT-STYLE (STREAM :FAMILY :SANS :SIZE 12 :FACE :BOLD)
    (FORMAT STREAM "~&Number~40TSquare Root~2%"))
  (LOOP
    FOR N FROM 1 TO 30
    DO (FORMAT STREAM "~&~4D~40T~8,4F~%" N (SQRT N))))
```


#### Using text window streams

Since the bound stream is not accessible outside of the scope of the macro, `WITH-OUTPUT-TO-WINDOW` works best with one-off output you don't need to append to. A combination of `OPEN-WINDOW-STREAM` and `WITH-WINDOW-STREAM` is a better option if instead you generate the output in successive steps; from different parts of the program; or interleave output to more than one window:

```lisp
(DEFUN PRINT-OUTPUT1 (STREAM)
  (GUI:WITH-WINDOW-STREAM (STR STREAM)
    (FORMAT STR "...")
  
    ;; ...
  
    (FORMAT STR "...")))

(DEFUN PRINT-OUTPUT2 (STREAM)
  (GUI:WITH-WINDOW-STREAM (STREAM)
    (FORMAT STR "...")
  
    ;; ...
  
    (FORMAT STR "...")))

(DEFUN DO-SOMETHING-ELSE ()
  ;; ...
  )

(DEFUN MAIN-PROGRAM ()
  (LET ((STREAM (GUI:OPEN-WINDOW-STREAM :TITLE "Output Window")))
    (PRINT-OUTPUT1 STREAM)
    (DO-SOMETHING-ELSE)
    (PRINT-OUTPUT2 STREAM)))

(MAIN-PROGRAM)
```

The function `OPEN-WINDOW-STREAM` creates a new window with title supplied by`:TITLE` and returns the corresponding output stream which output functions can print to. The functions must be wrapped in the context `WITH-WINDOW-STREAM` establishes by binding a variable to the appropriate stream. 

Like any other open stream the value of `OPEN-WINDOW-STREAM` may be closed when no longer in use, for example with `IL:CLOSE`.


#### Drawing graphics

The macro `WITH-GRAPHICS-STREAM` is the equivalent of `WITH-OUTPUT-TO-WINDOW` for graphics: it binds a variable to the stream of a new window to which the graphics operations in the body can send output to. This example uses `WITH-GRAPHICS-STREAM` to draw a pattern of triangular fractals:

```lisp
(GUI:WITH-GRAPHICS-WINDOW (STREAM :TITLE "Fractal Triangles")
  (DOTIMES (X 300)
    (DOTIMES (Y 300)
      (WHEN (ZEROP (MOD (LOGIOR X Y)
                        7))
        (IL:DRAWPOINT X Y NIL STREAM)))))
```


### Demos

The file `GUIDEMO` provides sample code to demonstrate the features of DandeGUI and how to use them.

To run the demos first load the file with `(LOAD 'GUIDEMO)`, which is not necessary to compile. If the compiled file `DANDEGUI.FASL` isn't already loaded `GUIDEMO` will load it assuming it's in the same directory or somewhere in the load path.

`GUIDEMO` exports from the `GUIDEMO` package the following functions which produce the corresponding output in the screenshot.


#### `FRACTAL-TRIANGLES &KEY WIDTH HEIGHT` [function]

Draw a pattern of triangular fractals in a new window. The pattern has a size of `WIDTH` by `HEIGHT` pixels which both have defaults.


#### `RANDOM-CIRCLES &KEY N MAX-R WIDTH HEIGHT` [fnction]

Draw `N` random filled circles in a new window. The circles are filled with a random shade, have a maximum radius `MAX-R`, and are drawn in an area of `WIDTH` by `HEIGHT` pixels. `N`, `MAX-R`, `WIDTH`, and `HEIGHT` have suitable defaults.


#### `SQRT-TABLE &OPTIONAL N` [function]

Displays in a new text window a table of the square roots of the integers up to `N`, or a default value if `N` is not supplied.


### API reference

The functionality of DandeGUI is accessible via the following functions and macros exported from the `DANDEGUI` package nicknamed `GUI`. They operate on Lisp streams of two types, text and graphics.

DandeGUI text output functions and macros operate on Interlisp `TEXTSTREAM` streams to which text may be sent by any printing function that takes a stream as an argument such as with `CL:FORMAT`, `CL:PRIN1`, and so on. However, these functions must be called using the DandeGUI output context macros and not outside ot them.

DandeGUI graphical output facilities operate on Interlisp `IMAGESTREAM` streams to which graphical elements may be drawn by most graphics primitives that take a stream as an argument such `IL:DRAWLINE` and `IL:DRAWPOINT`.


#### Text output

##### `OPEN-WINDOW-STREAM &KEY TITLE` [function]

Opens a new text window and returns the associated output stream. The function sets the window title to `TITLE` if supplied.


##### `WITH-OUTPUT-TO-WINDOW (VAR &KEY TITLE) &BODY BODY` [macro]

Performs the operations in `BODY` with `VAR` bound to a new text window stream.

Creates a new window titled `TITLE` if supplied, binds `VAR` to the stream associated with the window, and executes `BODY` in this context. Returns the value of the last form of `BODY`. After control leaves the context of `WITH-OUTPUT-TO-WINDOW` the new window no longer accepts output to the associated stream.


##### `WITH-TEXT-STYLE (STREAM &KEY FAMILY SIZE FACE) &BODY BODY` [macro]

Performs the operations in `BODY` in a context in which text printed to the text stream `STREAM` is rendered in the style specified by `FAMILY`, `SIZE`, and `FACE`. Uses `*DEFAULT-FONT*` if no matching style is available.

`FAMILY` must be one of `:SERIF` for serif, `:SANS` for sans serif, `:FIX` for fixed width, or a keyword denoting a family name such as `:TIMESROMAN`. `FACE` must be one of `:STANDARD`, `:ITALIC`, `:BOLD`, or `:BOLDITALIC`. Returns the value of the last expression of `BODY`.


##### `WITH-WINDOW-STREAM (VAR STREAM) &BODY BODY` [macro]

Performs the operations in `BODY` with `VAR` bound to a text window `STREAM`.

Evaluates the forms in `BODY` in a context in which `VAR` is bound to `STREAM` which must already exist, then returns the value of the last form of BODY.


#### Graphics output

##### `OPEN-GRAPHICS-STREAM &KEY TITLE` [function]

Opens a new window and returns the associated graphics stream to send output to. Sets the window title to `TITLE` if supplied, to a default title otherwise.


##### `WITH-GRAPHICS-STREAM (VAR STREAM) &BODY BODY` [macro]

Performs the operations in `BODY` with `VAR` bound to the graphics window `STREAM`.

Evaluates the forms in `BODY` in a context in which `VAR` is bound to `STREAM` which must already exist, then returns the value of the last form of `BODY`.


##### `WITH-GRAPHICS-WINDOW (VAR &KEY TITLE) &BODY BODY` [macro]

Perform the operations in `BODY` with `VAR` bound to a new graphics window stream.

Creates a new window titled `TITLE` if supplied, binds `VAR` to the stream associated with the window, and executes `BODY` in this context. Returns the value of the last form of `BODY`. After control leaves the context of `WITH-GRAPHICS-WINDOW` the new window no longer accepts output to the associated stream.


#### Window control

##### `CLEAR-WINDOW STREAM` [function]

Clears the contents of the window associated with `STREAM` and returns `STREAM`. New output will appear at the top left corner of a text window and at the bottom left of a graphics window.

The function does nothing if the argument is not a `TEXTSTREAM` or `IMAGESTREAM`.


##### `*DEFAULT-FONT*` [variable]

The default font of windows, which must be an Interlisp font descriptor or font list such as `(IL:TERMINAL 10)`.


##### `PRINT-MESSAGE STREAM MESSAGE &OPTIONAL DONT-CLEAR-P` [function]

Prints `MESSAGE` to the prompt area of the window associated with `STREAM` if it is a text stream, to the system prompt window otherwise. If `DONT-CLEAR-P` is non `NIL` the area or prompt window will be cleared first. Returns `STREAM`.

To clear the prompt area pass the empty string as the message and `T` as the optional argument, e.g. call `(GUI:PRINT-MESSAGE STREAM "" T)`.


##### `WINDOW-TITLE STREAM` [function]

Returns the title of the window associated with `STREAM` or sets it if called from `SETF`.


## Learn more

* [DandeGUI project updates](https://journal.paoloamoroso.com/tag:DandeGUI)
* [Medley Interlisp](https://interlisp.org)


## Author

DandeGUI is developed by [Paolo Amoroso](https://github.com/pamoroso).


## License

This code is distributed under the MIT license, see the `LICENSE` file.
