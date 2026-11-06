# Day 57 — Product search: YouTube short script (45–60 s)

Status: draft for Arunveer to record. Spoiler-light: search can show an old price on purpose, and why. No full design.

---

**[0–5 s] Hook, to camera**
Merchant changes a price. Two minutes later search still shows the old one. Broken site — or working as designed?

**[5–22 s] Two copies**
In most shops, search is not reading the catalog database. The catalog is the truth the merchant just saved. Search is a separate copy built so thousands of people can type keywords and filter by brand without melting the main database.

That copy is allowed to trail by up to about five minutes. You say that bound in the interview before anyone asks.

**[22–40 s] Who reads what**
Merchant tools read the catalog, so support sees the new price now. Shoppers read search. When they buy, checkout checks the real price and stock again. Search is a hint. It is not the cash register.

**[40–52 s] One refusal**
And never let the app write the database and search with no shared order. One write can succeed and the other fail, and the two copies drift with no alarm.

**[52–58 s] Close**
That's Day 57 of my free 90-day system design book. Link's in the description. Try the problem before you read the answer.

---

On-screen text: `search lags the catalog` · `say the freshness bound` · `checkout rechecks truth`
Description link: https://reasonfrom.com/courses/design-out-loud/day-57/
