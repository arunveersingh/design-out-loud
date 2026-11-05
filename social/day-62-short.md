# Day 62 — Metrics ingestion: YouTube short script (45–60 s)

Status: draft for Arunveer to record. Spoiler-light: the tag-mix math and the wrong-error-code trap. No full design.

---

**[0–4 s] Hook, to camera**
One tag on one chart can take down a monitoring system. Here's how.

**[4–16 s] Setup**
Apps send numbers to a monitoring system all day: requests, errors, speed. Each number has tags, like the page and the server. Every unique mix of tags is its own line on a chart, and the system keeps a little memory for each line.

**[16–32 s] The math**
Half a million numbers a second? That's only about 10 MB a second. Easy. But 5 methods times 10 status codes times 50 pages times 200 servers is already 500,000 lines. Add a user ID tag with a million users, and it can explode to billions. At around 4 KB each, memory runs out first.

**[32–48 s] What to say in the interview**
Give each customer a limit on new lines. And when they hit it, don't reply "try again later." Their software will retry forever. Reply "this is wrong, don't retry." Save "try again later" for customers who are just sending too fast.

**[48–58 s] Close**
That's Day 62 of my free 90-day system design book. Link's in the description. Try the problem before you read the answer.

---

On-screen text: `10 MB/s is easy` · `tag mixes multiply` · `over the limit = don't retry`
Description link: https://reasonfrom.com/faang-system-design-90-days/#/days/62-metrics-ingestion
