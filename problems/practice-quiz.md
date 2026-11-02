# Practice Quiz — Networking Midterm (Ch. 1–5)

> **This is a practice quiz for self-testing — it is NOT the graded quiz.** The graded quiz is
> given in the lab at 3:50pm and must be answered **without LLM assistance**; keep that one in
> `quiz/`. Answers to this practice quiz are at the bottom.

**Time:** ~15 minutes · **12 questions** · do it closed-book, then check.

---

## A. Datagram forwarding (Ch. 1)

**1.** In *neighbor form*, what exactly does the `next_hop` column of a forwarding table contain?

**2.** You add a `default` entry pointing to S3. Which existing rows can you now delete?

**3.** True or false, and justify: a forwarding loop can consist of five distinct switches wired
in a line that pass a packet back to the first switch but never form a cycle.

**4.** Two paths A→E: `A–S1–S2–S5–E` with link costs 1, 2 and `A–S1–S4–S5–E` with costs 5, 1.
Which path does a lowest-cost routing protocol choose, and what is its cost?

---

## B. Ethernet basics (Ch. 2)

**5.** After the **3rd** collision, from what set of values is the backoff `k` chosen, in what units?

**6.** Give the classic 10-Mbps Ethernet **slot time** and the **minimum frame size**, and say why
the minimum frame exists.

**7.** List the two steps a learning switch performs on every arriving frame.

---

## C. Advanced Ethernet (Ch. 3)

**8.** In the Spanning Tree Algorithm, how is the **root** switch chosen, and how does a non-root
switch break a tie between two equally-short paths to the root?

**9.** In OpenFlow, when a switch sees a packet whose **destination** is unknown, what two things
happen (controller side and switch side)?

---

## D. Wireless LANs (Ch. 4)

**10.** Define the **hidden-node problem** in one sentence.

**11.** In the two-ray ground model, received signal power is proportional to what function of
distance `r`?

---

## E. Other LANs (Ch. 5)

**12.** (a) How big is an ATM cell, and how is it split? (b) List the four columns of a VCI table.

---
---

## Answer key

**1.** The **neighbor node/switch** the packet is handed to next — *not* the port/interface number.

**2.** Every explicit row whose `next_hop` is **also S3** (those are implied by the default).

**3.** **False.** Forwarding is a function: each switch has exactly one next hop for a destination.
If the packet returns to its start, any repeated switch traps it in a shorter cycle — so a linear
loop of length > 1 is impossible; loops are either a two-switch bounce or a graph cycle.

**4.** The first path: cost `1+2 = 3` (vs `5+1 = 6`).

**5.** `k ∈ {0,1,2,3,4,5,6,7}` — i.e. `[0, 2³−1]` — measured in **slot times**. (n caps at 10.)

**6.** Slot time = **512 bit-times = 51.2 µs**; minimum frame = **64 bytes**. The minimum frame
guarantees a sender is still transmitting when a collision returns, so it can detect the collision
within the slot time.

**7.** (1) **Learn** the **source** address → record its arrival port. (2) Look up the
**destination**: forward out the one known port, or **flood** if unknown (broadcast always floods).

**8.** Root = the switch with the **lowest ID**. Ties in path cost are broken by the **lowest-ID
neighbor**.

**9.** The switch **reports the packet to the controller**; the controller **learns the source**
and installs a rule `source → arrival port`. The switch then **floods** the packet (destination
still unknown).

**10.** Two senders are each in range of a common receiver but **out of range of each other**, so
neither senses the other's carrier and their simultaneous transmissions **collide at the receiver**.

**11.** Power ∝ **1/r⁴** (inverse fourth power).

**12.** (a) **53 bytes** = **5-byte header + 48-byte payload**. (b) `VCI_in`, `port_in`,
`VCI_out`, `port_out`.

---

### Scoring
- **10–12 correct:** exam-ready on breadth — drill weak chapters only.
- **7–9:** solid; review the missed topics in `study-guide/study-guide.md`.
- **≤6:** re-read the study guide, then redo `problems/sample-problems.md` before retrying.
