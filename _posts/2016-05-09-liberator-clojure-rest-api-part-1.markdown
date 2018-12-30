---
title: "Liberator + Clojure REST API, First approach - Part 1"
layout: post
date: 2016-05-09 22:44
image: /assets/images/liberator-logo.png
headerImage: true
tag:
- markdown
- elements
star: true
category: blog
author: enyert
description: A gentile introduction about building APIs with Liberator and clojure
---

### Glossary

#### Clojure
  Clojure is a general-purpose programming language hosted on the Java Virtual Machine. Clojure encourages the use of high-order functions and
  comes with a set efficient immutable data structures, so it's a functional programming language too.
  More information: [Clojure](http://clojure.org/)

#### Leiningen
  Leiningen is a Clojure tool used for automating Clojure projects, managing dependencies and more.
  More Information: [Leiningen](http://leiningen.org/)

#### Ring
  Ring is a Clojure web applications library made to provide an abstraction of HTTP into a simple API.
  More information: [Ring](https://github.com/ring-clojure/ring)

#### Compojure
  Compojure is a Clojure routing library that uses Ring to allows web applications to be modular.
  More information: [Compojure](https://github.com/weavejester/compojure)

#### PostgreSQL
  PostgreSQL is an open-source RDBMS(Relation database management system).
  More information: [PostgreSQL](http://www.postgresql.org/)

#### Liberator
  Liberator is our rock star here. Liberator is a Clojure library that helps us expose our resources complying with the requirements of
  the HTTP especification.
  More information: [Liberator](http://clojure-liberator.github.io/liberator/)


### What is this tutorial about?

In this tutorial, we will understand how to make a REST API with Clojure/Ring/Liberator/PostgreSQL stack. This is not
a traditional web stack like MEAN(Mongo/Express/Angular/NodeJS) or LAMP(Linux/Apache/MySQL/PHP) but if you enjoy programming
with Clojure and you're trying to make an Rest API, then stay tunned.

This tutorial tries to catch the fundamental elements about building a REST API with Clojure through presenting each step of a basic
development process.


### Designing and creating our database

This is not a tutorial about installing and configuring PostgreSQL databases but you can start with these [Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-14-04), [Windows with installers](http://www.enterprisedb.com/products-services-training/pgdownload#windows) or [Mac via brew](http://exponential.io/blog/2015/02/21/install-postgresql-on-mac-os-x-via-brew/). If you already have
PostgreSQL installed and configured then it's time to start with the database design.

Let's suppose our client "ClojuChips" wants to store information about their products and promotions. They gave us Excel files
with information like this:

*Products.xls*


| Name             | Description             |  Quantity |  Price |
|------------------|-------------------------|-----------|--------|
| ClojuMilk        | Skim Milk for vampires  |    12     |  1.75$ |
| ClojuLamp        | Lamps for bats          |    1232   |   10$  |



*Promotions.xls*

|      Name      |       Description      | Discount | StartingDate | EndingDate | RelatedProducts |
|----------------|------------------------|----------|--------------|------------|-----------------|
| ClojuMilkPromo | Cheaper price for milk |    33%   |  22/05/2016  | 23/06/2016 | ClojuMilk       |
| ClojuLampPromo |     Lamps for bats     |    40%   |  13/07/2015  | 14/08/2016 | ClojuLamp       |


Given this information, we need to create the database in PostgreSQL. To create our db, we use this SQL script:

```sql
  CREATE DATABASE prueba ENCODING 'UTF8';  
```

To create our tables according with the information provided by the client, we can use this
SQL script:

```sql
CREATE TABLE product
(
id serial NOT NULL,
name character varying(70) NOT NULL,
description character varying(200),
quantity integer NOT NULL,
price real NOT NULL,
CONSTRAINT product_pkey PRIMARY KEY (id)
);

CREATE TABLE promotion
(
id serial NOT NULL,
name character varying(80) NOT NULL,
description character varying(200),
starting_date timestamp without time zone NOT NULL,
ending_date timestamp without time zone NOT NULL,
product_related integer,
CONSTRAINT promotion_pkey PRIMARY KEY (id),
CONSTRAINT promotion_product_related_fkey FOREIGN KEY (product_related)
    REFERENCES product (id) MATCH SIMPLE
    ON UPDATE NO ACTION ON DELETE CASCADE
);
```

### Designing the API

To start designing the API, we need to identify our resources. Resources in a REST API are objects with a type, a set of methods
that operate on it, associated data, and relationships to other resources. They are pretty similar to normal object instances, with
the difference that only a few standard method are defined for the resources(corresponding to HTTP Methods as GET, PUT, POST, DELETE,
etc). For more information about HTTP methods: [HTTP methods](http://www.tutorialspoint.com/http/http_methods.htm)

In this example, we have 2 resources: products and promotions. So we will create five functionalities for each one:

*Product functionalities:*

|---
| URL | HTTP Method | Description |
|-|-|-|
| /api/product | GET | List all product |
| /api/product/:id | GET | Give one product specified by product id |
| /api/product | POST | Create new product |
| /api/product/:id | PUT | Update one product specified by product id |
| /api/product/:id | DELETE | Delete one product specified by product id |
|---

*Promotions functionalities:*

|---
| URL | HTTP Method | Description |
|-|-|-|
| /api/promotion | GET | List all promotions |
| /api/promotion/:id | GET | Given one promotion specified by promotion id |
| /api/promotion | POST | Create new promotion |
| /api/promotion/:id | PUT | Update one promotion specified by promotion id |
| /api/promotion/:id | DELETE | Delete one promotion specified by promotion id |
|---


### Project structure
Setting a coherent project structure for a REST API would be a hard work, because we need to
divide our Clojure namespaces and our folder hierarchy into a kind of logical framework.
We will use the following approach over the lein compojure template:

![Project Structure]({{ site.baseurl }}/assets/images/liberator_tutorial_folder_structure.png)

##### Description of fundamental folders and files
* [*project.clj:*](https://github.com/enyert/liberator-service/blob/master/project.clj) This file contains information of the
dependencies and initial configurations needed for our project to work properly.

* [*handler.clj:*](https://github.com/enyert/liberator-service/blob/master/src/liberator_service/handler.clj) This file works as a middleware for compojure and
ring. Our routes are exposed here.

* [*models/db.clj:*](https://github.com/enyert/liberator-service/blob/master/src/liberator_service/models/db.clj) Here we have our database operations made with
jdbc. This represents the connection between our data and resources.

*  [*resources/:*](https://github.com/enyert/liberator-service/tree/master/src/liberator_service/resources) This folder contains the resource definition to interact with the functions from *db.clj*. We use liberator to define every resource.

* [*routes/:*](https://github.com/enyert/liberator-service/tree/master/src/liberator_service/routes) Inside of this folder we have the control of the API endpoints
and their relationships with the resources.


### Setup

To star with coding, we need to initiate our project. We are using Leiningen to dependency
management, "scaffolding" using default templates. In our case, we want a compojure
application, then we have to type *lein new compojure liberator-service* where *compojure*
is the name of the desired template and *liberator-service* is the name of our application.

Right now we have a folder with a basic compojure structure. Let's start modifying the
*project.clj* file located in the root folder of our project with this content:

```clojure
(defproject liberator-service "0.1.0-SNAPSHOT"
  :description "FIXME: write description"
  :url "http://example.com/FIXME"
  :dependencies [[org.clojure/clojure "1.6.0"]
                 [compojure "1.3.4"]
                 [hiccup "1.0.5"]
                 [ring-server "0.3.1"]
                 [liberator "0.13"]
                 [cheshire "5.2.0"]
                 [org.clojure/java.jdbc "0.6.0-rc1"]
                 [postgresql "9.3-1102.jdbc41"]]
  :plugins [[lein-ring "0.8.12"]]
  :ring {:handler liberator-service.handler/app
         :init liberator-service.handler/init
         :destroy liberator-service.handler/destroy}
  :profiles
  {:uberjar {:aot :all}
   :production
   {:ring
    {:open-browser? false, :stacktraces? false, :auto-reload? false}}
   :dev
   {:dependencies [[ring-mock "0.1.5"] [ring/ring-devel "1.3.1"]]}})
```

After this modification we have to run *lein deps* to install the dependencies for
our project.

In order to pass the database info to our project, we are going to need three environment variables: CLOJUCHIPS_DB_URL, CLOJUCHIPS_DB_USER and CLOJUCHIPS_DB_PASS. In Linux we can create these variables using *export* command via terminal or editing the .bashrc file.

```bash
export CLOJUCHIPS_DB_URL=url_value
export CLOJUCHIPS_DB_USER=user_value
export CLOJUCHIPS_DB_PASS=password_value
```  

After this step, we have our project ready to start coding. I will explain more details
of *ClojuChips* in the next part of this article. If you want to check the project go
to [this repository](https://github.com/enyert/liberator-service.git).
