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

In the last tutorial, we made our first steps with leiningen. In this chapter we will cover two important topics: project.clj
structure and dependency management with this file.

### project.clj file

When we create a project using lein, one of the files added by default is project.clj. This file is one of the most important because it
could contain some relevant configurations. Going a little more beyond the basics, this file contains the call of a macro named
**defproject** or a def with a project map defined. The source code for this macro can be found
[here](https://github.com/evinasgu/leiningen/blob/master/leiningen-core/src/leiningen/core/project.clj).

#### Structure and most used parts

As we just mentioned, project.clj contains the macro **defproject**. This macro initially takes two arguments: the project groupId and the name
using the format "<groupId/projectName>". After these two parameters, we can include other :key value pairs for specific configurations, so we
will mention some of them(most used):

- description: This key is associated with a string that contains the description of the project. Remember the description is searchable from
external repositories
- url: Associated with the project's URL.
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
lists, in this case, we can use the :mailing-lists key (Note plural) associated with a seq of the same structure of :mailing-list.
- license: This contains the license of the project with the following structure:
```clojure
{
 :name "Name of the license"(String)
 :url "url"(String)
 :distribution :repo(This have to be one of :repo or or manual keys)
 :comments: "Comments"(String)
}
```
- min-lein-version: Specifies the minimal leiningen version required for the project to work properly.
- exact-lein-version: If you require a specific leiningen version, this key will abort the execution if the version used mismatch with the value
of the key.
- dependencies: Dependencies base type is a vector or vectors that is transformed into a map using dependency-map function [here](https://github.com/evinasgu/leiningen/blob/master/leiningen-core/src/leiningen/core/project.clj).This key is super important because it is related with the **dependency management** provided by Leiningen and thats why I am starting a section for this point.

### Dependency Management

When I started this chain of posts, I mentioned one of the tasks that Leiningen performs is the Dependency Management.

Dependency Management is important because in almost all cases our projects will need other projects to add certain features otherwise, we would have
to reinvent the wheel every time or copy and paste code in our project.

On the other hand, this part of the development life cycle can help us to make segmentation of certain dependency groups required for different environments, e.g. development,
test. production, etc.

**Tip here**: We can use ```lein search $TERM``` to look for dependencies associated with $TERM in central repositories and clojar by default.

The fundamental keys used for dependency management in the project.clj file are: :dependencies, :plugins, :repositories, :plugin-repositories, :mirrors and :profiles. I will explain each of
these keys below:

- **:dependencies**: I mentioned this key before, but we will need more of this because is vital for Dependency Management. :dependency keyword has a structure like the following example:
```clojure
:dependencies [[org.clojure/clojure "1.10.0"]
		       [org.clojure/data.xml "0.0.8"]
			   [stencil "0.5.0" :exclusions [org.clojure/core.cache]]
			   [commons-io "2.6"]
			   [...]]
```

As we can see the structure is a vector of vectors and vectors are listed as [group-id/name version]. We can find internal keys like :exclusions to ignore from being included in our project's
classpath. Lein resolves dependencies transitively, for this reason, it is possible to find unwanted dependencies. These keywords are supported by [Pomegranate](https://github.com/clj-commons/pomegranate),
a library that works as Maven-resolver.

- **:plugins**: plugins are extra functionalities that run under leiningen to provide new tasks or hooks. The structure of this keyword is a vector of vectors e.g.:
```clojure
:plugins [[lein-pprint "1.1.1"]
		[lein-assoc "0.1.0"]
		[s3-wagon-private "1.1.1"]
		[lein-foo "0.0.1" :hooks false]
		[...]]
```

- **:repositories**: when we create a dependency we need to store it into safe places, these places are called artifact repositories. Leiningen uses [clojars.org](https://clojars.org)
and [Maven Central](https://search.maven.org) by default. Projects sometimes need particular, private, or third party artifact repositories, so **:repositories** keyword provides this information to
Leiningen. The structure of this keyword is a vector of vectors as well as you can see in the next example:

```clojure
:repositories [["java.net" "https://download.java.net/maven/2"]
	["sonatype" "https://oss.sonatype.org/content/repositories/releases"]
	[...]]
```

Every subvector has a "common" pattern, ["repository name" "url"] but it can be changed by other pair ["repository name" {map of settings}]. This map of settings contains keywords such as
:url, :snapshots, :sign-releases, :checksum, :update, :releases, etc. These settings provide specific information to Leiningen about the repositories registered.

- **:plugin-repositories**: Sometimes we need plugins that are stored in third-party repositories, so the information provided in this keyword will be included with :repositories when Leiningen
is loading the plugins. The structure of this keyword is equivalent to :repositories keyword.

- **:mirrors**: The structure of this keyword is a map of maps and it's used to override repositories when the key exists in the mirrors map. Let see an example to understand this better:
```clojure
:mirrors {"central" {:name "central" :url "http://url.url.com/mirrors/maven2"}
		"clojars" {:name "myclojars" :url "http://myclojars.local/mirrors/clojars"}}
```
In this case, we are overriding "central" and "clojars" default repositories with new names and url provided by :mirrors map.

- **:profiles**: this keyword provides separations of specific properties of the project to given profile names. We can use profiles to separate specific dependencies, plugins, compilation modes,
and other stuff. The structure of this keyword is a map of maps and the main keyword of the map is the profile name. The sub-maps are merged into the project map if the profile name is active. Here we
can see an example:

```clojure
:profiles {:debug {:debug true}
	:1.9 {:dependencies [[org.clojure/clojure "1.9.0"]]}
	:repl {:plugins [[cider/cider-nrepl "0.7.1"]]}
	{...}}
```
In this example we have three profiles: :debug, :1.9(Yes we can use version numbers as keywords) and :repl. The :debug profile has the :debug option enabled, to behavior related with :debug true will
happens. the :1.9 profile contains the specific dependency of clojure 1.9 and :repl profile contains the cider-nrepl plugin.

**Tip here**: we can use any of these profiles with higher-order tasks using with-profiles.

### Conclusion

The purpose of this post was to provide a guide about the structure of the main Leiningen configuration file a.k.a **project.clj** and take the opportunity to give some explanation about dependency
management with Leiningen.

For more specific information about these topics please refer to [sample.project.clj](https://github.com/technomancy/leiningen/blob/stable/sample.project.clj) and
[leiningen oficial tutorial](https://github.com/technomancy/leiningen/blob/master/doc/TUTORIAL.md#dependencies).

I hope you enjoyed the reading and please contact me for any comment.

Happy hacking!
