# Day 58 — Video on demand: YouTube short script (45–60 s)

Status: draft for Arunveer to record. Spoiler-light: why "upload done" is not "ready to play," and why video skips the app's servers. No full design.

---

**[0–5 s] Hook, to camera**
Your upload hits 100%. Then it says "processing." Why can't it just play?

**[5–22 s] Many copies**
The file you sent is one quality. Viewers are on fast Wi-Fi and slow mobile data. So the service makes several copies, like 360p, 720p and 1080p, and chops each into 6-second pieces. That's transcoding, and it takes computer time. Your player grabs piece after piece and drops quality when your signal drops.

**[22–38 s] Two moments**
So "upload finished" and "ready to play" are different. Finished means the original is stored safely. Ready means at least one quality is playable. Good services unlock play at the lowest quality and add the sharp ones a few minutes later. Say that in the interview.

**[38–52 s] One refusal**
And the video never flows through the app's own servers. Your phone uploads straight to storage, and viewers watch from a CDN, servers spread around the world. One viral video can need 400 gigabits per second. Not your app's job.

**[52–58 s] Close**
That's Day 58 of my free 90-day system design book. Link's in the description. Try the problem first.

---

On-screen text: `upload done ≠ ready` · `many qualities, small pieces` · `bytes skip the app`
Description link: https://reasonfrom.com/courses/design-out-loud/day-58/
