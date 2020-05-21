## Intro

In this article, we will build a Repository Catalogue, using Nuxt.js to generate
a static site from data being fetched from a FaunaDB database, at build time.

Pre-rendering is not all we will be doing. We will also to load additional, more
dynamic repo info, using Vue.js for client-side hydration.

The main idea I want to present is the benefit of being able to fetch data from
a database and use it to build a static/pre-rendered site that can be served
directly from a CDN. Jamstack approach

## Why a Repository Catalogue?

As developers, we all have a some projects we admire and like to keep track and
stay updated. our list of favorite projects to follow overview of the GitHub
repositories you're interested in, and displaying the information that is more
relevant to you In this example, we'll display the GitHub stars and the date of
last commit.

Let's imagine we have a Repo Catalogue of GitHub projects we would like to keep
track of. It's not that often that we need to add or delete a Film from the
catalogue. Do we need to have a server query a database at each client request?
Couldn't we pass the heavy lifting to the build process?

only some have a special place in your heart and that does not happen every day.

At the end of the tutorial, you/When we finish, you'll be able to take this
example, translate and apply it to your specific use case. "You can also adapt
this tutorial context for other real-time apps very easily."

## Jamstack

The concept of Jamstack is not new and its advantages have been extensively
documented before. Jamstack architectures allow us to build more secure, more
scalable, faster websites.

The term "static" can be a bit misleading - that's why I like to use
"pre-rendered" interchangeably. When we build a Jamstack app, it doesn't mean we
have to compromise on dynamic data.

https://css-tricks.com/build-a-dynamic-jamstack-app-with-gatsbyjs-and-faunadb/
"It’s usually a good idea to load as much data at build time as possible to
improve page performance. But if the data isn’t needed by all clients, or too
big to be sent to the client all at once, we can split things up and switch to
on-demand loading on the client. This is the case for user-specific data,
pagination, or any data that changes rather frequently and might be outdated by
the time it reaches the user.

The widespread of functionality APIs makes way for common tasks - such as
authentication, e-commerce, and data storage - that used to be implemented over
and over, now be delegated to the experts of those specific domains.

distinguishing static data and real-time data

It allows to call webmentio.io API only when building the site, which should be
less often than visitors viewing pages

https://nicolas-hoizey.com/articles/2020/05/05/jamstack-is-fast-only-if-you-make-it-so/
Using the server side build to get the webmentions provides multiple benefits:

- The performance for the users is much better, with HTML already computed on the server and statically served
- Much less API calls are made, requiring much less computing time and power

## Nuxt.js

Nuxt.js is well known for SSR capabilites
but it can also go static.

- Couple of paragraphs introducing Nuxt.js and FaunaDB.
- The Jamstack and how Nuxt.js (SSG) and FaunaDB (serverless) relate to it.

This is one of the key points I want to concentrate on. Do we really need to
have a server make the same request, to get the same data, take those same
results, run them against a templating engine and only then deliver the response
to the client?

## FaunaDB

FaunaDB is a globally "distributed database that promises to be always
consistent, always secure, low-latency" Plus, a Fauna database scales
automatically and it's globally distributed.

As a **serverless** database, FaunaDB allows our applications to access data "as
a service". Contrary to more "traditional" databases, there's no need to host
and manage our own database. zero operations no need to manage servers

From a developer's perspective this is awesome, because it allows us to be more
productive and focus on the domain logic of the app we're building.

## Goals

What we're setting to achieve in this tutorial:

- build a pre-rendered Repo Catalogue site with Nuxt.js
- helper app that we'll use to seed Fauna's database

In this tutorial, we'll build that lists our access the details page of each
repo

Although it's not the main goal of the article, we'll also be building a
supporting `fauna-seeder` app that will allows us to populate the FaunaDB
database with data from the with a single command from the terminal. It just
serves as a way of storing data in FaunaDB - in fact, you could be doing with
different

## Pre-requisites

Before you continue reading this article, I’d like to mention that a working
knowledge of the following technologies (or substitutes) are beneficial:

- JavaScript
- Vue/Nuxt
- GraphQL

The plan is to present the full life cycle. I want it to be a step-by-step
tutorial where the reader follows along and ends up with the exact same GitHub
repo/app.

Before you begin this guide you'll need the following:

- Node and npm installed
- A [FaunaDB account](https://dashboard.fauna.com/accounts/register)

Let's get started!

## Modelling our data

First things first, we begin by specifying the data model. The goal is to store
a collection of repos. Start by identifying the data and **dynamic** **static**
(data that will be stored in Fauna). Is that it's not supposed to change

The Repo entity has the **static** following properties:

- project name
- project's GitHub repo URL
- the project's logo
- the project's main color in hexa

## Writing the GraphQL schema

Resource on writing schema

In this section, we'll be creating a helper app `fauna-seeder` that we will be
responsible for populating Fauna's database.

It will also contain the GraphQL schema that represents and will be used to
create the database and what resources the database will provide.

> "GraphQL is a specification for an API query language and a server engine
> capable of executing such queries."[Production Ready GraphQL](https://book.productionreadygraphql.com/)

To begin, create a directory for the project and navigate to it:

```shell
mkdir fauna-seeder
cd fauna-seeder
```

From within the root directory, create a new git repo:

```shell
git init
```

Make git ignore the `node_modules` directory and the `.env` file:

```shell
echo "node_modules" > .gitignore
echo ".env" >> .gitignore
```

Then, let's initialize a Node project

```shell
npm init -y
```

by adding an empty package.json file:
"This creates a package.json with an initial setup for your TypeScript app."

add the Prisma CLI as a development dependency to it:"

install the required dependencies:

```shell
npm install dotenv faunadb simple-icons
```

- faunadb: JavaScript client
  FQL the native API for querying the data
  FQL functional, composable
- simple-icons
- dotenv to read Fauna's key

https://www.youtube.com/watch?v=KlUPiQaTp0I
FaunaDB has a native GraphQL layer.
That allows you to import a graphql shcema and get an "instant" graphql endpoint

"GraphQL also uses a typed schema."

Let's begin by writing our GraphQL schema. Create a new file `schemal.graphql`
and add the following:

```shell
touch schema.gql
```

```graphql
type Repo {
  projectName: String! @unique
  repoUrl: String! @unique
  svgLogo: String
  colorHex: String
}

type Query {
  allRepos: [Repo!]
}
```

We've declared the entity `Repo` to represent an individual repository in the
collection.

A repo has project name, a repository URL, and a SVG logo. `@unique` defines
that?attributes? doesn't allow to store repos with the same same or the same
repo url

We've also added a query, we want a list off all repos that are store in the
database `allRepos`, A query that asks for all the repositories.

## Creating a Fauna database

Login to your Fauna account.

We’ll start by creating a new database called `repos`. Inside a Fauna database,
we have Collections, Documents and Indexes.

SCREENSHOT

## Importing the schema

Now that the database is created, we can import our schema feature that allows
us to import a schema:

Import schema in Fauna "You can upload your schema.gql file via the FaunaDB
Console by clicking“GraphQL” on the left sidebar, and then click the “Import
Schema” button."

**PRINTSCREEN**

Fauna will make the collections, the indexes that are necessary

"Upon providing FaunaDB with a GraphQL schema, it automatically creates the
required collections for the entities in our schema (products and reviews).
Besides that, it also creates the indexes that are needed to interact with those
collections in a meaningful and efficient manner. You should now be presented
with a GraphQL playground where you can test out"

At this point we have an empty database, ready to be populated with some repo
data.

## Seeding data to Fauna

FaunaDB is a non-relational database that stores data in the form of JSON
documents. There are two ways of accessing Fauna data:

- Fauna clients
- Interactive shell

Now that we've created the database and imported the GraphQL schema, Get back to
our `fauna-seeder` to add the data
the basis for the information we’ll need to pull from the `repos` collection
to generate the static routes in the Nuxt.js app.

Go to Fauna's console and from the Security menu create a new key with the admin role
and name it FAUNA_ADMIN

**PRINTSCREEN**

Create a `.env` file:

```shell
touch .env
```

Paste the generated key directly after the variable's name:

```
FAUNA_ADMIN=
```

Let's create a function that and export a client connection to Fauna:

```shell
mkdir functions
cd functions
touch db-connection.js
```

Let's go ahead and export `db-connection.js`:

```javascript
require("dotenv").config();
const faunadb = require("faunadb");
const query = faunadb.query;

function createClient() {
  if (!process.env.FAUNA_ADMIN) {
    throw new Error("FAUNA_ADMIN key not found");
  }
  const client = new faunadb.Client({
    secret: process.env.FAUNA_ADMIN,
  });
  return client;
}

exports.client = createClient();
exports.query = query;
```

What are we doing there

## Repo data

We'll be using JSON to store the minimal set of repo data the seeder
app requires.

Each repo is represented by the three properties:

```json
{
  "projectName": "Vue.js",
  "repoUrl": "https://github.com/vuejs/vue",
  "simpleIconsName": "Vue.js"
}
```

- project **name**
- GitHub **repo URL**
- Simple Icons **brand title**

[SimpleIcons](https://simpleicons.org/) is a cool project that collects SVG
icons and colors for popular brands.

We'll be using the [simple-icons](https://github.com/simple-icons/simple-icons)
npm package - which we've installed in a previous step - to dynamically fetch
the SVG logo and the hex color code of each project, when the seeder app runs.

Create a `data.json` file:

```shell
touch data.json
```

Add the array of repos that will be written to Fauna's database using the format
shown above. You can either use the same file I've used or tweak it with your
favorite projects:

Make sure the `simpleIconsName` value exists an
https://simpleicons.org/
You can use their website to get the names of the projects you'd like.

## Running the seeder app

Create a file named `seed.js`:

```shell
touch seed.js
```

This is the code that we will run to populate the `Repo` collection:

```javascript
const { client, query } = require("./functions/db-connection");
const q = query;
const simpleIcons = require("simple-icons");
const reposData = require("./data.json");

const repos = reposData.map((r) => {
  const simpleIconsData = simpleIcons.get(r.simpleIconsName);
  r.svgLogo = simpleIconsData.svg;
  r.colorHex = simpleIconsData.hex;
  delete r.simpleIconsName;
  return r;
});

client
  .query(
    q.Map(
      repos,
      q.Lambda(
        "repo",
        q.Create(q.Collection("Repo"), {
          data: q.Var("repo"),
        })
      )
    )
  )
  .then(console.log("Repos seeded successfully in FaunaDB"))
  .catch((err) => console.log("Failed to add repo to FaunaDB", err));
```

Let's break down what we've done:

We're ready to write the repo data to Fauna:
ready to add documents to our collection

```shell
node seed.js
```

! Navigate to Fauna to confirm data was written successfully

## Nuxt Repo Catalogue

Now, let's change gears and look at the core app for this tutorial.

To get started quickly, we will initialize the project with the Nuxt's
scaffolding tool:

```shell
npx create-nuxt-app repo-catalogue
```

Go through the guide and make sure to select the following options:

- axios and dotenv in Step
- Bulma in Step "To style our app, we’ll be making use of Bulma.

Let's also install the other required dependencies:

- Fauna driver we've used to populate the database in the seeder app
- slugify will allows us to slug

```
npm install faunadb slugify
```

### Fauna Key

This step is important to keep acessible when the generate Fauna's key private
in order to access the right local environment variables:

The same way we did it before, let's creat a Fauna key - this time with Server
role
and create
use fetch data from the collection

Go to the Fauna dashboard
go to Security on the left-hand sidebar to manage the keys for the database
Create a new key -> select the database you've just created, select the Role
Admin you can name the key whatever you want and save

copy the secret key and save it as after you navigate away from this page it is
not going to be displayed again

add the API key you've just generated `.env` file:

```
FAUNA_SERVER_KEY=
```

"From here, we just need to access `process.env.FAUNA_SERVER_KEY` in order to access the right local
environment variables.

```javascript
require("dotenv").config();
```

Add edit the following to your nuxt.config.js:

## Setting up the Project

Add after build, property the generate:
routes function

```javascript
generate: {
    async routes() {
      const faunadb = require('faunadb')
      const query = faunadb.query
      const slugify = require('slugify')
      const q = query
      if (!process.env.FAUNA_SERVER_KEY) {
        throw new Error('FAUNA_SERVER_KEY not found.')
      }
      const client = new faunadb.Client({
        secret: process.env.FAUNA_SERVER_KEY
      })
      const result = await client.query(
        q.Map(
          q.Paginate(q.Match(q.Index('allRepos'))),
          q.Lambda('X', q.Get(q.Var('X')))
        )
      )
      const repos = result.data.map((repo) => repo.data)
      return repos.map((repo) => {
        const repoUrlParts = repo.repoUrl.split('/')
        return {
          route:
            '/repos/' +
            slugify(repoUrlParts[repoUrlParts.length - 1], {
              remove: /[*+~.()'"!:@]/g
            }),
          payload: repo
        }
      })
    }
}
```

Let’s break down the different steps of the code snippet:

- Import the PrismaClient constructor from the @prisma/client node module
- Instantiate PrismaClient
- Define an async function called main to send queries to the database
- Call the main function
- Return the database connections when the script terminates

## Creating our pages

The Site will have two pages:

- index.vue: where all repos will be listed
- \_slug.vue: repo details page

structure for our app. Our pages folder should look like this:

```shell
├── index.vue
├── repos
│   └── _slug.vue
```

Start with `pages/index.vue` and replace it with:

Create the file `pages/repos/_slug.vue` and replace it with:

## Running Nuxt generate

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

And that's it! You have now a working static site in the `dist` folder, that can
be served directly from a CDN!

## Adding dynamic content to our Repo Catalogue

Probably the main objection to static sites is "I don't want to have to rebuild
the entire site every time something changes in the database".

It's a totally valid argument - a nuanced one nonetheless. For example, a
Netlify hosted website won't have any downtime, while the build/deploy of a new
version takes place.

So what is the **real** downside? Maybe the time it takes to rebuild a website
with large amounts of content. And even in that domain, we're starting to see
amazing progress being made, e.g. Gatsby's recently announcing [incremental
builds](https://www.gatsbyjs.org/blog/2020-04-22-announcing-incremental-builds/).

But still, there are certainly some scenarios where constantly rebuilding the
site is not viable.

Going back to our Repo Catalogue, suppose we want to add some dynamic data, such
as stars and forks.

Clearly, it's not practical to rebuild an entire website whenever this type of
data changes.

In this section, we'll be adding some dynamic data to our Repo detail pages.
We'll be adding an asynchronous JavaScript API call to the GitHub API page that
gets back its rating.

The request for this data will be made client-side and we'll rely on Vue's
reactivity to display, we have to add some code `/pages/repos/_slug.js`:

```javascript
mounted() {
  this.$nextTick(async () => {
    const result = await this.$axios.$get(
      `https://api.github.com/repos/ramigs/dynamic-order-change-html`)
    this.repoData = result
  })
}
```

Now run generate again so that this client-side code is included in the app's
bundle:

```shell
npm run generate
```

## Conclusion

In this article we've built a Repo Catalogue static website that you can deploy
on your host of choice.

"In this article, we implemented an approach that loads part of the data at build
time, and then loads the rest of the data in the frontend as the user interacts
with the page."

"Interested in getting the code? You can grab it on Github! Take a look at the
finished product here."

"The code for this tutorial can be found here." `fauna-seeder` `repo-catalogue`

We've also learned/seen covered One of the key ideas I want to present in this article is
that it doesn't always have to be a matter of A/B decision. We should aim for a
"hybrid" solution whenever possible, where we pre-render the most we can, and
asynchronously fetch just the data we need.

### What to do next

### Acknowledgements

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

https://nuxtjs.org/blog/build-dev-to-clone-with-nuxt-new-fetch

https://devops.com/defining-the-database-requirements-of-dynamic-jamstack-applications/?utm_source=hootsuite&utm_medium=twitter

https://css-tricks.com/static-or-not/

https://github.com/public-apis/public-apis

https://www.youtube.com/watch?v=Qkc8p4D6JM0

https://www.dropbox.com/scl/fi/slltsmir86il06sniimsk/Jamstack-and-the-power-of-serverless-databases-with-FaunaDB.-Part-1..paper?dl=0&rlkey=063ep7p59fo45lkioriwaqflt
https://www.dropbox.com/scl/fi/gjhybns9hdsgbxyskjcgb/Jamstack-and-the-power-os-serverless-databases-with-FaunaDB.-Part-2..paper?dl=0&rlkey=9xqnw4md4tvvdqrzuw3a4y55g
