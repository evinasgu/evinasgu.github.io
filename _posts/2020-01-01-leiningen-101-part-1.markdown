---
title: "Leiningen 101 part I"
layout: post
date: 2016-05-11 22:44
image: /assets/images/leiningen.jpg
headerImage: true
tag:
- Clojure
- Leiningen
- Functional Programming
star: true
category: blog
author: enyert
description: Introduction about Leiningen features and basic usage
---

### Glossary

#### Clojure

Clojure is a general-purpose programming language hosted on the Java Virtual Machine. Clojure encourages the use of high-order functions and
  comes with a set efficient immutable data structures, so it's a functional programming language too.
  More information: [Clojure](http://clojure.org/)

#### Leiningen
  Leiningen is a Clojure tool used for automating Clojure projects, managing dependencies and more.
  More Information: [Leiningen](http://leiningen.org/)


### What is this tutorial about?

With this tutorial I want to start a serie of four posts(including this one and videos related) about [Leiningen](http://leiningen.org/). When I started learning Clojure, the first tool to be presented was Leiningen. I was just making my hello world example, so I decided to put more attention on the language but after a little while I just noticed that Leiningen is a powerful tool, so I want to share what I learnt in the process with you.

### So, What is Leiningen?

If you are a developer you know that your applications will need to be shared, exposed and deployed in the real world. On the other hand, you know that your application probably will use features already implemented by other people and packaged into dependencies for your application. In clojure, Leiningen is one of the tools responsible for all these stuff, including:

- Project generation
- Dependency management
- Run tests
- Compile your source code
- Run the project
- Package your app to be deployed and shared
- And more things that I will show you in these posts!

Other programming languages include this type of tools too. In Java for example you can use Maven or Gradle, in python you can use pip. So if you are from other tech stacks, Think in Leiningen as the "analogy" for these tools.

### Installation

There are two ways to install Leiningen, using the leiningen script located at [this link](https://leiningen.org/) or using a package manager. We will cover these two options, but I recommend you to install leiningen using a package manager because it can handle the modification of the $PATH environment variable and the updates from new versions.

#### Requirements

The only requirement to install Leiningen is Java 8 or superior. I tested it with OpenJDK 8 and Oracle JDK, obtaining good results in both kits.


#### Manual Installation

In general, the installation of Leiningen is easy. You can find the basic steps [here ](https://leiningen.org/#install) but I will cover these steps here as well.

- Download the script

Linux users the script is located [here(linux script)](https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein) and Windows users can find the script [here(windows script)](https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein.bat). Just right click on any of the links using your favorite browser and select the option Save Link As...

You can use wget or any equivalent tool for this purpose.

Remember to make the script executable using the following command:
```Bash
chmod a+x <PATH_TO_LEIN>/lein
``` 
**chmod** command modifies Linux file permissions, **a** indicator means that the script can be executed for the actual user, groups, and others the file belongs to. The **x** modifier add the execute permission. Remember the **+** operator add permissions to a file.

- Put lein in your $PATH or add the <PATH_TO_LEIN> to your $PATH

**$PATH** is an environment variable that contains a list of directories where executable programs are located. If you put the lein script in one of the directories of your $PATH your system will recognize it as an executable. On the other hand, you can add a custom location to your $PATH and get the same result. Remember the locations in our $PATH are separated by ":" symbol.

- Run lein

Type *lein* in your shell and it will download all the needed files to start using it!

To verify the installation is correct type ```lein -v``` or ```lein --version``` in your terminal, you could see something like this: 
IMAGE


#### Using a package manager(For this example, we will use [SDKMAN!](https://sdkman.io/)) == Recomended == 

I recommend using a package manager for lein installation because these tools can manage almost all the installation process and the updates when a new package created of the script is uploaded to the package manager repository. For this example we will use [SDKMAN!](https://sdkman.io/) in linux. For windows, [chocolatey](https://chocolatey.org/) is a great option and MacOS users have [homebrew](https://brew.sh/) as well.

[SDKMAN!](https://sdkman.io/) is a tool to manage multiple versions of many development kits. The installation is easy, just follow the instruction [here](https://sdkman.io/install)

After [SDKMAN!](https://sdkman.io/) is installed just use ```sdk install leiningen``` command, wait for the installation and we will be ready to start with Leiningen!

To verify the installation is correct type ```lein -v``` or ```lein --version``` in your terminal, you could see something like this:
IMAGE


### Using leiningen help

#### CLI

First of all, we need to remember how a CLI(**C**ommand-**L**ine **I**nterface) works. 

A CLI(**C**ommand-**L**ine **I**nterface) is a tool that processess certain instructions to a ***program*** in the form of **structured** text lines. In general, our ***program*** is a **command-line interpreter** that handle the parsing process and validation of the text lines. Then this ***program*** provides functionality in response to our instructions.

Coming back to our context, we have lein as our CLI. lein receives **tasks**, e.g, new, help, clean, install, and more. These tasks are related with many functions and we can pass data to them via **arguments**. For example, if we introduce the command ```lein new compojure leiningen-101``` the task here is **new** and the arguments are **compojure** and **leiningen-101**. Leiningen parse and validate the command introduced using the **command-line interpreter** and try to trigger the related functionality, in this case it will be new functionality. 

#### help task

We installed Leiningen and we understand how a CLI works, excellent! But one of the things I learned with my experience is that we need to figure out how to get help before using a new tool. For this part, we will use the CLI
to learn how the **help task** works.

Please type ```lein -h``` or ```lein --help``` in your terminal. You will see this output: IMAGE

We can see a short definition of Lein, a list of available task, the global options and the MOST important one: ```Run 'lein help $TASK' for details```. Here **$TASK** symbol means that you can get the description of any task you
insert as **$TASK**. Lets try **new** task typing ```lein -h new```, we will see something like this: IMAGE

All the detailed information needed to create our project is presented, so we are ready to create a new project!

### Creating our new project

As I mentioned in the help task section, the command to create our project is ```lein new```, it generates basic project structures based in templates. I have used four templates: **default** commonly used for libraries, **app** 
for standalone applications and, **compojure** and **luminus** for web applications. There are other templates listed in [here](https://clojars.org/search?q=lein-template) if you need more information. 
For this example I will use the **app** template, so lets try it!

#### Getting the template description

Please type ```lein new :show app```(the **:show** argument is used to get descriptions of specified templates), we will receive the following output: IMAGE

As you can see in the image, we received a little description of the template and the the argument list. The argument list contains the different things that we need to pass to Leiningen to generate a project with a specific
template. In this case we need to pass the name argument, but this can be different for other project. For example, if we type ```lein new :show luminus``` the argument list is this ```Argument list: ([name & feature-params])```, 
this means we need to provide the name and other feature-params(if needed) to Leiningen in order to generate our initial project.

#### Executing the command

We obtained the description of the app template using the :show argument, so type ```lein new app first-contact``` to obtain this mesage ```Generating a project called first-contact based on the 'app' template.```. When this command
finish, we will have a new folder named first-contact with the following structure: IMAGE

This is the basic structure for the app template and now we can start working with our project using the files generated by Leiningen. This is important because it saves the time of creating the folder hierarchy and the individual
files manually.

I hope this post had been useful for you to understand the basic first steps with Leiningen. In the second part we will cover the project.clj file and the dependency management feature provided by Leiningen.

Happy coding! =)

