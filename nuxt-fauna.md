# Introduction

- Couple of paragraphs introducing Nuxt.js and FaunaDB.
- The Jamstack and how Nuxt.js (SSG) and FaunaDB (serverless) relate to it.

Nuxt.js is well known for SSR capabilites
but it can also go static.

So, what makes FaunaDB a good choice for this example?

Let me start by elaborating more on the intent of the article, as I point out in
which way Fauna's architecture and certain features, apply to this use case.

The main idea I want to present is the benefit of being able to fetch data from
a database and use it to build a static/pre-rendered site that can be served
directly from a CDN.

The concept of Jamstack is not new and its advantages have been extensively
documented before. Jamstack architectures allow us to build more secure,
scalable, faster websites.

The term "static" can be a bit misleading - that's why I like to use
"pre-rendered" interchangeably. When we build a Jamstack app, it doesn't mean we
have to compromise on dynamic data.

The widespread of functionality APIs makes way for common tasks - such as
authentication, e-commerce, and data storage - that used to be implemented over
and over, now be delegated to the experts of those specific domains.

As a serverless database, FaunaDB allows our applications to access data "as a
service". Contrary to more "traditional" databases, there's no need to host and
manage our own database. Plus, a Fauna database scales automatically and it's
globally distributed.

From a developer's perspective this is awesome, because it allows us to be more
productive and focus on the domain logic of the app we're building.

# Goals

- What we're setting to achieve in this tutorial.

Repo Aggregator
As developers our list of favourite repos to follow
Yes, sure. The plan is to present the full life cycle. I want it to be a
step-by-step tutorial where the reader follows along and ends up with the exact
same GitHub repo/app.
In this tutorial, we'll build that lists
our access the details page of each repo

## Pre-requisites

- Nuxt.js as SSG
- [FaunaDB account](https://dashboard.fauna.com/accounts/register)

# Modelling our data

Now, focusing on the data model. Let's imagine we have a Film catalogue. It's
not that often that we need to add or delete a Film from the catalogue. Do we
need to have a server query a database at each client request? Couldn't we pass
the heavy lifting to the build process?

This is one of the key points I want to concentrate on. Do we really need to
have a server make the same request, to get the same data, take those same
results, run them against a templating engine and only then deliver the response
to the client?

Writing schema.gql

- Creating the database + data model (Film)
- Setting an index for the slug
- Defining the queries that will be used:

1. listing the film catalogue
2. fetching a film's details
3. fetching a film's rating

Import schema in Fauna

## Seeding data to Fauna

Client JS

# Creating and configuring our Nuxt.js app

- Implementing the page layouts
- Implementing the API calls

# Running nuxt generate

- Building our static website from Fauna data
- Dynamically generating routes/pages

# Adding dynamic content to our website

- Addressing the argument "I don't want to have to rebuild my website every time
  something changes in the database"
- Let's add some dynamic data to our Film pages

Probably the main objection for static/pre-rendered sites is "I don't want to
have to rebuild the entire site" every time something changes in the database.

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

In the last section of the tutorial I'll address this objection. We'll be adding
an asynchronous JavaScript API call to the Film page that gets back its rating.

# Conclusion

One of the key ideas I want to present in this article is that it doesn't always
have to be a matter of A/B decision. We should aim for a "hybrid" solution
whenever possible, where we pre-render the most we can, and asynchronously fetch
just the data we need.

"So there you have it — a static API.

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
