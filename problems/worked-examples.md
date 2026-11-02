# Worked Examples — Networking Midterm (Ch. 1–5)

Full solutions to `sample-problems.md`. Where a problem mirrors a real textbook exercise,
the book's official solution (Ch. 34) is cited so you can cross-check.

---

## Set A — Datagram forwarding

### A1 — Line topology

Topology: `A─S1─S2─S3─S4─D`, `B` on S2, `C` on S3.

| Switch | A | B | C | D |
|--------|---|---|---|---|
| S1 | local | S2 | S2 | S2 |
| S2 | S1 | local | S3 | S3 |
| S3 | S2 | S2 | local | S4 |
| S4 | S3 | S3 | S3 | local |

Every destination is reached by heading along the line toward its attachment point.

### A2 — Default entry for S2

S2 base table: `A→S1`, `B→local`, `C→S3`, `D→S3`.
Add `default → S3`. Delete explicit rows whose next_hop is S3: **C** and **D** are implied.

Result: `A→S1`, `B→local`, `default→S3`.

### A3 — Loop structure proof

Let `f(x)` be the next hop a switch uses for destination `D`. Forwarding follows
`A, f(A), f(f(A)), …`. Suppose a loop visits `A──S1──S2──…──Sn──A` with no `Si = A`.

Assume for contradiction some `Si = Sj`, `i < j`. Because `f` is a **function** (a switch has a
single next hop for a given destination), from `Si` the packet repeats the exact sequence
`Si → Si+1 → … → Sj = Si` forever — it can never reach `A`. But the loop does return to `A`.
Contradiction ⇒ all `Si` are distinct.

Consequence: a datagram loop is either a **two-switch bounce** (`S→T→S`) or a genuine
**topological cycle**; a "linear" chain that returns to its start with distinct intermediates is
possible only when the chain closes into a graph cycle.

### A4 — Weighted path

- Path 1: `A–S1–S2–S5–E` = 1 + 2 = **3**
- Path 2: `A–S1–S4–S5–E` = 5 + 1 = **6**

Lowest-cost path: **A–S1–S2–S5–E (cost 3)**. (Compare textbook official solution to 1.9/10.0(a).)

### A5 — Router forwarding table

R4 attaches to R1, R2, R3; each R*n* attaches one network. R4 has no directly attached network:

| Destination | next_hop |
|---|---|
| 200.0.1/24 | R1 |
| 200.0.2/24 | R2 |
| 200.0.3/24 | R3 |

---

## Set B — Ethernet

### B1 — Exponential-backoff simulation (5 stations, all start at T=0)

Convention: a collision at time `t` is detected at `t+1`; a station in collision round `n`
picks `k ∈ [0, 2ⁿ−1]` and attempts again at `t+1+k`.

| Time | Event | Note |
|---|---|---|
| T=0 | S1–S5 all transmit | collision; detected at T=1; all enter round 1 |
| T=1 | **S1 transmits alone → success** | others chose k=1 |
| T=2 | S2,S3,S4,S5 transmit together | collision; detected T=3; round 2 |
| T=3 | **S2 alone → success** | S3 chose k=1, S4 k=2, S5 k=3 |
| T=4 | **S3 alone → success** | |
| T=5 | **S4 alone → success** | |
| T=6 | **S5 alone → success** | all finished by T=6 |

Run detail: round 1 choices were `S1=0, S2=S3=S4=S5=1`; round 2 choices were
`S2=0, S3=1, S4=2, S5=3`. The actual result depends on the random coin flips
(use `animations/csma-cd.html` to replay).

### B2 — Learning switches (book Ex. 2.2/3.0, official sol. 34.2)

Process each transmission; every switch that sees the frame learns its **source**.

| Transmission | What happens |
|---|---|
| A→D | No switch knows D → all flood. **Every switch learns A.** |
| D→A | S2 knows A → forwards to S1 only. S1 knows A → delivers. S3,S4 never see it. S1,S2 learn D. |
| A→B | All flood again (B unknown); nothing new learned (A already known). |
| B→D | S4 doesn't know D → floods; S2 knows D → forwards only to D. S2,S4 learn B. |

Final learned sets:

| Switch | knows |
|---|---|
| S1 | A, D |
| S2 | A, B, D |
| S3 | A |
| S4 | A, B |

### B3 — Loop without spanning tree

The first unknown-destination (or broadcast) frame is flooded by both switches, each forwards it
to the other, and — because **Ethernet frames have no TTL** — the copies **circulate forever**,
each direction re-flooding the other. The frame never dies; the network saturates (a
broadcast storm). This is why STP exists.

### B4 — Spanning Tree prune

Root = S1 (lowest ID). Root paths:

- Level 1: **S3, S4** (direct neighbors of S1).
- S5: reachable in 2 hops via S3 **or** S4 → tie; pick the **lower-ID neighbor S3** → root port **S3–S5**; prune **S4–S5**.
- S6: reachable in 2 hops via **S4** (via S2 would be longer) → root port **S4–S6**.
- S2: reachable in 3 hops via S5 **or** S6 → tie; pick lower-ID neighbor **S5** → root port **S5–S2**; prune **S6–S2**.

Surviving tree: `S1–S3–S5–S2` and `S1–S4–S6`.
**Pruned links: S4–S5 and S6–S2.**

### B5 — OpenFlow single switch (book Ex. 3.4/5.0, official sol. 34.3)

Rule: report only on **unknown destination**; controller learns the **source**.

| Packet | Reported? | Rule installed | Outcome |
|---|---|---|---|
| h1→h2 | yes (h2 unknown) | h1 → port 1 | flood |
| h2→h1 | no (h1 known) | — | forward to h1 |
| h3→h1 | no (h1 known) | — | forward to h1 |
| h2→h3 | yes (h3 unknown) | h2 → port 2 | flood |

---

## Set C — Wireless

### C1 — Three-sender hidden node (book Ex. 4.1/2.0, official sol. 34.4)

Place senders A, B, C on the vertices of an **equilateral triangle** with receiver D at the
center: each pair of senders is out of range of each other, but all reach D, so their
simultaneous transmissions collide at D. (Alternatively, use impermeable walls to create the
same non-overlapping coverage.) It generalizes to more senders very naturally with the
wall arrangement; with the geometric arrangement you are limited to square/tetrahedron/pentagon.

### C2 — CSMA/CA contention (book Ex. 4.2/3.0, official sol. 34.4)

(a) 802.11g slot time = 9 µs, so the average contention interval is
`64 × 9 = 576 µs`.

(b) Transmission and contention alternate `…151–T–151–T…`, so the fraction of bandwidth lost is
`T / (T + 151) = 576 / 727 ≈ 0.79`.

**About 79 % of the potential data rate is lost to contention.**

### C3 — WiMAX ranging (book Ex. 4.4/5.0)

(a) In 60 s a station moving at 33 m/s covers `33 × 60 = 1980 m`, i.e.
`1980 / 300 ≈ 6.6 µs` of propagation delay. A packet can thus arrive about **6.6 µs early or late**.

(b) Guard time per sender = `2 × 6.6 = 13.2 µs` (early *or* late). With 5000 senders/s:
`5000 × 13.2 µs = 66 000 µs = 66 ms`, i.e. **≈ 6.6 % of each second** spent on guard intervals.

### C4 — Two-ray ground model (book Ex. 4.5/6.0)

(a) With equal heights `h` and horizontal distance `r`, the reflected path is
`r′ = √(r² + (2h)²) = r·√(1 + (2h/r)²) ≈ r(1 + 2h²/r²)` for `r ≫ h`, so
`r′ − r ≈ 2h²/r`.

(b) The received signal is line-of-sight minus the 180°-shifted reflection:
`(A/r)sin(2πft − 2πr/λ) − (A/r′)sin(2πft − 2πr′/λ)`.
Using `sin X − sin Y ≈ (X−Y)·cos X` (with `X ≈ Y`), the difference is approximately
`(A/r − A/r′)·sin(2πft − 2πr/λ) + (A/r′)(2π(r′−r)/λ)cos(2πft − 2πr/λ)`.

(c) Split the difference into two parts:

```
D = (A/r − A/r′)·sin(2πft − 2πr/λ)              [amplitude part]
  + (A/r′)·[sin(2πft − 2πr/λ) − sin(2πft − 2πr′/λ)]   [phase part]
```

- **Amplitude part:** `A/r − A/r′ = A(r′−r)/(r r′) ≈ A(2h²/r)/r² = 2Ah²/r³` → ∝ 1/r³.
- **Phase part:** using `sin X − sin Y ≈ (X−Y)cos X` with
  `X−Y = 2π(r′−r)/λ ≈ 4πh²/(λ r)`, this term is `(A/r′)·(4πh²/(λ r))·cos(…)`, and since
  `r′ ≈ r`, its amplitude ∝ `(1/r)(1/r) = 1/r²`.

The **phase part dominates** (1/r² beats 1/r³), so the received difference amplitude ∝ **1/r²**.
Power ∝ amplitude², hence received **power ∝ 1/r⁴**.

### C5 — Wi-Fi 4-way handshake replay (book Ex. 4.6/7.0)

The handshake derives session keys from a **fresh nonce** the AP sends. When B replays A's
recorded messages, it supplies A's **old nonce**, not the AP's **new** one; consequently B cannot
compute the correct **MIC** for the new exchange, and the **AP rejects** the attempt. The
handshake breaks down at the point where the nonce (ANonce) is required to be fresh.

---

## Set D — Other LANs

### D1 — VPN routing trap (book Ex. 5.0)

With "200.0.0.7 via eth0, everything else via ppp0", the intruder M at 12.1.2.3 falls under
"everything else". A's packets **to M are routed into the tunnel (`ppp0`)** and encapsulated
toward B, rather than going out `eth0`. So A cannot carry on a normal local exchange with M:
its replies are forced through the VPN. (If instead *all* traffic used `ppp0`, the tunnel's own
packets to 200.0.0.7 would also be tunnelled → recursion / the tunnel collapses.)

### D2 — Token ring timing (book Ex. 5.2/3.0)

Token = 64 bytes = 512 bits; at 10 Mbps it takes `512 / 10 000 000 = 51.2 µs` to transmit.
Average number of token passes on N=40 idle stations = `40/2 = 20`, so the average wait is
`20 × 51.2 µs ≈ 1024 µs = 1.024 ms`.

### D3 — ATM / SM90 XOR (book Ex. 5.3/4.0)

- **With** the XOR cell: always **21 cells** per packet, no retransmission.
- **Without**: a packet is lost with probability ≈ `20p`; each loss resends 20 cells, so the
  expected extra is `20p × 20 = 400p` cells, total `20 + 400p`.

Set `21 = 20 + 400p` ⇒ `p = 1/400 = 0.0025`.

**p ≈ 0.25 %** is the loss rate where both schemes cost about the same.

### D4 — Tracing a virtual circuit (book Ex. 5.7/7.0, official sol. 34.5)

S1 entry `VCI_in = 3, port_in = S3, VCI_out = 2, port_out = A` means the packet arrived **from
S3** with label 3 and leaves toward **A** with label 2. This is the tail of the connection:

```
B ──1── S2 ──2── S4 ──1── S3 ──3── S1 ──2── A
```

(VCIs shown between endpoints.) So the entry with `VCI_in = 3` at S1 belongs to the
**B→A** connection.

### D5 — VCI table, each switch visited twice (book Ex. 5.8/9.0)

Path: `A → S1 → S2 → S4 → S3 → S1 → S2 → S4 → S3 → B`. Assign a distinct VCI per link:

| Link | VCI | | Link | VCI |
|---|---|---|---|---|
| A→S1 | 1 | | S3→S1 | 5 |
| S1→S2 | 2 | | S1→S2 | 6 |
| S2→S4 | 3 | | S2→S4 | 7 |
| S4→S3 | 4 | | S4→S3 | 8 |
| | | | S3→B | 9 |

Resulting tables (each switch appears **twice**, once per visit):

**S1**

| VCI_in | port_in | VCI_out | port_out |
|---|---|---|---|
| 1 | A | 2 | S2 |
| 5 | S3 | 6 | S2 |

**S2**

| VCI_in | port_in | VCI_out | port_out |
|---|---|---|---|
| 2 | S1 | 3 | S4 |
| 6 | S1 | 7 | S4 |

**S4**

| VCI_in | port_in | VCI_out | port_out |
|---|---|---|---|
| 3 | S2 | 4 | S3 |
| 7 | S2 | 8 | S3 |

**S3**

| VCI_in | port_in | VCI_out | port_out |
|---|---|---|---|
| 4 | S4 | 5 | S1 |
| 8 | S4 | 9 | B |

Follow the rule: at each switch, use `VCI_out`/`port_out` to reach the next switch, then match
its `VCI_in`/`port_in`. The VCIs are **per-link** labels, so reusing small numbers is fine.
