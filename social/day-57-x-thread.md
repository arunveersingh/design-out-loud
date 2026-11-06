# Day 57 — Product search: X thread draft

Status: draft for Arunveer's approval. Do not post without his OK.
Spoiler check: why "I just changed the price" still shows the old one in search, and why the catalog database is the wrong place to run shopper search. No full index design, no answer to the attempt.

---

**1/**
A merchant updates a product price. Two minutes later a shopper still sees the old one in search.

Is the site broken?

In a system design interview, the useful answer is: maybe not. 🧵

**2/**
Search is usually not reading the catalog database.

The catalog is the source of truth — the row the merchant just saved. Search is a separate copy built for fast keyword lookup and filters like brand and price range.

That copy is allowed to lag. You say the bound out loud: typically under a minute, never more than about five.

**3/**
Why a separate copy at all?

Imagine 50 million products and thousands of shoppers searching at once, each one filtering by brand and sorting by relevance. Asking the main database to do that with text-match and "count by brand" collapses it.

So you keep a search engine fed from the catalog. Writes go to the catalog first. Search catches up.

**4/**
That lag is a feature you name, not a bug you hide.

Merchant tools should read the catalog, so support sees the new price immediately. Shoppers read search. When they click through to buy, checkout reads the real price and stock again — search is a hint, not the cash register.

**5/**
Two traps that show up in interviews:

Writing the app to the database and to search with no shared order. One succeeds, the other fails, and the two copies drift forever with no alarm.

And turning every product attribute a merchant types into a filter. Too many unique filters and the search cluster melts. Brand and category are fine. A filter per serial number is not.

**6/**
Day 57 of my free 90-day system design book has the full problem: keyword search, filters, how fresh is fresh enough, and what you refuse.

Try the problem before you read the answer:
https://reasonfrom.com/courses/design-out-loud/day-57/
