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

#### Manual Installation

#### Using a package manager(For this example, we will use [SDKMAN!](https://sdkman.io/))




##### Utility functions

```clojure
;; Utility operations

(defn get-environment-var
  "Get the value of an environment variable"
  [name]
  (System/getenv name))

(defn convert-dates
  "Convert dates from miliseconds to java.sql.Timestamp object"
  [date1 date2]
  (hash-map
   :starting_date (java.sql.Timestamp. date1)
   :ending_date (java.sql.Timestamp. date2)))

(defn process-promotion
  "Merge a promotion map with new dates from convert-dates function"
  [promotion]
  (merge promotion
    (convert-dates (:starting_date promotion) (:ending_date promotion))))
```

