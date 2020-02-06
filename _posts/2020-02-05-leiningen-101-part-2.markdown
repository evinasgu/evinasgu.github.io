---
title: "Leiningen 101 part II"
layout: post
date: 2020-02-05 22:44
image: /assets/images/leiningen.jpg
headerImage: true
tag:
- Clojure
- Leiningen
- Functional Programming
star: true
category: blog
author: enyert
description: Part II of Leiningen 101, covering dependency management and project.clj structure
---

In the last tutorial we made our first steps with leiningen. In this chapter we will cover two important topics: project.clj 
structure and dependency management with this file.

### project.clj file

When we create a project using lein, one of the files added by default is project.clj. This file is one of the most important because it 
could contains some relevant configurations. Going a little more beyond the basics, this file contains the call of a macro named
**defproject** or a def with a project map defined. The source code for this macro can be found
[here](https://github.com/evinasgu/leiningen/blob/master/leiningen-core/src/leiningen/core/project.clj).

#### Structure and most used parts 

As we just mentioned, project.clj contains the macro **defproject**. This macro initially takes two arguments: the project groupId and the name
using the format "<groupId/projectName>". After these two parameters, we can include other :key value pairs for especific configurations, so we
will mention some of them(most used):

- description: This key is associated with a string that contains the description of the project. Remember the description is searchable from
external repositories
- url: Associated with project's URL.
- mailing-list: The structure of the mailing-list key is a map with the following keys: :name, :archive, :other-archives, :post, :subscribe,
:unsubscribe. One example could be: 
```clojure
{
 :name "random mailing list"(String)
 :archive "http://random.org/random-mailing-list-archives"(String)
 :other-archives ["http://random.org/random-list-archive2"(String)
                  "http://random.org/random-list-archive3"(String)]
 :post "list@random.org"(String)
 :subscribe "list-subscribe@random.org"(String)
 :unsubscribe "list-unsubscribe@random.org"(String)
}
```
If you are familiar with mailing lists every key will be obvious. One important thing to mention here is that our project can have multiple mail
lists, in this case we can use the :mailing-lists key (Note plural) associated with a seq of the same structure of :mailing-list.
- license: This contains the license of the project with the following structure:
```clojure
{
  :name "Name of the license"(String)
  :url "url"(String)
  :distribution :repo(This have to be one of :repo or or manual keys)
  :comments: "Commments"(String)
}
```
- min-lein-version: Specifies the minimal leiningen version required for the project to work properly.
- exact-lein-version: If you require a specific leiningen version, this key will abort the execution if the version used mismatch with the value
of the key.
- dependencies: This key is super important, because it is related with the dependency management provided by Leiningen. Dependencies type base is a
vector or vectors. 

