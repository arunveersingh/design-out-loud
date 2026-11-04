# Day 59 — Live video: YouTube short script (45–60 s)

Status: draft for Arunveer to record. Spoiler-light: the video isn't the hard part, and the notification rush. No full design.

---

**[0–4 s] Hook, to camera**
When a big creator goes live, the video isn't what breaks. Here's what does.

**[4–18 s] Setup**
Five million viewers at 3 megabits each is 15 terabits a second. Nobody serves that from their own servers. A CDN does: servers all over the world holding copies close to viewers. The stream is cut into 2-second video files, and each CDN server fetches a file once and shares it with everyone nearby.

**[18–34 s] The real problem**
So the video is the easy part. What breaks is the stuff around it. "Is this stream live?" "Is this person allowed to watch?" Those questions hit the company's own servers, all in the same second.

**[34–50 s] What to say in the interview**
And often the company causes the rush. Send 20 million "they're live" notifications at once, everyone taps at once. Spread them over a minute instead. Same viewers, a pace you can handle. Say that in the interview.

**[50–58 s] Close**
That's Day 59 of my free 90-day system design book. Link's in the description. Try the problem before you read the answer.

---

On-screen text: `15 terabits/s = CDN's job` · `the rush is the questions, not the video` · `spread the notifications`
Description link: https://reasonfrom.com/faang-system-design-90-days/#/days/59-live-video
