# Day 60 — Nearby: X thread draft

Status: draft for Arunveer's approval. Do not post without his OK.
Spoiler check: why "how far away is this person" can reveal where they live, and why random noise doesn't fix it. No index choice, no full design, no answer to the attempt.

---

**1/**
"Show me people near me" sounds like one of the easiest features to build.

It's a classic system design interview problem, and the dangerous part isn't the speed. It's the number you show on screen. 🧵

**2/**
Say an app tells you: "Priya is 412 m away."

Feels harmless. You don't see her address, just a distance.

**3/**
Now picture someone who wants to find her. Their phone can lie about where it is.

They pretend to stand in three different spots and ask the app each time. Three spots, three distances, three circles drawn on a map. The circles cross at one point: where Priya is.

Researchers have found real dating-app users this way.

**4/**
The first fix most people suggest: add a bit of random error to each answer.

It doesn't work. Ask 100 times, take the average, and the random error cancels out.

**5/**
What works is boring and strict. The app quietly moves Priya's stored position to the center of a fixed block, about 100–200 m wide, before it measures anything. Every query gets the same answer, so asking 100 times teaches the attacker nothing new.

Then show a range like "under 1 km," never meters.

**6/**
And watch the person asking. If someone's phone claims to jump 5 km in 3 seconds, it's lying about its location.

In the interview, say this before anyone asks: privacy is a requirement, not an afterthought.

**7/**
Day 60 of my free 90-day system design book has the full problem, the map math, and what happens when one downtown block gets 30,000 drivers at once.

Try the problem before you read the answer:
https://reasonfrom.com/faang-system-design-90-days/#/days/60-nearby
