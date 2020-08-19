# Examples of generating Canvas "quizzes" using Markdown

This repo gives several examples of Geoffrey Poore's [text2qti](https://github.com/gpoore/text2qti) to generate [Canvas](https://www.instructure.com/canvas/) quizzes using [Markdown](https://en.wikipedia.org/wiki/Markdown).

The advantages of this approach are:

* You can get Canvas' intrusive UI out of your way
* Your questions are now plain text, meaning that you can version control them
  using [GitHub](https://github.com), [bitbucket](https://bitbucket.org), etc..
* You can collaborate on questions just as you would for regular source code
  hosted on [GitHub](https://github.com) or [bitbucket](https://bitbucket.org).
* You can use `GitHub`/`bitbucket` releases to create "version numbers"
  for each year.
* You can *finally* write questions with random data and have the
  answers validated on the fly.  Here, we
  can use [python](https://python.org) to generate questions and
  the answers.  Thus, there should only be one point of failure,
  which is the code to generate the answers.
  This approach lets you generate dozens or hundreds of permutations
  of a single question!
* You can use Python to generate graphics for the questions.
* For many faculty and students, we can leverage skills we've already developed
  elsewhere: Python programming, content generation via markdown, and using version
  control software are daily activities for many of us.

The disadvantages are:

* You need to learn `markdown`.  Fortunately, that is relatively rather easy.
  In fact, `Rstudio` users, etc., will already know the basics here.
* Knowledge of LaTeX and Python add considerable value.

## General comments

* Please read the `README` file for `text2qti`, which is [here](https://github.com/gpoore/text2qti) 
* The README is missing a key point, which is that the  code
  fence probably needs to say `{.python3 .run}` for most people
  most of the time.
* You need a *text editor* to edit questions.  It doesn't matter which one,
  but you need to be editing in **plain** text. (Not `.doc/docx`, not `.rtf`!)
* It is a good time to think about adding copyright information to all
  of your `Canvas` questions.  At `UCI`, we have a serious problem with
  our course material being remixed and resold back to students by
  a company.  I am adding explicit copyright info to each and every
  question. (A good text editor helps to automate this!)
* If you generate graphics with commercial software, then you'll need
  to export to `png` or `jpeg`.  See below for alternatives.
* Always check the formatting of your work!  One way is to do 
  a "dummy" upload to Canvas. (Pretty fast to do.)  You
  can also unzip the `QTI zip` file and inspect the images
  manually.

## Python dependencies

The Python dependencies are listed in `reuirements.txt`.  Install them via:

```sh
pip3 install -r requirements.txt
```

## Other dependencies

* LaTeX.  For `macOS` users, install [MacTex](https://tug.org/mactex/).
* [ImageMagick](https://imagemagick.org/index.php)
* [pandoc](https://pandoc.org)

From the above list, only `pandoc` is truly required.  The others are used for
specific example questions:

* LaTeX is used to render tables as `pdf`. (One could also generate graphics
  directly using [tikz](https://texample.net/tikz/).)
* The `convert` tool from `ImageMagick` takes the `pdf` output from LaTeX
  and generates a `png` file, which is used in the question.
  
### How to install these

#### Linux

They are available via your distro's package manager

#### macOS/Windows

I recommend [conda](https://docs.conda.io/en/latest/).  Windows 10 users
can probably use a Linux installation of `conda` into the
`bash/Ubuntu` subsystem.

### Gotchas

* On some systems, you may get an error when trying to use `convert`
  to manipulate `pdf` files.  This error is a safety check because
  of the potential security issues involving executing malicious code.
  This [SO post](https://stackoverflow.com/questions/52998331/imagemagick-security-policy-pdf-blocking-conversion) has advice on how to undo this check.
  (I have to do all of this on each of my Linux machines.)

## Building the quizzes

For each `markdown` file, execute

```sh
text2qti --run-code-blocks --pandoc-mathml foo.md
```

Note that `foo.md` is a placeholder.  Your actual quiz will have a name
like `Lecture1.md` and the quiz names provided here are visible in `quizzes/`.

One a `Unix`-like system (`Linux`, `macOS`, or `Windows 10` with the `bash`
subsystem), the `Makefile` will build the examples in `quizzes`:

```
cd quizzes
make
```

### Gotchas

* The builds are messy.  A lot of extra files end up in `quizzes/`.  I need
  to see if this can be fixed.
* You **cannot** do parallel builds! (`make -j 4`, etc.).  The system
  runs through an intermediate file with a fixed name.

## Image generation with Inkscape and krita

[Inkscape](https://inkscape.org) and [krita](https://krita.org) are two
open source programs for creating images.  The former is a vector
graphics package, works through `svg`
files and is well-suited for technical diagrams.  The latter is a very
capable drawing program.  As with their Adobe counterparts, both have
fairly steep learning curves.  One very nice feature is that both 
provide command line interfaces for exporting to standard graphics
formats.  The following show the terminal commands and the corresponding
`Makefile` rules for converting `svg` and `krita` files to `png`.

For `inkscape` `svg`, the terminal command is

```sh
inkscape --export-png image.png -D image.svg
```

The corresponding `make` rule is:

```sh
%.png: %.svg
    inkscape --export-png $@ -D $<
```

For `krita`, we have:

```sh
krita --export image.kra --export-filename image.png
```

```sh
%.png: %.kra
    krita --export $@ --export-filename $<
```

Both programs have more options to control the output `dpi`, etc.,
and `convert` can be used for resizing, etc..

The advantage of this approach is that you only ever need to store your
`inkscape` and `krita` files. (Both formats are not very `git`-friendly, however.)

## Uploading to Canvas

`Settings -> Upload Course Content` and probably create a new question bank.
Remember to "bookmark" it if it is uploaded to a "sandbox" course.
You'll also need to burn sage or perform a sacrifice of some sort.
