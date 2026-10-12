---
tags:
  - cs301
  - java
  - stdlib
  - input-output
  - graphics
aliases:
  - StdIn
  - StdOut
  - StdDraw
  - Standard IO
date_created: 2026-10-11
---

# Standard I/O & Graphics: StdIn, StdOut, StdDraw

The Princeton standard libraries (`stdlib.jar` / `introcs`) provide unified, beginner-friendly tools for standard input, terminal output, and 2D canvas drawing. They bridge the gap between command-line execution and interactive programs.

> [!info] Relation to Command-Line Arguments
> While `args[]` receives input **at program startup** (see [[Command Line Arguments]]), `StdIn` allows your program to receive data **interactively** or through continuous streams while the program is running.

---

## 1. `StdIn` (Standard Input)

`StdIn` reads tokens, numbers, and strings from standard input (keyboard, redirected text files, or pipeline streams).

### Method Summary

| Method | Return Type | Description |
| :--- | :--- | :--- |
| `StdIn.readInt()` | `int` | Reads next integer token (skips whitespace). |
| `StdIn.readDouble()` | `double` | Reads next floating-point token. |
| `StdIn.readBoolean()` | `boolean` | Reads `true`, `false`, `1`, or `0`. |
| `StdIn.readString()` | `String` | Reads next whitespace-delimited word. |
| `StdIn.readLine()` | `String` | Reads remainder of current line up to newline. |
| `StdIn.readAll()` | `String` | Reads entire remaining input stream as one `String`. |
| `StdIn.readAllInts()` | `int[]` | Reads all remaining integers into an array. |
| `StdIn.readAllDoubles()` | `double[]` | Reads all remaining doubles into an array. |
| `StdIn.readAllStrings()` | `String[]` | Reads all remaining words into an array. |
| `StdIn.isEmpty()` | `boolean` | Returns `true` if no non-whitespace input remains. |

### Common Idioms

#### Fixed Count ($N$ Inputs)
```java
int n = Integer.parseInt(args[0]);
double[] data = new double[n];

for (int i = 0; i < n; i++) {
    data[i] = StdIn.readDouble();
}
```

#### Indefinite Stream (Reading until EOF)
```java
while (!StdIn.isEmpty()) {
    double value = StdIn.readDouble();
    // Process each value as it arrives
}
```

> [!tip] Sending EOF (End of File) in Terminal
> When entering values by hand in the terminal, you signal EOF using:
> - **Windows:** `Ctrl + Z` followed by `Enter`
> - **macOS / Linux:** `Ctrl + D`

---

## 2. `StdOut` (Standard Output)

`StdOut` provides standard printing capabilities and formatted output compatible with pipeline redirection.

### Core Methods

| Method | Description |
| :--- | :--- |
| `StdOut.print(...)` | Prints argument without a trailing newline. |
| `StdOut.println(...)` | Prints argument followed by a newline. |
| `StdOut.println()` | Prints an empty newline. |
| `StdOut.printf(format, ...)` | Prints formatted output using format specifiers. |

### `printf` Format Specifiers

```java
StdOut.printf("Pi is roughly %7.2f\n", Math.PI);
// Result: "Pi is roughly    3.14"
```

| Specifier | Data Type | Example | Output |
| :--- | :--- | :--- | :--- |
| `%d` | Integer | `StdOut.printf("%5d", 42)` | `   42` (width 5, right-aligned) |
| `%f` | Floating-point | `StdOut.printf("%.2f", 3.14159)` | `3.14` (2 decimal places) |
| `%e` | Scientific | `StdOut.printf("%e", 1000.0)` | `1.000000e+03` |
| `%s` | String | `StdOut.printf("%-10s", "Hi")` | `Hi        ` (width 10, left-aligned) |
| `%n` | Platform Newline | `StdOut.printf("Done%n")` | Moves cursor to next line |

---

## 3. `StdDraw` (2D Canvas Graphics)

By default, `StdDraw` sets up a $512 \times 512$ pixel canvas bounded by the **unit square** $[0.0, 1.0] \times [0.0, 1.0]$. The origin $(0, 0)$ is at the **bottom-left corner**.

```
(0, 1) +------------------------+ (1, 1)
       |                        |
       |     Default Canvas     |
       |       [0,1] x [0,1]    |
       |                        |
(0, 0) +------------------------+ (1, 0)
```

### Canvas & Coordinate Settings

```java
StdDraw.setCanvasSize(800, 600); // Set canvas dimensions in pixels (width, height)
StdDraw.setXscale(0, 100);       // Rescale x-axis bounds
StdDraw.setYscale(-50, 50);      // Rescale y-axis bounds
```

### Geometric Primitives

| Method | Parameters & Details |
| :--- | :--- |
| `StdDraw.point(x, y)` | Draws a single point at $(x, y)$. |
| `StdDraw.line(x0, y0, x1, y1)` | Draws line segment from $(x0, y0)$ to $(x1, y1)$. |
| `StdDraw.circle(x, y, r)` | Outlined circle centered at $(x, y)$ with radius $r$. |
| `StdDraw.filledCircle(x, y, r)` | Solid circle centered at $(x, y)$ with radius $r$. |
| `StdDraw.square(x, y, halfLength)` | Outlined square with side length $2 \times halfLength$. |
| `StdDraw.filledSquare(x, y, halfLength)` | Solid filled square. |
| `StdDraw.rectangle(x, y, halfW, halfH)` | Outlined rectangle (centered, dimensions $2 \times halfW$ by $2 \times halfH$). |
| `StdDraw.filledRectangle(x, y, halfW, halfH)` | Solid filled rectangle. |
| `StdDraw.polygon(x[], y[])` | Outlined polygon defined by coordinates in parallel arrays. |
| `StdDraw.filledPolygon(x[], y[])` | Solid filled polygon. |
| `StdDraw.text(x, y, "label")` | Centers text at $(x, y)$. |
| `StdDraw.textLeft(x, y, "label")` | Left-aligns text starting at $(x, y)$. |
| `StdDraw.picture(x, y, "file.png")` | Draws an image file centered at $(x, y)$. |

### Pen & Color Settings

```java
// Pen line thickness (default is 0.002)
StdDraw.setPenRadius(0.01);

// Built-in Colors
StdDraw.setPenColor(StdDraw.BLACK);
StdDraw.setPenColor(StdDraw.WHITE);
StdDraw.setPenColor(StdDraw.RED);
StdDraw.setPenColor(StdDraw.BOOK_LIGHT_BLUE);

// Custom RGB (0 to 255)
StdDraw.setPenColor(new java.awt.Color(128, 0, 255));
```

### Animation & Double Buffering Template

> [!warning] Prevent Flickering
> Without double buffering, drawing each element clears or refreshes parts of the screen in real-time, causing flickering. Always use `enableDoubleBuffering()`, draw offscreen, and call `show()` once per frame.

```java
// 1. Turn on double buffering before starting loop
StdDraw.enableDoubleBuffering();

double x = 0.0;
double speed = 0.01;

while (true) {
    // 2. Clear previous frame
    StdDraw.clear();

    // 3. Draw scene objects
    StdDraw.filledCircle(x, 0.5, 0.05);

    // 4. Push finished buffer to display & delay
    StdDraw.show();
    StdDraw.pause(20); // ~50 FPS (20 ms pause)

    // 5. Update state for next tick
    x += speed;
    if (x > 1.0) {
        x = 0.0; // Wrap back around
    }
}
```

---

## 4. Terminal Redirection & Piping Cheat Sheet

These standard libraries interact cleanly with command-line streams:

| Syntax | Description | Example | Effect |
| :--- | :--- | :--- | :--- |
| `< file` | Input Redirection | `java Stats 100 < data.txt` | Reads `StdIn` directly from `data.txt`. |
| `> file` | Output Redirection | `java Generator 100 > data.txt` | Writes `StdOut` to `data.txt` (overwrites existing file). |
| `>> file` | Append Output | `java Generator 100 >> log.txt` | Appends `StdOut` to the end of `log.txt`. |
| `\|` | Piping | `java Generator 100 \| java Stats 100` | Streams `StdOut` of program 1 into `StdIn` of program 2. |
