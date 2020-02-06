---
title: "Leiningen 101 part II"
layout: post
date: 2020-02-06 22:44
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
could contains some relevant configurations. Going a little more beyond the basics, this file contains the invocation of a macro named
** defproject ** or a def with a project map defined. The source code for this macro can be found 
[here](https://github.com/evinasgu/leiningen/blob/master/leiningen-core/src/leiningen/core/project.clj).

#### Structure and most used parts


