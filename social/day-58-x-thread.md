# Day 58 — Video on demand: X thread draft

Status: draft for Arunveer's approval. Do not post without his OK.
Spoiler check: why "upload finished" is not the same as "ready to play," and why video files never pass through the app's own servers. No full design, no answer to the attempt.

---

**1/**
You upload a video. The progress bar hits 100%. Then the app says "processing" for ten minutes.

Why can't it just play?

In a system design interview, this gap is one of the first things worth explaining. 🧵

**2/**
The file you uploaded is one big file at one quality.

Viewers are on fast Wi-Fi, slow mobile data, and everything between. So the service makes several copies at different qualities, say 360p, 720p and 1080p. Making those copies is called transcoding, and it takes real computer time.

**3/**
Each copy is also chopped into small pieces of about 6 seconds.

The video player on your phone fetches piece after piece. If your connection drops, it switches to a lower quality for the next piece. That switching is called adaptive bitrate, and it's why a video gets blurry for a moment instead of freezing.

**4/**
So "upload finished" and "ready to play" are two different moments.

A good design says that out loud. Upload finished means the original file is safely stored. Ready means at least one quality is chopped up and playable. Many services unlock play when the lowest quality is done and add sharper ones a few minutes later.

**5/**
The other big point: the video bytes never pass through the company's application servers.

Those servers hand your phone a temporary upload link, and your phone sends the file straight to storage. Playback comes from a CDN, a network of servers spread around the world that keeps copies close to viewers.

**6/**
Why so strict? One viral video watched by 100,000 people at once at 4 megabits per second is about 400 gigabits per second.

No ordinary app server fleet should carry that. The CDN carries it. The app just keeps track of who uploaded what and whether it's ready.

**7/**
Day 58 of my free 90-day system design book has the full problem: uploads, the quality ladder, when to call a video ready, and what you refuse.

Try the problem before you read the answer:
https://reasonfrom.com/courses/design-out-loud/day-58/
