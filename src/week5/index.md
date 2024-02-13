# Week 5 – File Exploration and Text Analysis from the Command Line

## Lecture Materials

- [Monday Lecture Handout (Slides)](https://docs.google.com/presentation/d/12_-kvpyzGMU5LaTJgjQMxbRoZhN3P9Ly/edit?usp=sharing&ouid=109342588918218787603&rtpof=true&sd=true)
- [Monday Lecture Handout (PDF)](https://drive.google.com/file/d/15jPpueKIIctNBL1dgkXj6TEyQdtpIXiU/view?usp=sharing)
- [Wednesday Lecture Handout (Slides)](https://docs.google.com/presentation/d/12WAVMCkyDjOYnLK3g4VF5QXEfJ6zjgq0/edit?usp=sharing&ouid=109342588918218787603&rtpof=true&sd=true)
- [Wednesday Lecture Handout (PDF)](https://drive.google.com/file/d/1-q-2oqlWWyC5_xm8Uq3oxBJ6_1U7D__3/view?usp=sharing)
- Monday Notes <iframe src="https://drive.google.com/file/d/13V_dJ3paNNeYrm55sfxWPRxCnansDm3i/preview" width="100%" height="600px"></iframe>
- Wednesday Notes <iframe src="https://drive.google.com/file/d/1YJTx58-PVt4mDiV8jiqKLnBBpA0ll6Nh/preview" width="100%" height="600px"></iframe>

## Lab Tasks

As usual, we publish these ahead of time, but they aren't guaranteed to be final
until the start of lab on Wednesday.

Discuss with your group:
    
![Image](../../images/mouse_escape_vehicle.png)
    
Write down your answers (and why you chose them!) in your group's shared doc.
    

In this lab you'll work with scripts to do several tasks, and explore programs
that do a _recursive traversal_ of directories.

To get started, fork and clone this repository:


[docsearch](https://github.com/ucsd-cse15l-s23/docsearch)

The `technical/` directory is a sample of writing in English from
[https://anc.org/data/oanc/download/](https://anc.org/data/oanc/download/), a
free and open corpus of English text samples. We'll use it as sample data to
explore how to search through files. We'll do two main tasks:

1. Answer several questions about the dataset by using command-line tools and
bash scripts
2. Write a web server that can respond to queries for files within this
directory

### Answering Questions about Text Files

In this section we'll use a few different command-line tools to build scripts
that can answer interesting questions about these text files – they'd work on
any directories containing plain text files! We'll also generally get practice
with using tools purely from the command-line.

#### Counting Text Files

First question: **How many text files (files ending in .txt) are there?** We'll
walk through this together.

First, let's try the `find` command. `find` will take a directory path as an
argument and list files and directories inside that directory. Try using

```
find technical/
```

What do you see? (If your local computer is Windows, make sure you have a
`bash` terminal open!)

That's a lot of files, and all that output kind of takes over the terminal!

One really useful thing we can do with _any_ command is use **output
redirection** to put whatever would be printed into a file. Then we can process
that file with other commands. The `>` character does output redirection in
bash. Try:

```
find technical/ > find-results.txt
```

What do you see? Nothing, right? Do `ls` and you'll see that `find-results.txt`
has been created in the current directory. You can use `cat` on it and see the
long listing of all the files and directories.

Sometimes we want to explore a file at the command line (because we're on the
remote), and we don't want the long output from `cat`. Another command, called
`less`, is really good for this. Try:

```
less find-results.txt
```

This will “take over” your terminal with just the first screenful of lines. You
can press `q` to exit out of `less` and get back to the normal terminal (try it,
then restart `less`). You can scroll up and down using the up and down arrows,
and go down by a screen at a time by using the space bar. `less` is a great way
to quickly check the contents of a file when you don't have a convenient visual
editor (like VScode) to use to explore it.

OK, so we can confirm that this file that we've made `find-results.txt`, has a
bunch of lines and each line is a path. Let's get back to our question:

**How many text files are there?**

There are a few ways we could do this. Since we'd (eventually) like an answer
that works in a script, it would be useful to find a _command_ that does this,
rather than, say, counting them by hand or using the line number in a text
editor. That leads us to introduce one more command, `wc`, which stands for
“word count”. `wc` takes a path and prints out some information about that file.

Try this:

```
wc find-results.txt
```

You'll see output that looks something like this:

```
    1402     1402   54468 find-results.txt
```

The first is the number of _lines_ in the file. The second is the number of
_words_ (`wc` uses a pretty simple definition of words – strings separated by
whitespace; since the paths don't have spaces, each counts as one word). The
third is the number of _characters_ in the file.

Since there's one line per path, it seems like 1402 is our answer. We used a few
commands and concepts to get here:

- `find «directory-path»`, which searches (recursively) in a directory for files
and lists them all
- `less «file-path»`, which helps explore files from the command line
- `wc «file-path»`, which counts words in a file
- `«any-command» > «a-file»`, which isn't a command, but we can put after a
command to _redirect_ its output to a file

**Write down in notes**: Show screenshots of using the above commands to get to
this answer. Are you sure it's the right answer? How do you know? Can you see
anything that might be inconsistent about that answer when you use `less`?

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

Turns out this answer (1402) is wrong. You might say it's only a _little bit_
wrong, but it's still not right! It's wrong because `find` includes all of the
_directory_ names as well as the file names. (It would also be wrong if there
were non-`.txt` files in the directory structure – are there any?)

There are a lot of ways we can do this—I encourage you to do a web search for
the `-name` and `-type` options for `find`—we will use it as an excuse to
introduce one more really cool command: `grep`.

At its simplest, `grep` takes a string and a file, and prints out all the lines
in that file that match the string. Try:

```
grep ".txt" find-results.txt
```

Then, let's store the results in a file so we can work with them:

```
grep ".txt" find-results.txt > grep-results.txt
```

The, use `wc` to check the line count in this new file (you try that yourself!)

**Write down in notes**: What's the actual count of `.txt` files?

### Putting it Into a Script

That's a lot of exploration at the terminal! It's useful to also consider how to
turn this into a _script_ that prints the answers. Let's see what that might
look like. We can put the commands in a row in a file called `count-txts.sh`:

```
find technical > find-results.txt
grep ".txt" find-results.txt > grep-results.txt
wc grep-results.txt
```

Then we can run it with `count-txts.sh`.

```
$ bash count-txts.sh
    1391     1391   54178 grep-results.txt
```

**Write down in notes**: Show putting this into a script and running it to get
this answer.

Sometimes it's useful to _parameterize_ a script with command line arguments.
Make it so this script takes the name of the directory to traverse as the first
command-line argument, so you use it like this instead:

```
bash count-txts.sh technical
```

Then, use it to count the number of files in some of the subdirectories like
`biomed` and `plos`.

**Write down in notes**: How many files are in those directories?

**Write down in notes**: What happens to the `find-results.txt` and
`grep-results.txt` files when you run the script? What are some consequences of
that for where you should be careful when using output redirection?

#### Counting Sizes of Text Files

Here's another question that would be nice to answer: **How many total words
are in the files in `technical/biomed`?**

For this, it would be nice to be able to use `wc` on all the files in that
directory.  `wc` can take multiple filenames. For example, we could give two
paths, and `wc` will tell us the number of lines, words, and characters in each:

```
$ wc technical/biomed/1468-6708-3-1.txt technical/biomed/1468-6708-3-3.txt 
     432    3380   24112 technical/biomed/1468-6708-3-1.txt
     296    2166   16882 technical/biomed/1468-6708-3-3.txt
     728    5546   40994 total
```

We can use a `*` pattern to make `wc` work on _all_ the files in that directory:

```
$ wc technical/biomed/*.txt
     432    3380   24112 technical/biomed/1468-6708-3-1.txt
     296    2166   16882 technical/biomed/1468-6708-3-3.txt
     547    4301   31378 technical/biomed/1468-6708-3-4.txt
     317    2312   18114 technical/biomed/1468-6708-3-7.txt
     533    3630   29585 technical/biomed/1468-6708-3-10.txt
     ... lots of lines! ...
  490673 3437323 26328271 total
```

Here we have our answer – 3437323. That's a lot of words!

**Write down in notes**: How many total words are in `technical/plos`? How many
total characters?

Another related question we might want to answer is **which file in
`technical/biomend` has the most lines?** If `wc` reported the files' counts in
order, we could simply read off the first or last one. But we can see in the
output above that there is no particular ordering relative to line, word, or
character counts in the output.

There's another command that's great for many situations like this: `sort`.
That's right – there's a sorting command built-in! `sort` takes a file and
prints out the lines in that file in sorted string order. The way `wc` is
designed, this ends up exactly matching a sort based on line number!

Let's try it:

```
$ wc technical/biomed/*.txt > biomed-sizes.txt
$ sort biomed-sizes.txt
... a bunch of lines ...
    1656   12212   89104 technical/biomed/1472-6904-2-5.txt
    1773   10309   83990 technical/biomed/gb-2002-3-12-research0086.txt
    1803    8968   73428 technical/biomed/gb-2002-3-7-research0036.txt
    2236    9393   78562 technical/biomed/1471-2105-3-18.txt
    2359   17408  136424 technical/biomed/1471-2105-3-2.txt
  490673 3437323 26328271 total
```

The last file output has 2359 lines, and it's
`technical/biomed/1471-2105-3-2.txt`.

**Write down in notes**: What is the article in that file about?

**Write down in notes**: Answer the following questions using `grep`, `find`,
*`*` patterns, `>` redirection, `wc`, and `sort`:

- What is the file with the _fewest words_ in `technical/plos`? What are the
first few lines of that file? (Hint: the line count comes first. You can make
`wc` report just the word count with the `-w` option)
- What is the file with the _most characters_ in either `technical/plos` or
`technical/biomed`? What are the first few lines of that file? (Hint: try the
`-c` option to `wc`)
- How many lines in `technical/plos` contain the string `"base pair"`? What
about in `technical/biomed`? (Hint: look up the `-r` option to `grep`)
- How many _files_ in `technical/plos` contain the string `"base pair"`? What
about in `technical/biomed`? (Hint: look up the `-l` option to `grep`)

Copy the commands you used to get these answers along with the answers
themselves! You can make scripts out of them (especially if they needed multiple
commands).

**Discuss**: What other interesting questions can you answer with what you know?

### A Search Server

The repository also has a file `DocSearchServer.java`, which has a (fixed)
version of `getFiles` from last week's lab, and a server that uses it.

- Add `start.sh` and `test.sh` scripts as we did in lecture, and make sure they
start the server and run the tests, respectively.
- Start the server and check that the following URL paths have the described
behavior:
  - `/` prints `"There are NNNN files to search"` where NNNN is the total number of
  files returned by `getFiles`
  - `/search?q=search-term` prints `"There were NNNN files found:"` and then a list
  of all the paths of files that contain that search term. For example, if the
  search term is `base pair` it should print the same paths you found in your
  search above.
- Add a few tests that give meaningful search results (you can use some of the
ideas from using `grep` above), and take some screenshots of the working server
loaded from a browser.

**Write down in notes**: How long did it take you to make the scripts? Now that
you've made them how long does it take you to run the tests and start the
server? Was that an overall savings on your time? What if we run the tests and
server 100 more times this quarter, will it be worth it?

**Push to Github**: The scripts you added to your fork

**Experiment**: Add a new text file somewhere in `technical` with the contents
of your choice. Then, get the code and data onto `ieng6` if you haven't already
(you could push and then `git clone` on the server). Start the server and have
our partner do a search that finds the file you added. Then do the same with
their server (they add a new file that you find). Where are those files stored?
What does that say about how the filesystem and paths work for searching for
these files?

Then, make an **extension** to the behavior of the server:

- Accept queries of the form `?title='<some string>'`. This should return all
  the file paths where the given string is part of the *path* of the file
  (including its file name)
- Write two tests in the test file that use this query
- Include a few screenshots demonstrating this query
- Start your enhanced server on `ieng6` and get someone else to try it out from
  another computer

If you want a programming **challenge**, try making it so you can support
queries of the form `title=str&q=str` that check for _both_ the title and the
file contents containing the respective strings.



### Getting AI to Do It

What's a question you want to answer, but aren't sure how to answer about these
files with the commands you have? Maybe someone in your group or your lab tutor
would have good guesses! Or maybe.... ChatGPT would.

Come up with at least one idea that you don't know how to answer with the
commands you've seen so far. Ask [ChatGPT](https://openai.com/blog/chatgpt/) to
help! You (or one of the members of your group) can make a free account by
logging in with Google.

We're not giving any examples here because _we are all new to this technology_.
We want you to experiment and teach each other (and us) what works and what
doesn't for you in using it to explore different command-line options.

The crucial thing here is that you should both **try out** and **attempt to
explain** the results from ChatGPT. As we saw in class, it's completely capable
of lying or giving inconsistent results. So we have to actually run the
commands to check that they're producing something reasonable (and maybe check
by hand that some of the answers are correct!)

You'll probably see new commands (ChatGPT doesn't know which commands we
learned this week), see new options and symbols, and so on. Try asking your
tutor, your group, Google, and ChatGPT for help understanding them. Write down
in your notes the prompts that worked especially well, and what you learned.

**Write down in notes**: At least 4 prompts you gave to ChatGPT where it
suggested command lines to try, with screenshots showing what happened when you
tried out those commands, and explanations of how they work. Don't just
copy-paste the explanation from ChatGPT if it gives one (we've seen those be
wrong in class, too!) – try to verify the explanation.

## Lab Report 3 - Bugs and Commands (Week 5)

You’ll write this report as a Github Pages page, then print that page to PDF and upload to Gradescope.

### Part 1 - Bugs

Choose one of the bugs from week 4's [lab](https://ucsd-cse15l-w24.github.io/week4/index.html#lab-tasks).

Provide:

- A failure-inducing input for the buggy program, as a JUnit test and any associated code (write it as a code block in Markdown)
- An input that _doesn't_ induce a failure, as a JUnit test and any associated code (write it as a code block in Markdown)
- The symptom, as the output of running the tests (provide it as a screenshot of running JUnit with at least the two inputs above)
- The bug, as the before-and-after code change required to fix it (as two code blocks in Markdown)

Briefly describe why the fix addresses the issue.

### Part 2 - Researching Commands

Consider the commands `less`, `find`, and `grep`. Choose _one_ of them. Online,
find 4 interesting command-line options or alternate ways to use the command
you chose. To find information about the commands, a simple Web search like “find
command-line options” will probably give decent results. There is also a
built-in command on many systems called `man` (short for “manual”) that
displays information about commands; you can use `man grep`, for example, to
see a long listing of information about how `grep` works. Also consider asking
ChatGPT!

For example, we saw the `-name` option for `find` in class. For each
of those options, give 2 examples of using it on files and directories from
`./technical`. Show each example as a code block that shows the command and its
output, and write a sentence or two about what it’s doing and why it’s
useful.

That makes 8 total examples, all focused on a single command. There should be
two examples each for four different command-line options. Many commands like
these have pretty sophisticated behavior possible – it can take years to be
exposed to and learn all of the possible tricks and inner workings.

Along with each option/mode you show, **cite your source** for how you found out
about it as a URL or a description of where you found it. See the [syllabus](https://ucsd-cse15l-w24.github.io/index.html#lab-reports-and-academic-integrity) on Academic Integrity and how to 
cite sources like ChatGPT for this class. 

