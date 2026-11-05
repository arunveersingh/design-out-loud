# Day 60 — Nearby: YouTube short script (45–60 s)

Status: draft for Arunveer to record. Spoiler-light: distance can leak location, and why random noise fails. No full design.

---

**[0–4 s] Hook, to camera**
"Priya is 412 meters away." That one line can tell a stranger where she lives.

**[4–20 s] The trick**
Someone's phone can lie about where it is. So they pretend to stand in three places and ask the app each time. Three distances make three circles on a map. Where the circles cross, that's Priya. Researchers have found real dating-app users exactly like this.

**[20–32 s] The fix that fails**
Most people say: add a bit of random error to each answer. But ask a hundred times, take the average, and the error cancels out.

**[32–50 s] What to say in the interview**
What works: before measuring, the app moves Priya's stored spot to the center of a fixed block, about 100 to 200 meters wide. Same answer every time, so repeating teaches nothing. Show "under 1 kilometer," never meters. And if a phone jumps 5 kilometers in 3 seconds, it's lying.

**[50–58 s] Close**
That's Day 60 of my free 90-day system design book. Link's in the description. Try the problem before you read the answer.

---

On-screen text: `exact distance = location leak` · `random noise averages out` · `snap to a block, show a range`
Description link: https://reasonfrom.com/faang-system-design-90-days/#/days/60-nearby
