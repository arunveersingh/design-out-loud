# Day 67 — Collaborative document: X thread draft

Status: draft for Arunveer's approval. Do not post without his OK.
Spoiler check: the "last save wins" trap and the cursor math. No full design, no answer to the attempt.

---

**1/**
You and a friend type in the same Google Doc at the same time. Both of your words show up, in the right places, within a second.

Designing that is a classic system design interview problem: the shared document. Here's the trap most people walk into. 🧵

**2/**
The obvious plan: each person's app sends the whole document to the server, and the server keeps whichever copy arrived last.

Simple. And wrong.

**3/**
Picture it. You type a sentence. Your friend fixes a typo in another paragraph at the same moment.

Your friend's copy reaches the server a split second after yours. The server keeps it. Your sentence is gone, and nobody did anything wrong.

**4/**
In the interview, say this instead: the server doesn't store whole copies. It stores each small edit ("add the letter s here", "delete this word") and puts all of them in one order.

When two edits happen at once, the server adjusts the later one so it still lands in the right spot. Both edits survive.

**5/**
Now a surprise. What sends more messages: the typing, or the moving cursors that show where everyone is?

50 people in one doc, each typing 5 edits a second. The server passes each edit to the other 49 people. That's about 12,000 messages a second.

**6/**
Cursors usually move more often than people type. At 10 cursor updates a second each, it's about 24,500 messages a second.

The cursors cost twice as much as the actual writing. So you send cursor updates less often, and it's fine if a few get lost. Lost words are never fine.

**7/**
Day 67 of my free 90-day system design book has the full problem, a worked example with three letters, and what happens when someone edits offline for a day.

Try the problem before you read the answer:
https://reasonfrom.com/faang-system-design-90-days/#/days/67-collaborative-document
