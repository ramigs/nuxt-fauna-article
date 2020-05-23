# Building a static Nuxt.js site with FaunaDB

In this article, we will build a Repository Catalogue, using Nuxt.js to generate
a static site, from FaunaDB data.

Pre-rendering at build time is not all we will be doing. We will also display
additional, more dynamic repo info, using Vue.js for client-side hydration.

Check out the finished product [here](https://elegant-hopper-28219e.netlify.app/).

## Why a Repository Catalogue?

As developers, we all have projects we admire/find interesting/look up to, and
like to keep track of.

The Repository Catalogue will serve as a collection of GitHub projects, that you
can customize to display repo information that is more relevant to you.

Although we'll be using a very concrete example implementation, the main
underlying idea presented is the benefit of pre-rendering static site being able
to fetch data from a database and use it to build a static/pre-rendered site
that can be served directly from a CDN.

After all, it's not _that_ often we need to add or delete a repo from the
catalogue. only some have a special place in your heart and that does not happen
every day.

so right away we see two categories of data, some that changes frequently and
some that does not. What if we?

Making us question: "Do we really need to keep makimg the same request, to get
the same data, take those same results, run them against a templating engine and
only then deliver the response to the client?

At the end, you'll be able to take this example, adapt and apply it to your
specific use case. You can also translate this tutorial context for other
real-time apps very easily, which we'll discuss later.

## Jamstack

The concept of Jamstack is not new and its advantages have been extensively
documented before. Jamstack architectures allow us to build more performant,
more secure, and more scalable websites.

The term "static" can be a bit misleading - that's why we see "pre-rendered"
being used interchangeably. When we build a Jamstack app, it doesn't mean we
have to compromise on dynamic features or dynamic data.

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

Nuxt.js is an MIT-licensed, open-source web application framework built on top
of Vue.js. It is well known for its SSR capabilities, but it can also do static.

We'll be using Nuxt to do the heavy lifting during the build stage. Instead of
having to at each client request?

## FaunaDB

FaunaDB is a globally distributed, low-latency database that promises to be
always consistent and always secure.

As a serverless database, FaunaDB allows our applications to access data "as a
service". Contrary to more "traditional" relational databases, there's no need
to host and manage our own database. zero operations no need to manage servers
and scales automatically

From a developer's perspective this is awesome, because it allows us to be more
productive and focus on the domain logic of the app we're building.

## Goals

What we're setting to achieve in this tutorial:

- building a pre-rendered Repo Catalogue site with Nuxt.js
- building a helper app that we'll use to seed Fauna's database

Although it's not the main focus of the article, the supporting `fauna-seeder`
app that will allows us to populate the FaunaDB database with a single command
from the terminal. It's just one a way of storing data in FaunaDB - in fact, you
could be doing with different ways.

## Pre-requisites

Before we move on, I’d like to mention that although not mandatory, a working
knowledge of the following technologies is beneficial:

- JavaScript
- Vue.js/Nuxt.js
- GraphQL

This will be a complete step-by-step tutorial, where the reader follows along
and ends up with the exact same site.

Before you begin, you'll need:

- Node and npm installed
- A [FaunaDB account](https://dashboard.fauna.com/accounts/register)

Let's get started! diving head on

## Modelling our data

First things first, we begin by specifying the data model. The goal is to store
a collection of repos in the Fauna database.

Each repo is represented by the following properties:

- name
- GitHub repo URL
- SVG logo
- main color hex code

## Writing the GraphQL schema

Resource on writing schema

In this section, we'll be creating the helper app `fauna-seeder` that will
populate the database.

It will also contain the GraphQL schema that represents and will be used to
create the database and what resources the database will provide.

> "GraphQL is a specification for an API query language and a server engine
> capable of executing such queries."[Production Ready GraphQL](https://book.productionreadygraphql.com/)

Create a directory for the project and navigate to it:

```shell
mkdir fauna-seeder
cd fauna-seeder
```

From within the root directory, create a new git repo:

```shell
git init
```

Configure git to ignore the `node_modules` directory and the `.env` file:

```shell
echo "node_modules" > .gitignore
echo ".env" >> .gitignore
```

Then, let's initialize a Node project:

```shell
npm init -y
```

This creates a `package.json` file where we can install the required
dependencies:

We've installed three dependencies:

- faunadb: JavaScript driver for FaunaDB
- simple-icons: [SimpleIcons](https://simpleicons.org/) npm package
- dotenv: to store and load Fauna's secret key from a `.env` file

```shell
npm install dotenv faunadb simple-icons
```

https://www.youtube.com/watch?v=KlUPiQaTp0I
FaunaDB has native GraphQL support that allows us to import a GraphQL schema and
get an "instant" GraphQL endpoint.

"GraphQL also uses a typed schema."

FQL the native API for querying the data FQL functional, composable

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

We've also declared a query `allRepos`, to list off all repos that are stored in
the database.

## Creating a Fauna database

Log in to your Fauna account.

Visit the [dashboard](https://dashboard.fauna.com/) and create a new database
and name it `repos`.

**PRINTSCREEN**

## Importing the schema

Now that the database is created, we can import the GraphQL schema into FaunaDB.

Import schema in Fauna "You can upload your schema.gql file via the FaunaDB
Console by clicking“GraphQL” on the left sidebar, and then click the “Import
Schema” button."

**PRINTSCREEN**

Inside a Fauna database, we have Collections, Indexes and Documents.

"FaunaDB automatically created the necessary collection for the `Repo` entity.
Additionally, It also creates the indexes that to support the schema."

"Besides that, it also creates the indexes that are needed to interact with
those collections in a meaningful and efficient manner."

At this point we have an empty database, ready to be populated with some repo
data.

## Seeding data to Fauna

FaunaDB is a non-relational database that stores data in the JSON format. There
are two ways of interacting with Fauna data:

- Fauna drivers (available in several programming languages)
- Interactive shell using The Fauna Query Language (FQL)
- GraphQL Playground

We'll use the JavaScript [driver](https://github.com/fauna/faunadb-js), that
we've already installed in a previous step.

The driver requires a Fauna Admin Key in order to guarantee that it has the
correct permissions to access and write data in the `repos` database.

Go to the Fauna dashboard go to Security on the left-hand sidebar to manage the
keys for the database Create a new key -> select the database you've just
created, select the Role Admin you can name the key whatever you want and save
Go to the Fauna dashboard and from the Security menu create a new key with the
admin role, and name it `FAUNA_ADMIN`:

**PRINTSCREEN**

Create a `.env` file in the root directory of the `fauna-seeder` app:

```shell
touch .env
```

copy the secret key and save it as after you navigate away from this page it is
not going to be displayed again
Paste the generated key right after the variable's name:

```
FAUNA_ADMIN=
```

"From here, we just need to access `process.env.FAUNA_SERVER_KEY` in order to access the right local
environment variables.

Let's create a function that handles a client connection to Fauna:

```shell
mkdir functions
cd functions
touch db-connection.js
```

Add the following to `db-connection.js`:

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

We'll be using JSON to store the minimal set of repo data the seeder app
requires.

Each repo is represented by three properties:

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

[Simple Icons](https://simpleicons.org/) is a cool project that collects SVG
icons and colors for popular brands.

We'll be using their [npm package](https://github.com/simple-icons/simple-icons)
to dynamically fetch the SVG logo and the hex color code of each project, when
the seeder app runs.

Create a `data.json` file:

```shell
touch data.json
```

Using the format shown above, add an array of repos that will be written to
Fauna's database. You can either use the same file I've used or tweak it to
feature your favorite projects.

Make sure the `simpleIconsName` value exists in the Simple Icons collection. You
can use the search feature on the [website](https://simpleicons.org/) to get the
correct names of the projects you're adding.

## Running the seeder app

Create a file named `seed.js`:

```shell
touch seed.js
```

This is the code that will run to populate the `Repo` collection:

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
  .then(console.log("Repos seeded successfully to FaunaDB"))
  .catch((err) => console.log("Failed to add repo to FaunaDB", err));
```

Let's break down what we've done there:

We're ready to add documents to the `Repo` collection:

```shell
node seed.js
```

Navigate to "Collections" from the sidebar menu, and confirm that the data was
written successfully.

**PRINTSCREEN**

## Nuxt Repo Catalogue

Now, let's change gears and look at this tutorial's core app.

To get started quickly, we'll initialize the project using Nuxt's scaffolding
tool:

```shell
npx create-nuxt-app repo-catalogue
```

Go through the steps and select the following options:

- axios and dotenv in the modules step
- Bulma in the UI framework step

Once the tool finishes creating our Nuxt app, install also the other required
dependencies:

- faunadb: JavaScript driver for FaunaDB
- slugify: to generate slugs from repo names

```
npm install faunadb slugify
```

### Fauna key

The same way we did for the `fauna-seeder` app, let's create a new Fauna key - this
time with "Server" role, fetch data from the collection.

**PRINTSCREEN**

Edit the `.env` file and paste the key you've just generated.

```
FAUNA_SERVER_KEY=
```

In the top of `nuxt.config.js` require and configure dotenv:

```javascript
require("dotenv").config();
```

## Routes

The Repo Catalogue website will respond in two types of routes:

- one home page, where all repos are listed
- several repo detail pages, one for each repo in the catalogue

The `pages` folder will look like this in Nuxt:

```shell
├── index.vue
├── repos
│   └── _slug.vue
```

Since we want to pre-render the site's pages, we need to tell Nuxt which routes
to [generate](https://nuxtjs.org/api/configuration-generate).

The routes for the individual repo pages have to be generated
[dynamically](https://nuxtjs.org/guide/routing#dynamic-routes).

In Nuxt, we do this by setting the generate.routes property to an array of
dynamic routes.

When running `nuxt generate`, Nuxt.js will use the configuration defined in the
`generate` property, to pre-render the site.

- Define an async function called main to send queries to the database

Let's add the `generate` property in `nuxt.config.js`:

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
      const routes = repos.map((repo) => {
        const repoUrlParts = repo.repoUrl.split('/')
        const slug = slugify(repoUrlParts[repoUrlParts.length - 1], {
          remove: /[*+~.()'"!:@]/g
        })
        repo.slug = slug
        return {
          route: '/repos/' + slug,
          payload: repo
        }
      })
      routes.push({
        route: '/',
        payload: repos
      })
      return routes
    }
  }
```

It's quite some code. So, let’s review the different steps of the snippet:

- Import the `faunadb` driver from `node_modules`
- Import the `slugify` package from `node_modules`
- Load the Fauna secret key from `.env`
- Instantiate a Fauna client using the secret key
- Fetch the entire repo collection using the `allRepos` Index
- Go through each repo, generate a slug and return an object with the route path
  and the repo data as its payload pass along the entire user object to the
  context in \_id.vue.
- Add the route for the home
- Return the array of routes that should be generated

## Creating the site pages

Start with `pages/index.vue` and replace the existing `<script>` with:

```vue
<script>
export default {
  asyncData({ params, error, payload, $axios }) {
    return { repos: payload };
  },
};
</script>
```

Now we can access the payload from /users/\_id.vue like so:

Start with `pages/index.vue` and replace the existing `<template>` with:

```vue
<template>
  <section class="section">
    <div class="container">
      <h1
        :style="{ marginBottom: '5rem' }"
        class="title has-text-centered is-size-1"
      >
        Repo Catalogue
      </h1>
      <div class="columns is-multiline">
        <div
          v-for="repo in repos"
          :key="repo.projectName"
          class="card column is-3"
        >
          <div
            :style="{ backgroundColor: '#' + repo.colorHex }"
            class="card-image"
          >
            <a :href="`/repos/${repo.slug}`">
              <figure
                :style="{ maxWidth: '20%' }"
                v-html="repo.svgLogo"
              ></figure>
            </a>
          </div>
          <div class="card-content">
            <div class="media">
              <div class="media-content">
                <h3 class="title">{{ repo.projectName }}</h3>
                <a class="subtitle" :href="repo.repoUrl">GitHub</a>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
```

Create the file `pages/repos/_slug.vue` and replace it with:

Now we can access the payload from /users/\_id.vue like so:

## Running Nuxt generate

Now that we've created the page templates, we have everything we need to build
our website, with dynamically generated routes, from Fauna data.

Build your Nuxt.js Repo Catalogue:

```shell
npm run generate
```

And that's it! You have now a working static site in the `dist` folder, that can
be served directly from a CDN!

## Adding dynamic content to our Repo Catalogue

Probably the main objection to static sites is "I don't want to have to rebuild
the entire site every time something changes in the database".

It's a totally valid argument - a nuanced one nonetheless. For example, a
[Netlify](https://www.netlify.com/) hosted website won't have any downtime,
while the build/deploy of a new version takes place.

So what is the **real** downside? Maybe the time it takes to rebuild a website
with large amounts of content. And even in that domain, we're starting to see
amazing progress being made, e.g. Gatsby's recently announcing [incremental
builds](https://www.gatsbyjs.org/blog/2020-04-22-announcing-incremental-builds/).

But still, there are certainly some scenarios where constantly rebuilding the
site is not viable.

Going back to our Repo Catalogue, suppose we want to add some dynamic data to
the detail pages, such as stars and forks.

Clearly, it's not practical to rebuild an entire website whenever this type of
data changes.

In this section, we'll be adding some dynamic data to the Repo detail page.
We'll be adding an asynchronous JavaScript API call to the GitHub API page the
repo's information.

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

Now, tell Nuxt to generate the site again, so that this client-side code is
included in the app's bundle:

```shell
npm run generate
```

## Conclusion

In this article, we've built a Repo Catalogue static website that you can deploy
on a host of your choice.

"we implemented an approach that loads part of the data at build time, and then
loads the rest of the data in the frontend as the user interacts with the page."

"Interested in getting the code? You can grab it on Github!

"The code for this tutorial can be found here." `fauna-seeder` `repo-catalogue`

We've also learned/seen covered One of the key ideas I want to present in this article is
that it doesn't always have to be a matter of A/B decision. We should aim for a
"hybrid" solution whenever possible, where we pre-render the most we can, and
asynchronously fetch just the data we need.

### What to do next

- Host on Netlify
- [Webhooks on content change] - trigger build

### Acknowledgements

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
