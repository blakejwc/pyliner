PYLINER - SCRIPT TO RUN ARBITRARY PYTHON CODE ON THE COMMAND LINE (PYTHON RECIPE)
=================================================================================

A Python script that runs arbitrary Python scripts in an input loop. This allows one-liner Python scripts similarly to how Perl runs them (-l,-a,-n,-F, BEGIN, END)

It provides additional syntax that allows multiline Python scripts to be run on a single line

I work on *nix systems and use one-liner bash+awk+sed+etc.. scripts all the time, but I find I need Perl for some features. But I prefer and remember Python better. There are a few other scripts that allow something like Perl's cool one-liner '-lane', 'BEGIN' ,'END', etc, but don't have the full functionality.

This script is based on http://code.activestate.com/recipes/437932-pyline-a-grep-like-sed-like-command-line-tool/ (Graham Fawcett + Jacob Oscarson, Mark Eichin) and Perl. It attempts to provide all the one-liner functionality that Perl provides. G.F., J.o., and M.E.'s original recipe is now hosted at [437932_Pyline_greplike_sedlike_commandline](https://github.com/ActiveState/code/tree/master/recipes/Python/437932_Pyline_greplike_sedlike_commandline/)

To get this to work, I had to create some additional syntax to allow Python to run on a single line without tabs. I understand this goes against much of what Python 'stands' for, but I'm pragmatic. This is no PEP to modify the language or a critique of Python's syntax; I just like the language and want to use it in an area that it's not well adapted for.

Usage:
------

`pyliner (options) "script"`

  <dl>
    <dt>options</dt>
    <dd><code>-a</code> enables auto-split of input into the <code>F[]</code> array.</dd>
    <dd><code>-F</code> specifies the characters to split on with the <code>-a</code> option (default=" ").</dd>
    <dd><code>-p</code> loops over and prints input.</dd>
    <dd><code>-n</code> loops over and does not print input.</dd>
    <dd><code>-l</code> strips newlines on input, and adds them on output.</dd>
    <dd><code>-M</code> lets you specify module imports.</dd>
    <dd><code>-o</code> print out parsed and indented script on multiple lines.</dd>
    <dd><code>-e</code> (assumed - does nothing, added for Perl compatibility).</dd>
  </dl>
  
  <dl>
    <dt>script</dt>
    <dd><code>";"</code> specify newlines</dd>
    <dd><code>":"</code> will start an indent for the following lines</dd>
    <dd><code>"?;"</code> will add a newline and dedent the following lines</dd>
    <dd><code>"/;"</code> will reset the indent the far left</dd>
    <dd><code>"BEGIN:"</code> starts the an initial script not run in the input loop</dd>
    <dd><code>"END:"</code> starts part of the script run after the input loop</dd>
    <dd><code>"#/"</code> ends both the <code>"BEGIN:"</code> and <code>"END:"</code> portions of the script</dd>
  </dl>

  <dl>
    <dt>variables</dt>
    <dd><code>F[]</code>  = split input</dd>
    <dd><code>line</code> = current input line</dd>
    <dd><code>NR</code>   = current input line number</dd>
  </dl>

  <dl>
    <dt>functions</dt>
    <dd><code>I(x)</code> = function returns <code>F[x]</code> cast to integer</dd>
    <dd><code>f(x)</code> = function returns <code>F[x]</code> cast to float</dd>
  </dl>

Examples
--------

>test.txt:
>```bash
>10,test
>1,rest
>100,test
>10,best
>1000,we
>1,see
>```

Example 1:

>Return the sum of numbers in the first column that are greater than 90:
>```bash
>cat test.txt | ./pyline.py -F"," -lane \
>'BEGIN:sum=0#/if i(0) > 90:sum+=i(0)) END:print sum#/'
>```
>output:
>```bash
>1100
>```

Example 2:

>pyliner
>```bash
>cat test.txt | ./pyliner.py -F"," -lane \
>'BEGIN:sum=0#/a=f(0);if a > 90:sum+=a END:print sum/NR#/'
>```
>output:
>```bash
>183.333333333
>```

>perl:
>```bash
>cat test.txt | perl -F"," -lane \
>'BEGIN {$sum=0; } ;$sum=$sum+=@F[0] if @F[0]>90;END { print $sum/$.; }'
>```
>output:
>```bash
>183.333333333333
>```

Example 3:

>Chunk average (average of every N amount of numbers in a pipe):
>```bash
>echo -en "1\n 2\n 3\n 4\n 10\n 10\n 10\n 10\n" | ./pyliner.py -lane -F"," \
>'BEGIN:s=0;n=4#/s+=f(0);if not NR % n:print s/n;s=0'
>```
>output:
>```bash
>2.5
>10.0
>```

License
=======
Originally created by [Drew Gulino](http://code.activestate.com/recipes/577075-pyliner-script-to-run-arbitrary-python-code-on-the/) at [ActiveState](http://code.activestate.com/recipes/577075-pyliner-script-to-run-arbitrary-python-code-on-the/) and now located at [577075_Pyliner__Script_run_arbitrary_Pythcode](https://github.com/ActiveState/code/tree/master/recipes/Python/577075_Pyliner__Script_run_arbitrary_Pythcode)

This program is licensed  under the GNU V3 license. See [LICENSE.md](LICENSE.md) for the full text.
