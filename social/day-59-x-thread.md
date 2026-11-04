# Day 59 — Live video: X thread draft

Status: draft for Arunveer's approval. Do not post without his OK.
Spoiler check: why the video itself isn't the hard part, and the "notify everyone at once" trap. No full design, no answer to the attempt.

---

**1/**
A famous creator goes live. A few million people tap the notification within a minute.

Designing what happens next is a classic system design interview problem. Most people worry about the wrong thing. 🧵

**2/**
The obvious worry is the video itself. 5 million viewers watching at about 3 megabits a second each is roughly 15 terabits a second.

No company serves that from its own app servers. A CDN does it: a network of servers spread around the world that keep copies close to viewers.

**3/**
Here's the trick that makes it work. The stream is cut into small video files, about 2 seconds each.

When thousands of viewers ask one CDN server for the same file at the same moment, it fetches it once from the company and hands the copy to all of them.

**4/**
So millions of requests a second at the CDN turn into a few dozen a second back at the company. The video is the easy part.

**5/**
The hard part is everything around the video. "Is this stream live?" "Is this viewer allowed to watch?" Those questions usually go to the company's own servers, and they all arrive in the same second.

**6/**
And the company often causes the rush itself. Send 20 million "they're live!" notifications in one second, and everyone taps at once.

Spread those notifications over a minute and the same viewers arrive at a pace the system can handle. In the interview, say that out loud.

**7/**
Day 59 of my free 90-day system design book has the full problem, the delay math (why live is ~10 seconds behind), and the failure most people miss.

Try the problem before you read the answer:
https://reasonfrom.com/faang-system-design-90-days/#/days/59-live-video
