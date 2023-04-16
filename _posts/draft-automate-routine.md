## Tools to speed-up your programming routines

Routines like running a complex build command, doing an API call, synchronizing data between machines occupy a significant amount of daily work of a programmer. Reducing the time to get these things done means more
time to write software that brings value. Hence, automation of these routine processes is one of the simplest ways to boost productivity as a programmer. Let's discuss some ways to do that.

Imagine that we want to examine logs of a Lambda Function on AWS. Using a command-line interface (CLI) of choice, the call would look like this:

```bash

```

If we call this command repeatedly over time, typing it by hand gets annoying. The most basic cure to this is to create an alias for the command we are using:

```bash

```

The problem is now solved. After some time some service annoys we more than others and we decide to add some filters to the initial command to watch logs for specifically this service. Command becomes:

```bash

```

But how do we create an alias for this command? A set of seemingly random letters that denote first letters of each parameter won't be usable, since it is 1) hard to remember 2) hard to scale for other services. One needs to find a better way. Let's build a small bash script.

```bash
parametrized script with default arg
```

Now we can call it in a few words, passing our service as a parameter:

```bash

```

And we can even use the aliasing technique for this new command. But let's recap how much work we did to build this light script? What about building a lot of them? There should be a more straightforward solution. And here GNU Make comes onto stage.

Create a file called Makefile. We format the code from the bash script to the following:

```bash

```

Now, from the terminal run the `make filter`. This is all it takes to run our complex filter command.
The last one is a very convenient tool to quickly automate routine and is a nice fit in most of the cases. Yet, writing a more complex set of commands together could become tricky. So Makefile may not be our last resort.

Let's switch from an API call to another example. Now we are running multiple experiments with a C++ program and don't want to modify this rather scary-looking Makefile:

```makefile

```

What are our alternatives? One option is to visit realms of some fancy make succesors: Taskfile, etc.
Another, for those who have a sense of writing in Python, I suggest taking a look at the following.