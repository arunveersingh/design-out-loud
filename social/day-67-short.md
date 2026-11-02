# Day 67 — Collaborative document: YouTube short script (45–60 s)

Status: draft for Arunveer to record. Spoiler-light: the "last save wins" trap and the cursor math. No full design.

---

**[0–4 s] Hook, to camera**
Two people type in one Google Doc. Why doesn't one of them lose everything?

**[4–18 s] The obvious answer, and the trap**
The obvious plan: each app sends the whole document, and the server keeps the last copy it gets. But if your friend fixes a typo a split second after you write a sentence, their copy wins and your sentence disappears.

**[18–32 s] The better answer**
In the interview, say this: the server stores each small edit, like "add the letter s here", and puts every edit in one order. When two edits happen at once, it adjusts the later one so it still lands in the right place. Both survive.

**[32–48 s] One number**
Here's the surprise. Fifty people in one doc. Their typing means about 12,000 messages a second for the server to pass around. Their moving cursors? About 24,500. Cursors cost twice as much as the writing, so you send them less often. Losing a cursor is fine. Losing a word never is.

**[48–58 s] Close**
That's Day 67 of my free 90-day system design book. Link's in the description. Try the problem before you read the answer.

---

On-screen text: `last copy wins = lost words` · `store each edit, in one order` · `cursors: 2× the messages of typing`
Description link: https://reasonfrom.com/faang-system-design-90-days/#/days/67-collaborative-document
