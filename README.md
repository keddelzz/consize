# Consize

> If you are interested in doing some research on concatenative programming, see my announcement (in German): [Compiler-Optimierung durch partielle Interpretation umgesetzt für die konkatenative Sprache Consize](research/PartialInterpretation.Topic.md); this might result in a beautiful master thesis ;-) 

Consize is a concatenative programming language. It is a close relative to the [Factor programming language](https://factorcode.org/). The word "Consize" is intentionally misspelled, it is a combination of the words "concatenative" and "size". Concatenative programs tend to be small in size and concise. 

I designed Consize for research purposes and for educational purposes. For example, Consize is used in my master course "Kernel Architectures in Programming Languages" (Kernel-Architekturen in Programmiersprachen) at the Department of Mathematics, Natural Sciences and Informatics, University of Applied Sciences Mittelhessen (Technische Hochschule Mittelhessen), Germany.

Consize comes with comprehensive documentation explaining the concatenative paradigm, the interpreter and the built-in library, the so called _prelude_. The documentation is written in German, since my German students are my primary target audience. Though English is the _lingua franca_ in informatics, it is a barrier to learn new concepts of an uncommon programming paradigm in a foreign language. Nonetheless, I might consider rewriting the documentation in English.

I feel always thrilled by the fact that the interpreter is not more than 150 lines of code written in [Clojure](https://clojure.org). Basically, Consize is written in Consize and bootstrapped from a small image, which has also been produced by and with Consize.

Regarding conciseness: Consize has numerous words for functional programming, most words being composed of one, two or three lines of code; Consize comes with a unit test framework implemented in eight lines of code; you can set breakpoints and step through the code for debugging purposes implemented in three lines of code; serialization and producing image dumps comes also in some few lines. Do you have an interest in meta-programming? Not only has Consize a meta-protocol to handle unknown words, but _continuations_ which allow you to manipulate the future of computations and is used to implement an _ad hoc_ parser to extend the grammar of Consize.

Whet your appetite? You might head over reading the [documentation](/doc/Consize.pdf) first.

Enjoy,

Dominikus Herzberg, [@denkspuren](https://twitter.com/denkspuren)

## Run Consize

Running Consize requires a Java runtime environment and Clojure. Here, I assume that you have installed Clojure 1.8.0 or higher on your system.

~~~
>java -cp clojure-1.8.0-slim.jar clojure.main consize.clj "\ prelude.txt run say-hi"
~~~

Alternatively, you might also type

~~~
>java -jar clojure-1.8.0-slim.jar consize.clj "\ prelude-plain.txt run say-hi"
~~~

You can also call Consize with a plain version of the prelude called `prelude-plain.txt`. This version will be a little bit slower at startup time than `prelude.txt`.

Be patient, wait for a moment -- and Consize shows up with

~~~
This is Consize -- A Concatenative Programming Language
>
~~~

For example, type in

~~~
> 2 3 +
5
> clear

> 2 5 [ 1 + ] bi@
3 6
clear
~~~

To define the factorial function:

~~~
> : ! ( n -- n! ) dup 0 equal? [ drop 1 ] [ dup 1 - ! * ] if ;

> 4 !
24
> 6 !
24 720
~~~

Run the suite of unit tests to check if everything works as expected.

~~~
\ prelude-test.txt run
~~~

## How to Extract the Prelude from the Documentation?

The prelude is a Consize program extending Consize considerably for practical use. Without the prelude there is no interactive console to interact with (called the REPL, _Read Evaluate Print Loop_), there are no means to define new words etc. You wouldn't have much fun with Consize without the prelude.

The prelude is written as a _literate program_ (see [literate programming](https://en.wikipedia.org/wiki/Literate_programming)), that means the code is embedded in its documentation. Literate programming is a technique to keep the documentation and the code in sync.

Have a look at [Consize.Prelude.tex](/doc/Consize.Prelude.tex) and watch out for lines starting with either `>>` oder `%>>`; the `%`-sign marks a comment in LaTeX. All these lines make up the prelude (with the marking prefix removed).

To extract the prelude from the documentation, you do not need a special program, Consize has the word `undocument` for that.

> There is no need to extract the prelude if you are not interested in updating and/or extending Consize. Consize is delivered with "batteries included", i.e. with a prelude and a tiny bootimage. So you might stop here and just enjoy running and using Consize.

Run Consize and type the following in the REPL; the word `slurp` reads a file, the word `spit` writes a file.

~~~
> \ ../doc/Consize.Prelude.tex slurp undocument \ new.prelude-plain.txt spit
~~~

You will find a file named `new.prelude-plain.txt` in your `/src` directory. You can restart Consize with the new prelude. Leave Consize with entering `exit` and restart Consize on the command line interface:

~~~
>java -cp clojure-1.8.0-slim.jar clojure.main consize.clj "\ new.prelude-plain.txt run say-hi"
~~~

To produce an image of the current status of the dictionary, type

~~~
This is Consize -- A Concatenative Programming Language
> get-dict \ new.prelude-dump.txt dump
~~~

The image file `new.prelude-dump.txt` loads faster than the plain source code file `new.prelude-plain.txt`. The reason is that the source code requires syntactic preprocessing to fill the dictionary with word definitions whereas the image file just reconstructs the dictionary.

It's a good idea to verify whether something is broken. Run the test suite with each version of the Prelude, `new.prelude-plain.txt` and `new.prelude-dump.txt`. Start each version separately, run the tests, exit for testing the other version.

~~~
>java -cp clojure-1.8.0-slim.jar clojure.main consize.clj "\ new.prelude-plain.txt run say-hi"
This is Consize -- A Concatenative Programming Language
> \ prelude-test.txt run
~~~

~~~
>java -cp clojure-1.8.0-slim.jar clojure.main consize.clj "\ new.prelude-dump.txt run say-hi"
This is Consize -- A Concatenative Programming Language
> \ prelude-test.txt run
~~~

If you want to, you can also generate `bootimage.txt`. The bootimage is a dump of a minimalistic directory that includes all the word definitions required to load the prelude. To produce the bootimage type the following in the REPL; take care, the current version of the bootimage gets overwritten by that -- you might create a copy beforehand.

~~~
> bootimage
~~~

After that, run both versions of Consize again (plain file and image file) and run the test suite one more time. If there are no problems, you are almost done.

Delete the old versions of the prelude and rename the ones you have created:

* rename `new.prelude-plain.txt` to `prelude-plain.txt`
* rename `new.prelude-dump.txt` to `prelude.txt`

Congratulations, you are done!

By the way, did you notice that we had to use a running version of the prelude and the bootimage to extract a new version of the prelude from the documentation and generate a fresh bootimage afterwards? This is a typical process for image-based self-referential implementations. Otherwise you would have to extract the prelude by hand and to generate the bootimage by hand -- which I did for the very first incarnation of the bootimage.

## Generate the Documentation

> If you have no interest in updating the documentation, skip this section!

To produce the PDF document yourself, install a TeX distribution on your computer such as [MikTeX](https://miktex.org/) for Windows. Start the compilation process with `Consize.tex`. On Windows you might use `TeXworks` for that purpose, which is released with MikTeX.

