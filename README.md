# Bikini Bottom Election
![Bikini Bottom](images/bikini-bottom.jpg)

## Overview
Bikini Bottom needs a new mayor. And it's up to the brave students of Bitmaker to bring the vote to the people.

This assignment has two parts:

1. Listing out the current election results
1. Allowing the user to vote.

We're providing you with a server to interact with for this assignment. You'll be communicating with it through a JSON API.  The API will give you the current election results, and allow you to submit a vote.

It's up to you to build a wonderful frontend for this API. You will be writing exclusively client-side code for this assignment, no Rails app.

Let's get started!

![The](images/the.jpg)

## Part 0 - Setup and Review
**First**, I recommend installing a browser addon to help you test making requests to the API. You can always use the command line program `curl` to make requests, but it can be a little intimidating.

A couple great choices are [Postman](https://www.getpostman.com/), which has both a Chrome App and native Mac App, or [RESTClient](https://addons.mozilla.org/en-US/firefox/addon/restclient/), an addon for Firefox.

**Second**, go ahead and fork the repo at https://github.com/bitmakerlabs/bb-election, where you'll find the skeleton of the frontend you'll be building.

**Third**, make sure you understand the various parts of a request. Here's a quick review. When you make a request:
1. There's a **method**: `GET`, `POST`, `PUT`, `PATCH`, or `DELETE`. We'll be using `GET` to retrieve data, and `POST` to submit data.
1. The **url**, broken down into three parts: `host`, `path`, and `query string`
  + The **host** is the part of the url that doesn't change on a website.  If you go to https://alexa.bitmaker.co/weeks/current, the host is `alexa.bitmaker.co`
  + The **path**, or the route we're taking in the website. In https://github.com/bitmakerlabs/bb-election, the path is `/bitmakerlabs/bb-election`
  + The **query string** contains additional parameters not in the path.  In https://www.google.ca/search?q=spongebob, the query is everything after `?`, `q=spongebob`
1. The **body**. In a `GET` request, the body is empty, but in a `POST`, it'll contain data. This data can be in multiple formats, but today it'll all be JSON, like this: `{ "key" : "value" }`

There are other parts as well, but this is what you'll need for this assignment.

The server you'll be interacting with is deployed at:
+ https://bb-election-api.herokuapp.com/

If you go to it now in your browser, you'll see some lovely JSON data!

## Part 1 - Listing Current Results
![List](images/list.png)

As you may have already seen if you went to the API in your browser, our first endpoint, the one that returns election results, lives at **the root of our API**, or `/`.

Making a `GET` request to `https://bb-election-api.herokuapp.com/` is going to return a bunch of JSON data to us, that'll look something like this:
```json
{
  "candidates": [
    {
      "id": "577805c3e30089e66c1ede16",
      "name": "Spongebob",
      "votes": 2
    },
    {
      "id": "577805c3e30089e66c1ede18",
      "name": "Squidward",
      "votes": 1
    },
    {
      "id": "577805c3e30089e66c1ede19",
      "name": "Sandy",
      "votes": 0
    },
    {
      "id": "577805c3e30089e66c1ede17",
      "name": "Patrick",
      "votes": 0
    },
    {
      "id": "577805c3e30089e66c1ede1a",
      "name": "Gary",
      "votes": 1
    }
  ]
}
```

Let's think about what's being returned. The response contains an array called `candidates`, which contains an object representing each candidate in the election. Each one has:
+ An `id`
+ A `name`
+ A `votes` count

**NOTE:** If you're observant, you may have noticed that the ids look a little different than what you're used to seeing. That's because the server uses MongoDB as a database instead of Postgres or SQLite like normal, and the ids are [hex strings](https://en.wikipedia.org/wiki/Hexadecimal) instead of numbers. Don't stress out about this, to the frontend, **it doesn't actually matter** what technology the server is using, it not going to change how we interact with the server, except that our ids will be `Strings` instead `Numbers`.

Ok, so we can make a `GET` request to our server and get a bunch of data back, but what do we do with this data?

### TODO
1. In the `election.js` file, build an AJAX request that makes a `GET` request to the root path, and expects a JSON response.
  + *Make sure you're making this request within your `$(document).ready` block*
1. Add a `<ul>` to your `index.html` file, with an id.  This is what we'll attach our data to.
1. In the `.done(function(data){})` **callback function** for the AJAX request, parse the JSON with `JSON.parse(data)`, loop over the candidates, and [append](http://api.jquery.com/append/) a `<li>` element for each candidate into the DOM at our `<ul>` from the last step. You'll want to show the `name` and `votes` count of each candidate.
1. Add a **"Refresh"** button or link to the `index.html` file. Create a `click` event handler for this button in `election.js`. When it's clicked, update the vote counts of the various candidates. You'll have to update the existing `<li>` elements now instead of appending.
1. **STRETCH:** Since we're making the same AJAX request in two seperate places, when we load the page and when we hit **"Refresh"**, DRY up your code a bit by putting this AJAX call into a function that we can call.

## Part 2 - Voting
![Wish](images/wish.jpg)

Now that we've covered listing out the candidates and their vote count, lets add some buttons that'll let us vote for a candidate!

We're gonna be making use of a second endpoint in our API to do this, `POST /vote`. This endpoint requires either an `id` or a `name` be given to it, so that it knows who the vote is going to.  We can insert this info either into the **query string** or the **body** of the request:
```js
// THESE TWO ARE THE EQUIVALENT
  method: POST,
  url: "https://bb-election-api.herokuapp.com/vote?id=577805c3e30089e66c1ede19",
  body: {}
// ============================
  method: POST,
  url: "https://bb-election-api.herokuapp.com/vote",
  body: { "id" : "577805c3e30089e66c1ede19" }

// AND THESE TWO ARE EQUIVALENT
  method: POST,
  url: "https://bb-election-api.herokuapp.com/vote?name=Gary",
  body: {}
// ============================
  method: POST,
  url: "https://bb-election-api.herokuapp.com/vote",
  body: { "name" : "Gary" }
```

If you want to see it in action, try sending some requests through one of the addons I mentioned above.
+ If you receive a response with a `200` status, and message of `Ok`, it worked!
+ If you get a `400`, you left out `id` and `name`.
+ If you get a `404`, it means that there's no candidate matching the `id` or `name` you entered.
+ `500` means there's a server error of some kind. Either you encountered a bug, or something crashed on the server.

Alright, let's make it happen.

### TODO

1. Add a vote `<button>` or `<a>` tag to each candidate, under the `<li>` element. You'll have to add these buttons with jQuery after your first load of the candidates completes, because the candidates won't be populated until this request completes.
1. Each one should have a `data-id` or `data-name` attribute where we'll store the id or name of the candidate. Update your code from **Part 1** where you're inserting the `<li>` element to also insert the candidate's id or name into one of these `data-` attributes.
1. Create a `click` event handler for these buttons. A couple tips:
  + Remember that your page doesn't have vote buttons until after your first load of the candidates, so you can't define your click handler until this request completes. If you try to define your click handler on page load, it won't work.
  + You should only need one event handler for every vote button, you won't need a seperate handler for each button.
1. Construct a `POST` request to `https://bb-election-api.herokuapp.com/vote`:
  + This request should be made whenever the `click` event from one of our vote buttons is fired.
  + Get the `name` or `id` of the candidate who's button was pressed from the `data-` attribute you defined in step 2. You can make use of `$(this).data('id')` or `($this).data('name')` in your click handler for this.
  + Put the `name` or `id` in either the **query string** or the **body** of the `POST` request we're making. Try both!
  + Once we click the vote button, and our `POST` request is sent, make sure that we're receiving a `200` (a success) status code in response to our AJAX request. You'll need to chain another function to your AJAX request in addition to `.done`, called `.fail`. `.done` is triggered when the request completes successfully, `.fail` when it does not. If you're getting back a different status code, something isn't quite right with your request, review the codes listed out above.
1. **STRETCH:**
  + Update the vote counts after a vote
  + Disable the vote buttons after a vote (it's fine if they reenable after refresh)

## We're done!

Congratulations, you've helped bring Bikini Bottom into the 21st century. A winner is you!

![To-Do](images/to-do.jpg)

