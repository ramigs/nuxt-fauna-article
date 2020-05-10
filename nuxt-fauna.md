# Introduction

In this article, we’ll create a repository aggregator static site, using Nuxt.js
as a static site generator (SSG) with data from a FaunaDB database as the data
source

When you're finished, you'll be able to...
At the end you should know which commit created the leak

## Why a repository aggregator

As developers our list of favorite repos to follow overview of the GitHub
repositories you're interested in, and displaying the information that is more
relevant to you In this example, we'll display the GitHub stars and the date of
last commit.

## Jamstack

- Couple of paragraphs introducing Nuxt.js and FaunaDB.
- The Jamstack and how Nuxt.js (SSG) and FaunaDB (serverless) relate to it.

The concept of Jamstack is not new and its advantages have been extensively
documented before. Jamstack architectures allow us to build more secure,
scalable, faster websites.

The term "static" can be a bit misleading - that's why I like to use
"pre-rendered" interchangeably. When we build a Jamstack app, it doesn't mean we
have to compromise on dynamic data.

The widespread of functionality APIs makes way for common tasks - such as
authentication, e-commerce, and data storage - that used to be implemented over
and over, now be delegated to the experts of those specific domains.

The main idea I want to present is the benefit of being able to fetch data from
a database and use it to build a static/pre-rendered site that can be served
directly from a CDN.

Let's imagine we have a Film catalogue. It's
not that often that we need to add or delete a Film from the catalogue. Do we
need to have a server query a database at each client request? Couldn't we pass
the heavy lifting to the build process?

This is one of the key points I want to concentrate on. Do we really need to
have a server make the same request, to get the same data, take those same
results, run them against a templating engine and only then deliver the response
to the client?

## Nuxt.js

Nuxt.js is well known for SSR capabilites
but it can also go static.

## FaunaDB

"is a distributed database that promises to be always consistent, always secure,
global low-latency, zero operations (no need to manage servers"

So, what makes FaunaDB a good choice for this example?

Let me start by elaborating more on the intent of the article, as I point out in
which way Fauna's architecture and certain features, apply to this use case.

As a serverless database, FaunaDB allows our applications to access data "as a
service". Contrary to more "traditional" databases, there's no need to host and
manage our own database. Plus, a Fauna database scales automatically and it's
globally distributed.

From a developer's perspective this is awesome, because it allows us to be more
productive and focus on the domain logic of the app we're building.

# Goals

- What we're setting to achieve in this tutorial.

build Repo Aggregator
The plan is to present the full life cycle. I want it to be a step-by-step
tutorial where the reader follows along and ends up with the exact same GitHub
repo/app.

In this tutorial, we'll build that lists
our access the details page of each repo

Although it's not the main goal of the article, we'll also be building a
supporting `fauna-seeder` app that will allows us to populate the FaunaDB
database with data from the with a single command from the terminal.

## Pre-requisites

Before you begin this guide you'll need the following:

- Node and npm installed
- A [FaunaDB account](https://dashboard.fauna.com/accounts/register) to store the data

Let's get started!

# Modelling our data

Now, focusing on the data model.
Start by identifying the data

## Writing GraphQL Schema

Introduction to the step. What are we going to do and why are we doing it?

```shell
mkdir fauna-seeder
cd eleventy-static-api
```

"From within the project directory root, run the following commands":

"Then let's add an empty package.json file:"

```shell
npm init -y
```

"And then add a .gitignore file to ignore the node_modules directory:"

```shell
echo "node_modules" > .gitignore
```

Once everything is installed,

https://www.youtube.com/watch?v=KlUPiQaTp0I
FaunaDB has a native GraphQL layer
"allows you to import a graphql shcema and get an instant graphql endpoint"

Let's begin by writing our GraphQL schema:

```graphql
type Repo {
  projectName: String! @unique
  repoUrl: String! @unique
  svgLogo: String!
}

type Query {
  allRepos: [Repo]!
}
```

One entity `Repo`

A repo has project name, a repository url, and svg logo. `@unique` defines
that?attributes? doesn't allow to store repos with the same same or the same
repo url

Then we also added a `Query`, we want a list off all repos that are store in the
database `allRepos`

## Creating the database

Introduction to the step. What are we going to do and why are we doing it?

"From here, we’ll log into FaunaDB and create our first data set. We’ll start by
creating a new Database called “bookmarks.” Inside a Database, we have"
Collections, Documents and Indexes.
Create a new database which we will call `repos`.

SCREENSHOT

## Importing the schema

Introduction to the step. What are we going to do and why are we doing it?
feature that allows us to import a schema:

- Import the schema
  printscreen
  Fauna will make the collections, the indexes that are necessary

- Setting an index for the slug
- Defining the queries that will be used:

1. listing the film catalogue
2. fetching a film's details
3. fetching a film's rating

Import schema in Fauna

SCREENSHOT

## Seeding data Fauna

Introduction to the step. What are we going to do and why are we doing it?

Get back to our `fauna-seeder` to add the data

Feel free to tweak this file to reflect your favorite projects:

"This creates the basis for the information we’ll need to pull from our bookmarks
as well as provides us with our first set of data to pull into our template."

"you can use JSON data files or JavaScript data files. By utilizing a JavaScript
file, we can actually make our API calls and return the data directly into a
template."

```shell

```

We’re going to use a tool `faunadb`.
Client JS
FQL functional, composable

## Env vars

Go to the Fauna dashboard tab and take a first heap snapshot. First, we build
our project in dev mode, in order to access the right local environment
variables:

"From here, we just need to . That’s where mapBookmarks() comes in!"

# Creating and configuring our Nuxt.js app

Introduction to the step. What are we going to do and why are we doing it?
"We will start by initializing the project with the Nuxt.js create app template."

```shell
npx create-nuxt-app repo-aggregator
```

"Navigate through the guide and choose the following options:"

    package manager of your choice

    none UI Framework

    none custom server framework

    no modules needed

    choose Universal rendering mode (SSR)

Let's also install the required dependencies:
Fauna driver we've used to populate the database in the seeder app.

```
npm install faunadb
```

## Env vars

First, we build our project in dev mode, in order to access the right local
environment variables:

Now `cd repo-aggregator` and edit `nuxt.config.js` the central point of a Nuxt
app

Add the following to your nuxt.config.js:

## Setting up the Project

- Implementing the page layouts
- Implementing the API calls

Let’s break this down.

## Creating our pages

Introduction to the step. What are we going to do and why are we doing it?

Site will have 2 static pages:

- index.vue: latest articles about Nuxt.js will be listed
- \_slug.vue: most popular articles for last year period.

"Let’s imitate DEV.TO URL structure for our simple app. Our pages folder should
look like this"

```shell
├── index.vue
├── repos
│   └── _slug.vue
```

Let's add some CSS to give some style to our app

## Running nuxt generate

Introduction to the step. What are we going to do and why are we doing it?

"Since we want the bookmarks to be rendered in HTML and not fetched by the
browser, we’ll need something to do the rendering."

Pre-generate a route for each repo of the collection

- Building our static website from Fauna data
- Dynamically generating routes/pages

build your Nuxt.js project:

```shell
npm run generate
```

And that's it!
"You have a working static"

# Adding dynamic content to our website

Probably the main objection for static/pre-rendered sites is "I don't want to
have to rebuild the entire site" every time something changes in the database.

- Addressing the argument "I don't want to have to rebuild my website every time
  something changes in the database"

It's a totally valid/comprehensible argument - a nuanced one nonetheless. For
example, a Netlify hosted website won't have any downtime while the build/deploy
of a new version takes place. So what is the **real** downside? Maybe the time
it takes to rebuild a website with large amounts of content. And even in that
area, we're starting to see amazing progress being made, e.g. Gatsby's recently
announcing incremental builds.

But still, there are certainly some scenarios where constantly rebuilding is not
viable. Going back to our Film example, let's imagine we have a real-time user
rating of Films. Clearly, it's not practical to rebuild an entire website
whenever a user rates a Film.

Let's add some dynamic data to our Repo pages:

We'll be adding an asynchronous JavaScript API call to the Film page that gets
back its rating.

# Conclusion

In this article we've learned

static website that you can deploy on your host of choice.

One of the key ideas I want to present in this article is that it doesn't always
have to be a matter of A/B decision. We should aim for a "hybrid" solution
whenever possible, where we pre-render the most we can, and asynchronously fetch
just the data we need.

## What to do next:

"Interested in getting the code? You can grab it on Github! Take a look at the
finished product here."

[Webhooks on content change]

https://css-tricks.com/static-first-pre-generated-jamstack-sites-with-serverless-rendering-as-a-fallback/
https://css-tricks.com/static-or-not/
https://css-tricks.com/get-static/

https://jamstack.training/p/pre-generate-static-pages-with-dynamic-content
https://www.smashingmagazine.com/2019/10/bookmarking-application-faunadb-netlify-11ty/
https://css-tricks.com/consistent-backends-and-ux-why-should-you-care/?utm_source=hootsuite&utm_medium=twitter

https://fauna.com/blog/building-a-serverless-jamstack-app-with-faunadb-cloud-part-1
https://github.com/netlify/netlify-faunadb-example
https://www.netlify.com/blog/2018/07/09/building-serverless-crud-apps-with-netlify-functions-faunadb/
https://css-tricks.com/build-a-dynamic-jamstack-app-with-gatsbyjs-and-faunadb/

https://nuxtjs.org/blog/build-dev-to-clone-with-nuxt-new-fetch

https://devops.com/defining-the-database-requirements-of-dynamic-jamstack-applications/?utm_source=hootsuite&utm_medium=twitter

https://css-tricks.com/static-or-not/

https://github.com/public-apis/public-apis

https://www.youtube.com/watch?v=Qkc8p4D6JM0
