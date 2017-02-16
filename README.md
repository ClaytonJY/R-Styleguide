# R-Styleguide
This styleguide gives guidelines for structuring and formatting R code to improve readability and interoperability with others.

It is based on [Google's R Styleguide](https://google-styleguide.googlecode.com/svn/trunk/Rguide.xml) and [Hadley's Advanced-R Styleguide](http://adv-r.had.co.nz/Style.html); many thanks to Hadley and the Google team.

# The Golden Rule
The most important guideline is _**be consistent**_! Some of the guidelines below leave the reader with options; pick whichever option you like, but strive to be consistent within a file or project. Consistency is an especially important consideration when modifying older code; if it uses outdated conventions, it is better to mirror those than to have two different styles in the same project.

Almost every guidelines can have exceptions. Common ones are listed below; any others should be clearly justified.


# Notation and Naming
## File & Directory Names
R files and associated directories should have meaningful names, lowercase letters, and words should be separated by dashes. R files should end in `.R` (not `.r`).

```r
GOOD:
timeseries-analysis.R
process-compression-ratios.R
dhs-data-cleaning/

BAD:
timeseries_analysis.R
TimeseriesAnalysis.R
timeseries-analysis.r
process_compression-ratios.R
foobar.R
DHS_Data_Cleaning/
DHS-Data-Cleaning/
DHSDataCleaning/
dhsDataCleaning/
```

**Exceptions**:

- There can be not-too-descriptive filenames like `functions.R` (in Shiny apps); these are okay if there is a strong expectation as to what they contain.

## Function Names
Function names should be descriptive verbs, with the first letter of each word capitalized, and no separators between words. They should not overload functions or variable names from base R or packages being used.

```r
GOOD:
MakeChart()
GetRatioDiscrepancies()

BAD:
makechart()
makeChart()
Get_Ratio_Discrepancies()
Get.Ratio.Discrepancies()
func()
mean()
```

**Exceptions**:

- In a package, function names should still be descriptive verbs, but use only lower-case letters, and separate words with underscores, e.g. `make_chart()`. This matches how most package authors name functions, and allows the non-package functions to clearly stick out.


## Variable Names
Variable names should be descriptive, lower-case nouns, with underscores to separate words. They should not overload function or variable names from base R or packages being used. Periods should be reserved for method dispatching with R's OO systems. In `data.frame`'s, append `_uncleaned` to the name of uncleaned variables for clarity.

```r
GOOD:
max_date
bad_entries
some_measure_uncleaned

BAD:
maxDate
max-date
max.date
BadEntries
badEntries
someMeasureX
some_measure_raw
c
```

**Exceptions**:

- Many older projects use periods as word separators, but the broader R community had moved away from this standard
- Common practice is to refer to the primary `data.frame` with `df`; that's standard and acceptable.


# Syntax

## Indentation
Always use two spaces per level of indentation. _Never_ use literal tabs (note that RStudio will use spaces when the tab key is pressed by default, and most editors can be configured to do the same).
When function calls or definitions span multiple lines, either align subsequent lines with the first character inside the parentheses, or place each clause inside the parentheses on its own line with an additional level of indentation and place the closing parenthesis on its own line.

```r
GOOD:
stopifnot(is_character(a),
          is_logical(b))

stopifnot(
  is_character(a),
  is_logical(b)
)

foobar <- function(a = "foobarbaz",
                   b = "FooBarBaz")

foobar <- function(
  a = "foobarbaz",
  b = "FooBarBaz"
)


BAD:
stopifnot(
          is_character(a),
          is_logical(b)
)

foobar <- function(a = "foobarbaz",
  b = "FooBarBaz")
```

## Spacing
Place spaces around binary/infix operators, after commas, and before parentheses not part of a function call.
Never put spaces before a comma, around colons (e.g. `::`), or around code inside brackets/parentheses.
Use extra spaces to vertically align similar lines of code along operators.

```r
GOOD:
x <- y + z
x %in% c(y, z)
if (x == TRUE) y <- z
rCharts::renderChart2()
x <- foobar(a = "foo", b = "bar")

foo    <- foo    + x
foobar <- foobar + x


BAD:
x<-y+z
x%in%c(y, z)
x %in% c(y , z)
x %in% c( y, z )
if(x == TRUE) y <- z
if (x==TRUE) y <- z
if (x == TRUE) y<-z
rCharts :: renderChart2()
x <- foobar(a="foo", b="bar")
x <- foobar(a = "foo", b="bar")

foo <- foo + x
foobar <- foobar + x
```

## Curly Braces
An opening curly brace is never on it's own line, but a closing curly brace is always on it's own line (except when followed by `else`) and followed by a newline.
Short `if` statements can omit curly braces if they are confined to one line.
Indent code inside of curly braces.

```r
GOOD:
if (foo == "foo") {
    bar <- "bar"
}

if (foo == "foo") {
    bar <- "bar"
} else {
    bar <- "foobar"
}

if (foo == "foo") bar <- "bar"

BAD:
if (foo == "foo") {
    bar <- "bar"}

if (foo == "foo")
{
    bar <- "bar"
}

if (foo == "foo") {bar <- "bar"}

if (foo == "foo")
  bar <- "bar"

if (foo == "foo") {
    bar <- "bar"
} else {bar <- "foobar"}

if (foo == "foo") {
bar <- "bar"
}
```

## Assignment
Use `<-`, not `=`, when assigning values to names. Reserve `=` for argument-passing in function calls.

```r
GOOD:
foo <- "bar"
foobar <- function(a="foo", b="bar")

BAD:
foo = "bar"
foobar = function(a<-"foo", b<-"bar")
```

## Line Length
We don't adhere to a strict limit, but 120 is a good rule of thumb. Long lines can always be broken into multiple lines or commands, which is much more readable.


# Organization
## General Layout
Each *.R file should begin by loading any libraries required by the code therein; see the above section on Package Loading. Functions should not load libraries within themselves! If different functions in a file have very different dependencies, it might make sense to separate them into different files.

Files should always end with a newline, and have no trailing whitespace at the end of any line (including empty lines within indented blocks). Your editor of choice can probably help you enforce these guidelines.

When writing a Shiny application, always keep `server.R` and `ui.R` as separate files, as it clarifies the separation between front- and back-end code. Use `global.R` (not `server.R`) for loading data and defining global variables like directory paths.

## Comments
Always comment your code! We prefer too-many to too-few comments. Comments should be denoted with a single pound sign followed by a space and an un-capitalized sentence.

Organize your code into blocks which accomplish a single goal, and describe that goal in a prefacing comment following a newline:

```r
var <- "last line of last codeblock"

# cleanup and order the available columns
setnames(dt, "value", "size")
output.cols <- c("timestamp", "leapserv_id", "model_family", "region_name", "database", "size")
bad.cols <- setdiff(names(dt), output.cols)
dt[, (bad.cols) := NULL]
setcolorder(dt, output.cols)
```

Use in-line comments sparingly:

```r
dt.list <- lapply(sheet.names, function(s) setDT(read_excel(xlsx.file, sheet=s, skip=2)))  # first 2 rows are junk
```

You can also use comments to group blocks into larger chunks. These comments should begin with a pounds sign and four dashes, then have a description with first letters capitalized (like a title), followed by four more dashes. They should be preceeded by 2 newlines and followed by one.

```r
# ---- Load data ----

# load some dataset
...
...

# load some other dataset
...
...


# ---- Plot data ----

# plot some stuff
...
```

## Function Documentation
Every function must be prefaced by a special set of comments which describe and explain the function. **Another user should be able to use your function by reading only these comments**, ignoring the code entirely.

There are 3 or 4 sections to a "function doc" comment:

- one-sentence description of the function
- longer explanation of function mechanics (not always necessary)
- list of arguments with explanation, denoted by `Args:`
- description of return value, denoted by `Returns:`

Here's an example:

```r
# Create a table which gives data size by timestamp, leap, and database
#
# This function reads in all the timestamped files, puts them together, converts some columns, removes some columns, and imposes logical columns names + ordering.
# It also filters out non-primary models families, and entries with a zero or unavailable size.
#
# Args:
#   directory: (character) a path to the directory with the raw CSV's
#   prefix:    (character) the prefix all the relevant files in `directory` share
#
# Returns:
#   A data.table object with columns (timestamp, leapserv_id, model_family, region_name, database, size) and a row for each database type in each leap found in each collection.
#   Keyed on (timestamp, model_family, region_name) for faster filtration + aggregation
GetMasterTable <- function(directory, prefix) {
    ...
}
```

Notice that the arguments' classes/types are listed in parentheses. We don't have a formal standard for these descriptors ("string" and "character" are both common), but the point is to give indication of what kinds of objects are valid.

Also notice the indentation, line breaks, and vertical alignment used.

**Exception:**
When writing a package, use the `Roxygen2` package for function documentation rather than the above style.
