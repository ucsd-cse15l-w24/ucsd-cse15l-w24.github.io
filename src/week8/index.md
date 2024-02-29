# Week 8 - Debuggers and Controlling Processes

## Lecture Materials
- [Monday Lecture Handout (PDF)](https://drive.google.com/file/d/1XTtv6DswnhH3i3tBmEW31eTW-FCOte7Q/view?usp=sharing)
- [Monday Lecture Handout (Slides)](https://docs.google.com/presentation/d/14EV0i0urY3vycA2ZZs11XvdYjRQ6lBB7/edit?usp=sharing&ouid=109342588918218787603&rtpof=true&sd=true)
- [Monday EdStem Workspace](https://edstem.org/us/courses/51148/workspaces/pJq0Arr9rHhhaIXhq4RkmOzLhfBTnpMY)
- [Wednesday Lecture Handout (PDF)](https://drive.google.com/file/d/1eAoODTAVoxD-37UKxT6P-mjmkNg7puAu/view?usp=sharing)
- [Wednesday Lecture Handout (Slides)](https://docs.google.com/presentation/d/1491K1SwkcP7o3ulwt8kFrQRe-Z1rJqRb/edit?usp=sharing&ouid=109342588918218787603&rtpof=true&sd=true)
- Monday Notes <iframe src="https://drive.google.com/file/d/1erszbTMKaTs8Ee3ZmeEvkLU4rG-6Q3Mc/preview" width="100%" height="600px"></iframe>

### To Watch/Read

- Videos on `jdb`:
    - [Debuggers and `jdb`](https://www.youtube.com/watch?v=0Olg_U0Su_I)
    - [`jdb` and Infinite Loops](https://youtu.be/AFkUAwvPTGA)
- Week 7 Skill Demo Solution <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/0EY46CfEIQs?si=6DPz27tCRfzEi8zt" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Lab Tasks

As always, we have published the lab beforehand but they are subject to change until the start of the labs on Wednesday.

Discuss with your group:
![Image](../../images/circles_vs_triangles.png)

### Editing at the Command Line

**Everyone** should do this; it's skill practice that you all need.

Fork the repo [chat-server-pro](https://github.com/ucsd-cse15l-w24/chat-server-pro), making sure you **unselect** "copy the main branch only." Here's a screenshot of what that looks like:
![main-branch](../../images/main-branch.png)

Then, after `ssh`ing into ieng6, clone your **fork** of the `chat-server-pro` repo. 

Make sure you can `javac` and `java` as per usual to build and run your tests: 
```
$ javac -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar *.java
$ java -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar org.junit.runner.JUnitCore HandlerTests
```
Note that since you last saw `ChatServer`, our devs have added more functionality such as emoji handling and reading chat history from file and loading it into our `ChatServer`.

Let's now run our `ChatServer` by executing `java ChatServer [port number]` in the terminal, open a web browser, and then point it to:
```
http://ieng6-[number].ucsd.edu:[port number]
```
We should see a blank page. Let's now import one of our chat history files by running: 
```
http://ieng6-[number].ucsd.edu:[port number]/retrieve-history?file=chathistory1.txt
``` 
This will now load the chat history and make it available to our `ChatServer`. Go to the root directory: 
```
http://ieng6-[number].ucsd.edu:[port number]/
```
You should now see the chat history you just loaded. Take a screenshot of your root output and add it to your lab doc. Discuss with your lab mates how this could have worked. **Note:** You are not expected to be able to breakdown or rewrite the `ChatHistoryReader.java`.

Once you see the messages, feel free to add another chat by visiting 
```
http://ieng6-[number].ucsd.edu:[port number]/chat?user=[name]&message=[message]
```

You should be seeing a new message at the bottom

Then, go to

```
http://ieng6-[number].ucsd.edu:[port number]/save?name=[file name]
```

to save the messages into a new file. You can see the new file saved under `chathistory` folder.

Now on your own, open the java test file by giving it as an argument to `vim`, i.e.
`vim HandlerTests.java`. Note there's nothing to fix here, but we would like you to browse through the test to see the changes we've made. The test should work - our devs worked overtime to fix these bugs! You've just done file handling entirely on ieng6 and didn't need VScode at all,
just a terminal! Take a screenshot of your test results and paste them into your document.

As a group, discuss and **write in notes**:

- What were the names of the test functions within `HandlerTests.java`?
- What were two things you thought were annoying about using `vim`? Be specific.
- What were two things you thought were cool about using `vim`? Be specific.

### git branches

**Do this part as a group**, while logged into someone's account on `ieng6`. Here we will look at a branch in our code that is currently in developement and a work in progress. You and your group have been tasked with testing and debugging the new beta branch. This new branch includes adding even more functionality to the `ChatServer` you just pulled, such as semantic analysis based on emoji's used.

1. Make sure you are in the `chat-server-pro` folder and run `git branch` to see the current branch we are in and `git branch -a` to list all available branches. Take a screenshot of these commands and add them to your shared lab doc.
2. Notice the presence of the `week8-sprint` branch which is only available remotely. We will want to checkout the new `week8-sprint` branch by running `git checkout week8-sprint`. Normally performing a `git checkout` will update your current repo to that branch, but to ensure that we have the latest updates, you can always run a fresh pull in order to update our files. Let's do that by running `git pull` and verify our branch by running another `git branch`. Go ahead and take a screenshot of these steps and add them to your document. Discuss with your group members and write a short summary of what you just did in steps 1-2.

Fun Fact: [Sprint](https://www.codecademy.com/resources/blog/what-is-a-sprint/) is a common Software Engineering technique used to complete project milestones.

### Using a Debugger
1. We will now want to recompile our code and rerun our new tests for the semantic analysis. Run: 
```
$ javac -encoding utf-8 -g -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar *.java 
$ java -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar org.junit.runner.JUnitCore SemanticAnalysisHandlerTests
```
Notice that we have an infinite loop where the terminal stuck at . This will eventually result to test failures.
    
2. Next, use `jdb` to run the JUnit tests. You can refer to the lecture videos for a good way to
do this. Again, use `jdb` commands to find:
    - The stack trace when the exception is happening
    - The local variables in `index` and `analysis` when the exception is happening

3. Now, diagnose and fix the bug so that all the tests pass. There will be 3 bugs that you will need to identify and fix. Note that you may need to add additional tests to unearth these bugs as the given tests may not exhibit them! After identifying one, discuss with your members how to fix it and copy and paste the code into code markdown with the fixes. Recompile and rerun your tests. What other bugs can you find? What variables should we be looking at instead of `index` and `analysis`? Should we still look at these variables or others? Find each bug and use `jdb` to step through your code.

Practice using `jdb` with `suspend` to pause the program and show the stack
trace during the loop. You should be able to identify:

- Which test is triggering the infinite loop
- Which line the program stopped on when the program was `suspend`ed
- What the current values of all the variables are in `/semantic-analysis` at the
moment the program suspended

Take a screenshot or copy/paste of your `jdb` session and indicate in your notes
each of the three items above and how your `jdb` session informs you of that.

4. Make a commit with the fix and push it to Github. Paste the Github link to the commit in your notes. Recall from lecture the following terminal commands for adding files, commiting changes and pushing said changes:
```
git add
git commit
git push
```

**Write down in notes:**

- What is information that you were able to get via `jdb` that you would be
unable to get via the stack trace of the exception?
- What are some pros and cons of using `jdb` to get information vs. adding print
statements to do so?
- Discuss the `/semantic-analysis` command – What emoji's are there? How are they being analyzed and how is the semantic implemented? How could these type of methods be useful for analyzing other chat histories?

<!--
### More Debugger Uses

After compiling the new branch, run the `ChatServer` code, 
and debug the bugs. There will be 3 bugs that you will need to identify and fix. After identifying one, discuss with your members how to fix it and copy and paste the code into code markdown with the fixes. Recompile and rerun your tests. What other bugs can you find? What variables should we be looking at instead of `index` and `analysis`? Should we stil look at these variables or others? Find each bug and use `jdb` to step through your code. 

Practice using `jdb` with `suspend` to pause the program and show the stack
trace during the loop. You should be able to identify:

- Which test is triggering the infinite loop
- Which line the program stopped on when the program was `suspend`ed
- What the current values of all the variables are in `/semantic-analysis` at the
moment the program suspended

Take a screenshot or copy/paste of your `jdb` session and indicate in your notes
each of the three items above and how your `jdb` session informs you of that.
-->
### Adding Your Own Emojis!

Now, consider the following chat histories. Edwin and Onat are other 15L professors and these are conversations between all the professors:

### Snippet 1

```
Joe: Hey, did you hear about the Java developer who walked into a bar?

Edwin: No, what happened?

Joe: He said, "Give me a strong cup of Java! And make it Object-Oriented!" ☕️😄

Onat: Haha, that's a good one, Joe! Speaking of Java, did you know that Monty Python's Holy Grail was written in Java?

Edwin: Really? I thought it was written in Python!

Onat: Well, that would make more sense, wouldn't it? But it seems they had a "Java Holy Grail" moment! 🏆😂

Joe: And let's not forget the Java programmers who always have to deal with "NullPointerExceptions."

Edwin: Yeah, they're like the Knights Who Say "Null"! They're always on a quest to find that elusive object.

Onat: True, true! And when they finally find it, they shout, "Eureka!" just like Archimedes.

Joe: Haha, the life of a Java developer can be quite the adventure, just like a Monty Python sketch!

Edwin: Indeed! But at the end of the day, we all know that "The Spam of Java is not a valid beverage!" 🍖🚫☕️

Onat: Well said, Edwin! Let's keep the Java and Python jokes rolling, and help our student's code be as legendary as a Monty Python tale! 😄🐍👨‍💻
```

### Snippet 2

```
Onat: Hey guys, have do you know why the chicken crossed the road?

Edwin: Was it late to class?

Joe: To get to the other side?

Onat: No, to escape the Ministry of Silly Walks! 😄

Edwin: And did you hear about the lumberjack who wanted to be a dentist?

Onat: Nope, what happened?

Edwin: He got tired of the daily grind! 🌲😁

Joe: That's a good one, Edwin! But let me tell you about the time I tried to buy a shrubbery for my garden...

Onat: A shrubbery, you say? Did the Knights Who Say "Ni" give you a hard time?

Joe: They did! They demanded a sacrifice of... a herring! 🐟

Edwin: Well, that's not too bad. At least they didn't ask for a dead parrot. 🦜

Onat: Ah, the dead parrot sketch! I love how they could turn the most ordinary situations into comedy gold.

Joe: Absolutely! It's like their humor is a cross between a witty intellectual and a three-headed knight!

Joe: Agreed! Now, let's not be too silly and get back to work before we end up in a sketch ourselves. 😄
```

### Snippet 3

```
Joe: Hey, have you heard about the Java bug that's as elusive as the Force?

Edwin: No, tell me more!

Joe: Well, it's like a Jedi mind trick. It only appears when you're not looking for it! 🧙‍♂️

Onat: Haha, that sounds like a classic Java bug. But you know what's even more mysterious? Jar Jar Binks's role in Star Wars!

Edwin: Oh, Jar Jar... He's the real mystery of the galaxy. Maybe he's secretly a Java developer trying to fix bugs in the codebase.

Joe: Or perhaps he's a Sith Lord in disguise, using the Dark Side of the Force to cause those Java bugs!

Onat: That would explain a lot! "Meesa causing bugs, oopsie!" 😂

Edwin: And when you finally find a solution to a tricky Java bug, it's like saying, "These aren't the bugs you're looking for!"

Joe: Absolutely! You wave your hand and hope the bug disappears. But it usually doesn't work that way.

Onat: Well, as they say in the Java world, "May the stack trace be with you!" 🚀🌌

Edwin: Haha, that's the programmer's version of "May the Force be with you!" Let's hope we can teach our students to squash those Java bugs and keep the galaxy safe from code errors!

Joe: Agreed! And may our code be as strong as the Force itself. 🤖👾💻
```

For **each** snippet, add a test both to **your** implementation of
`ChatServer` that takes into account an emoji for **each** snippet and performs a semantic analysis. Run the tests and show
the results of running the tests on each on your lab doc. This means you should add a total of
**3** test methods, one for each semantic analysis implementation.
<!-- 
## Week 8 Lab Report
Your report should include:

- A link to your `ChatServer` repository
- For each test above:
    - Decide on what it _should_ produce by creating the expected output in a text file.
    - For **your implementation**, the corresponding output when running the
    tests; if it passed, say so. If it didn't pass, show the specific part of
    the JUnit output that shows the test failure.




Add your lab report as `lab-report-4-week-8` within the same Github pages lab
reports repository you've been using all quarter, and include all of the
elements above. -->
