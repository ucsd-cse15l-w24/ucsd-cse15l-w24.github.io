# Week 6 - Scripting, CI, and Autograding

## Lecture Materials

- [Monday Lecture Handout (Slides)](https://docs.google.com/presentation/d/12uJMpqCtK4eky0nco598ltxUuwP_wqQy/edit?usp=sharing&ouid=109342588918218787603&rtpof=true&sd=true)
- [Monday Lecture Handout (PDF)](https://drive.google.com/file/d/1UqCrST7Cd_-e8aJkHdmmBUyrYNbrPbHw/view?usp=sharing)
- [Wednesday Lecture Handout (Slides)](https://docs.google.com/presentation/d/12itRtbSPi6b9tkYgjJe4dArs1JFq3NET/edit?usp=sharing&ouid=109342588918218787603&rtpof=true&sd=true)
- [Wednesday Lecture Handout (PDF)](https://drive.google.com/file/d/1agzsyQa7yg4pL5nFRFLE84YiJvNnFJ1p/view?usp=sharing)
- Monday Notes <iframe src="https://drive.google.com/file/d/11YI4XEsycQ83YOLeBabSLLJNvMK2SJWK/preview" width="100%" height="600px"></iframe>

### Video Shorts

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/z_iR6mXNvO8?cc_load_policy=1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/1BF1mqHwESw?cc_load_policy=1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/2_riXejzcKg?cc_load_policy=1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Lab Tasks

Discuss with your group:
    
![Image](../../images/three_rooms_question.png)
    
Write down your answers (and why you chose them!) in your group's shared doc.

In this week's lab you will write an automatic “grader” for some of the
methods we worked on in the week on testing.

In particular, you'll write a script and a test file that gives a score to the
functionality of a student-submitted `ListExamples` file and class (see
[ListExamples.java](https://github.com/ucsd-cse15l-f23/lab3/blob/main/ListExamples.java)).
The specific format is that you'll write a `bash` script that takes the URL of
a Github repository and prints out a grade:

```
$ bash grade.sh https://github.com/some-username/some-repo-name
... messages about points ...
```

This will work with a test file that _you_ write in order to grade students'
work. You can use this repository to get started with your grader
implementation; you should **make a fork**:

[https://github.com/ucsd-cse15l-s23/list-examples-grader](https://github.com/ucsd-cse15l-s23/list-examples-grader)

As part of your work, you'll need to **add new tests** (or copy them from your
work on Week 4's repo called lab3) to the testing file, because the few tests that are there
aren't sufficient for grading. You can do that incrementally as you try out the
script you write below on different student submissions.

### Your Grading Script

Do the work below in pairs—as a pair, you should produce **one**
implementation—push it to one member's fork of the starter Github repository
and include the link to that repository in your notes.

When your script gets a student submission it should produce either:

- A grade message that says something about a score (maybe pass/fail, or maybe
  a proportion of tests passed – your choice) if the tests run.
- A useful feedback message that says what went wrong if for any reason the
  tests couldn't be run (compile error, wrong file submitted, etc.)

A general workflow for your script could be:

1. Clone the repository of the student submission (you can find a list of submissions to test against [here](#sample-submissions)) to a well-known directory
name (provided in starter code) _This is done by the git clone command in the
provided script_
2. Check that the student code has the correct file submitted. If they didn't,
detect and give helpful feedback about it. _This is not done by the provided
code, you should figure out where to add it_
      - Useful tools here are `if` and `-e`/`-f`. You can use the `exit` command to
    quit a bash script early. These are summarized in the [week 4 Wednesday
    lecture
    handout](https://docs.google.com/presentation/d/13iOKAl6k-1018WpEJJIRf3UIzYnpfUdm/edit?usp=sharing&ouid=109342588918218787603&rtpof=true&sd=true)
3. Get the student code, the `.java` file with the grading tests, and any other
files the script needs into the `grading-area` directory. _The `grading-area`
directory is created for you, but you should move the files there._
      - Useful tools here might be `cp` (also look up the `-r` option to `cp`)
4. Compile your tests and the student's code from the appropriate directory
with the appropriate classpath commands (remember that if you're testing
locally on Windows, the [classpath is
different](https://ucsd-cse15l-w24.github.io/week4/index.html#:~:text=OK%20(2%20tests)-,WINDOWS%20USERS%3A,-local%20%24%20javac%20%2Dcp)). If the compilation fails, detect and
give helpful feedback about it. _You should add this_
      - Aside from the necessary `javac`, useful tools here are output redirection
    and error codes (`$?`) along with `if`
      - This might be a time where you need to turn _off_ `set -e`. Why?
5. Run the tests and report the grade based on the JUnit output. _You should add this_
      - Again output redirection will be useful, and also tools like `grep` could
    be helpful here

**Work incrementally** – make sure you understand what the given code does. Then
add steps incrementally. After each step, run on a test student submission and
check for syntax errors, debug that step, add `echo` statements to check what's
stored in variables, and so on. Try running it more than once – is there any set
up or cleanup you need to do before or after running it?

**Write down in notes:** screenshots of what your grader does on each of the
sample student cases below.

### Sample Submissions

Assume the assignment spec was to submit:

- A repository with a file called `ListExamples.java`
- In that file, a class called `ListExamples`
- In that class, two methods:
  - `static List<String> filter(List<String> s, StringChecker sc)`
  - `static List<String> merge(List<String> list1, List<String> list2)`
- These methods should have the implementations suggested in the repo [lab3](/week4/)

You should use the following repositories to test your grader:

- [https://github.com/ucsd-cse15l-f22/list-methods-lab3](https://github.com/ucsd-cse15l-f22/list-methods-lab3),
  which has the same code as the starter from lab 3
- [https://github.com/ucsd-cse15l-f22/list-methods-corrected](https://github.com/ucsd-cse15l-f22/list-methods-corrected),
  which has the methods corrected (I would expect this to get full or
  near-to-full credit)
- [https://github.com/ucsd-cse15l-f22/list-methods-compile-error](https://github.com/ucsd-cse15l-f22/list-methods-compile-error),
  which has a syntax error of a missing semicolon. Note that your job is _not_
  to fix this, but to decide what to do in your grader with such a submission!
- [https://github.com/ucsd-cse15l-f22/list-methods-signature](https://github.com/ucsd-cse15l-f22/list-methods-signature),
  which has the types for the arguments of `filter` in the wrong order, so it
  doesn't match the expected behavior.
- [https://github.com/ucsd-cse15l-f22/list-methods-filename](https://github.com/ucsd-cse15l-f22/list-methods-filename),
  which has a great implementation saved in a file with the wrong name.
- [https://github.com/ucsd-cse15l-f22/list-methods-nested](https://github.com/ucsd-cse15l-f22/list-methods-nested),
  which has a great implementation saved in a nested directory called `pa1`.
- **Challenge**
  [https://github.com/ucsd-cse15l-f22/list-examples-subtle](https://github.com/ucsd-cse15l-f22/list-examples-subtle),
  which has more subtle bugs (hints: see
  [`assertSame`](https://javadoc.io/doc/junit/junit/latest/index.html), which
  compares with `==` rather than `.equals()`, and think hard about duplicates
  for `merge`)

### Other Student Submissions

After you're satisfied with the behavior on all of those submissions, write
your own. Try to come up with at least two examples:

- One that is wrong but is likely to get full scores
- One that is mostly correct but crashes the grader and doesn't give a nice
  error back (and is likely to cause a Piazza/EdStem post saying “the grader
  is broken!”)

You should create these as **new, public Github repositories**, so that you can
run them using the same grader script by providing the Github URL.

**Write down in notes**: Run everyone's newly-developed student submissions on
everyone's grader. That means each team should be running commands like

```
bash grade.sh <student-submission-from-some-group>
```

Whose grading script is the most user-friendly across those tests?

### Running it Through a Server

We've also provided our `Server.java` and a server we wrote for you called
`GradeServer.java` in the starter repository.

You can compile them and use

```
java GradeServer 4000
```

to run the server.

Look at the code to understand the expected path and parameters in
`GradeServer.java`. Loading a URL at the `/grade` path with one of the repos
above as the query parameter. What happens?

That's quite a bit of the way towards an autograder like Gradescope!

**Write down in notes**: Show a screenshot of the server running your
autograder in a browser.

**Discuss and write down**: What other features are needed to make this work
more like Gradescope's autograder? (Think about running for different students,
storing grades, presenting results, etc)

Congratulations! You've done one kind of the work that your TAs do when setting
up classes 🙂
