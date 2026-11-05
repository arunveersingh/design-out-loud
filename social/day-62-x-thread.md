# Day 62 — Metrics ingestion: X thread draft

Status: draft for Arunveer's approval. Do not post without his OK.
Spoiler check: the "bytes are cheap, combinations are expensive" idea and the wrong-error-code trap. No full design, no answer to the attempt.

---

**1/**
Every app you use sends a stream of small numbers to a monitoring system: how many requests, how many errors, how slow.

Designing the system that collects those numbers is a classic system design interview problem. Most people worry about the wrong thing. 🧵

**2/**
Each number comes with tags that say what it describes. "Requests, method GET, status 200, page /checkout."

Every unique mix of tags becomes its own line on a chart. The system has to keep a small record in memory for every one of those lines.

**3/**
The obvious worry is volume. Say 500,000 numbers arrive every second.

That sounds scary, but it's only about 10 MB a second. A normal server handles that.

**4/**
The real danger is the number of tag mixes.

5 methods × 10 status codes × 50 pages × 200 servers = 500,000 lines from one measurement. Now an engineer adds a "user ID" tag with a million users. The possible lines jump to 500 billion.

**5/**
If each line costs about 4 KB of memory, 100 million lines needs over a terabyte of memory once you keep 3 copies. One customer with one bad tag can take the whole system down overnight.

So in the interview, give every customer a limit on new lines, and say it out loud.

**6/**
One more trap. When a customer hits the limit, which error do you send back?

If you send "too busy, try again later", their software keeps retrying forever, because waiting won't fix it. Send "this request is wrong, don't retry" instead. Save "try again later" for when they're just sending too fast.

**7/**
Day 62 of my free 90-day system design book has the full problem, the memory math, and how to stop one customer from hurting the other 4,999.

Try the problem before you read the answer:
https://reasonfrom.com/faang-system-design-90-days/#/days/62-metrics-ingestion
