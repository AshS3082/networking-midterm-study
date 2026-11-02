# Study Guide — Networking Midterm (Chapters 1–5)

**Course text:** *An Introduction to Computer Networks*, 2nd ed., Peter L. Dordal —
<https://intronetworks.cs.luc.edu/current2/html/>

**Exam:** chapters 1–5. The professor has stated **every exam question will be a textbook
exercise**, so this guide is organized around the **exercise sections**, not the prose order.
A one-page cheat sheet is allowed (see `cheatsheet/cheatsheet.html`).

Source pages used (2nd edition):
- Ch. 1 `intro.html` · Ch. 2 `ethernet1.html` · Ch. 3 `ethernet2.html` · Ch. 4 `wireless.html` · Ch. 5 `otherLANs.html`
- Official solutions: Ch. 34 `solutions.html` (solutions for ch. 1–5 exist for some exercises)

---

## 1. Exercise census — what is actually testable

44 exercises exist across ch. 1–5. No prose topic is worth studying unless it appears here.

| Ch | Title | Exercises | Dominant tested topics |
|----|-------|-----------|------------------------|
| 1 | An Overview of Networks | 1.0–12.0 (12) | **Datagram forwarding / forwarding tables**, default entries, **routing loops**, link weights & shortest path, IPv4 forwarding with subnets |
| 2 | Ethernet Basics | 1.0–10.0 (10) | **CSMA/CD contention & exponential backoff**, **learning switches** (flooding, source learning), loops without STP, VLAN color |
| 3 | Advanced Ethernet | 1.0–8.0 (6) | **Spanning Tree Algorithm**, **VLANs**, **SDN / OpenFlow** flow tables & controller behavior |
| 4 | Wireless LANs | 1.0–7.0 (7) | **Hidden-node problem**, **CSMA/CA contention** (numeric), WiMAX ranging (numeric), **two-ray ground model**, Wi-Fi 4-way handshake |
| 5 | Other LANs | 1.0–9.0 (9) | **VPN / tunneling / NAT**, **Token Ring/Bus** token timing, **ATM** (AAL XOR), **virtual circuits & VCI tables** |

Topic-by-topic index (exercise numbers are the 2nd-edition ones you'll see in the book):

| Topic | Ch. exercise(s) |
|-------|-----------------|
| Forwarding tables (datagram forwarding) | Ch.1 Ex 1.0–7.0, 9.0 |
| Default forwarding entries | Ch.1 Ex 5.0 |
| Routing loops | Ch.1 Ex 3.0, 8.0 |
| Link weights / shortest path | Ch.1 Ex 10.0, 11.0 |
| IPv4 forwarding with subnets (Class C) | Ch.1 Ex 12.0 |
| CSMA/CD + exponential backoff | Ch.2 Ex 1.0 |
| Learning switches / flooding | Ch.2 Ex 2.0–7.0, 10.0 |
| Loops without spanning tree | Ch.2 Ex 8.0 |
| VLAN color | Ch.2 Ex 9.0 · Ch.3 Ex 3.0 |
| Spanning Tree Algorithm | Ch.3 Ex 1.0, 2.0 |
| Distance-vector for Ethernet | Ch.3 Ex 4.0 |
| SDN / OpenFlow / controller | Ch.3 Ex 5.0–8.0 |
| Hidden-node problem | Ch.4 Ex 1.0, 2.0 |
| CSMA/CA contention (numeric) | Ch.4 Ex 3.0 |
| WiMAX/LTE vs Wi-Fi behavior | Ch.4 Ex 4.0 |
| WiMAX ranging / propagation delay | Ch.4 Ex 5.0 |
| Two-ray ground model (power ∝ 1/r⁴) | Ch.4 Ex 6.0 |
| Wi-Fi security / 4-way handshake | Ch.4 Ex 7.0 |
| VPN / tunneling / NAT | Ch.5 Ex 1.0, 2.0 |
| Token Ring / Token Bus | Ch.5 Ex 3.0 |
| ATM / AAL-5 XOR | Ch.5 Ex 4.0 |
| Virtual circuits / VCI tables | Ch.5 Ex 5.0–9.0 |

> **Exercise numbering warning.** The 2nd edition renumbered exercises, but some prose *inside*
> the exercises still cites the **first-edition** numbers. For example, Ch. 3 Ex 6.0 says
> "the arrangement of exercise 12.0" — that refers to what is now **Ch. 3 Ex 5.0**. Likewise
> in-text phrases like "in exercise 7.0" may not match the printed problem number. **Map by
> content, not by the number in the sentence.** The book ships a conversion table at
> `exercise_numbering.html`. All numbers in *this guide* are the current ones.

---

## 2. Chapter 1 — An Overview of Networks

### 2.1 Core model (the backbone of almost every ch. 1 exercise)

A **switch** (or router) forwards an arriving **packet** toward its destination using a
**forwarding table** with rows `⟨destination, next_hop⟩`:

- **next_hop** is the *neighbor* the packet is handed to. In exercises, use **neighbor form**
  (name the neighbor) rather than **interface form** (port number).
- A **directly connected** destination is usually written `direct` or `local`.
- **Flooding** = forward out all ports except the arrival port (used when the destination is unknown).

**Datagram forwarding** is hop-by-hop and *stateless per packet*: each switch only needs
`⟨destination, next_hop⟩`, not the full path.

### 2.2 Forwarding-table construction (Ex 1.0, 2.0, 4.0, 6.0, 9.0)

Algorithm for "give the forwarding tables":

1. Determine the topology (which nodes are directly connected to which switches).
2. For each destination, find the shortest path; the **next hop** is the first switch on that path.
3. For a directly attached node, next_hop = `direct`/`local`.

**Ties.** When two shortest paths exist, either next hop is acceptable — the exercise asks you
to *indicate* the ambiguous destinations (Ex 4.0).

**Example (Ex 1.0).** Topology:

```
A         B         C
│         │         │
S1────────S2────────S3
          │
          S4───D
```

- `A` connects to `S1`; `B` to `S2`; `C` to `S3`; `D` to `S4`; `S4` hangs off `S2`.
- S1: `A→local`, `B→S2`, `C→S2`, `D→S2`
- S2: `A→S1`, `B→local`, `C→S3`, `D→S4`
- S3: `A→S2`, `B→S2`, `C→local`, `D→S2`
- S4: `A→S2`, `B→S2`, `C→S2`, `D→local`

**Example (Ex 4.0).** Five nodes A–E: a square A–C–E–D–A with B attached to A and C.
Destinations are A–E; all routes minimal (1 hop to a neighbor, 2 hops otherwise). Ambiguity
arises when two 2-hop paths are equally good (the book's solution lists e.g. `A→E: D (or C)`).

### 2.3 Default entries (Ex 5.0)

A special `⟨default, next_hop⟩` row catches destinations not listed. Rule: **eliminate any
explicit entry whose next_hop equals the default's next_hop** — those are implied by the
default. This shrinks tables. In Ex 5.0 the default points toward S5.

### 2.4 Routing loops (Ex 3.0, 8.0)

Key facts the exercises want you to prove:

- A forwarding loop can be **two adjacent switches bouncing a packet**, or a **topological cycle**.
- **Linear loops of length > 1 are impossible**: in a path `A→S1→S2→…→Sn→A` (with A distinct
  from the Si), all the Si must be distinct, because each switch would have to be its own ancestor.
- If the *only* path from A to C is through B, a single loop **cannot** contain both A and C
  (consider how B forwards toward the loop's destination).
- Loop prevention in practice: **TTL** (IP) and **split horizon / path vectors**.

### 2.5 Link weights and shortest path (Ex 10.0, 11.0)

Costs add along a path; choose the path of **minimum total weight**. This is the
**shortest-path** problem (Dijkstra / distance-vector). Example from the book: A→E is
`A–S1–S2–S5–E` cost `1+2=3` vs `A–S1–S4–S5–E` cost `5+1=6`.

Ex 11.0 is the inverse problem: **assign weights** so that a given route becomes optimal.
Trick from the hint: give every link weight 1 except one or two "bad" links with weight 10;
the chosen route is then the one avoiding the bad links.

### 2.6 IPv4 forwarding with subnets (Ex 12.0)

Three Class C networks joined by R1–R4, no outside Internet. For each router, the table lists
each network with its next hop; **directly attached networks use `direct`/`local`**.

Example rows (illustrative — recompute for the given topology):
`200.0.1/24 → local`, `200.0.2/24 → <neighbor toward that net>`, plus a default if present.

---

## 3. Chapter 2 — Ethernet Basics

### 3.1 Classic Ethernet, collision detection & exponential backoff (Ex 1.0)

**CSMA/CD.** A station listens; if the medium is idle it transmits. If it detects a collision it
sends a **jam signal** and backs off.

**Exponential backoff:** after the *n*-th collision (n = 1, 2, …), choose `k` uniformly at
random from `[0, 2^n − 1]` slot times and wait `k` slots. Max n = 10 (then stays at
`2^10 − 1 = 1023`). After 16 collisions, give up.

**Key numbers (10-Mbps classic Ethernet):**
- **Slot time = 512 bit times = 51.2 µs** (two-way propagation bound; needed to guarantee
  collision detection).
- **Minimum frame = 64 bytes** (reflects the slot time); **max payload = 1500 bytes**.
- Gigabit Ethernet extends the slot time (carrier extension) so the 64-byte minimum still works.

**Ex 2.0 technique.** Simulate five stations all transmitting at T=0. Track each station's
collision count, draw `k` from the proper range, and recurse. One slot time is needed to
detect a collision, so a station choosing `k=0` retransmits one slot after the collision is
detected.

### 3.2 Ethernet switches and the learning algorithm (Ex 2.0–7.0, 10.0)

Each switch has a forwarding table `⟨MAC address, port⟩`. On every arriving frame:

1. **Learn** the **source** address → record it against the arrival port.
2. Look up the **destination**:
   - known → forward out that one port;
   - unknown → **flood** out all ports except the arrival port;
   - **broadcast** → always flood.

**Method for all learning-switch exercises:** process transmissions **in order**, and after
each one update *every* switch that saw it. A switch "sees" a frame if the frame reaches it
(before the frame is pruned by a known destination). Record what each switch learned.

Worked example **(Ex 3.0)** with A–S1–S2–S3–C and B,D off S2:

| transmission | behavior |
|---|---|
| A→D | all switches flood; all learn **A** |
| D→A | S2 knows A, forwards to S1; S3/S4 don't learn D |
| A→B | all flood again; nothing new |
| B→D | S4 floods (doesn't know D); S2 knows D → forwards only to D; S2,S4 learn **B** |

Final learned sets: S1={A,D}, S2={A,B,D}, S3={A}, S4={A,B} (per official solution 34.2).

### 3.3 Loops without spanning tree (Ex 8.0)

Two switches wired in a loop, no STP: the first **unknown** destination gets flooded endlessly;
the frames **circulate forever** (and the table entries thrash), because a broadcast/unknown
frame has no TTL at layer 2. This is *exactly why* STP exists (ch. 3).

### 3.4 VLANs (Ch.2 Ex 9.0, Ch.3 Ex 3.0)

A **VLAN** partitions a physical switch network into logical LANs. Ports are assigned a
**"color"** (VLAN id). Frames between switches on **trunk** links carry a tag (**802.1Q**).
The exercise asks how switches could track the color of every destination: propagate
`⟨address, color⟩` via a protocol (analogous to learning/BPDU), and when a port changes from
switch-link to host-link, the color must be (re)configured for that port.

---

## 4. Chapter 3 — Advanced Ethernet

### 4.1 Spanning Tree Algorithm (STP) (Ex 1.0, 2.0)

**Goal:** turn a mesh into a loop-free tree. **All switches run the same algorithm** and agree
on a single tree.

1. Elect the **root** = the switch with the **lowest ID**.
2. Each non-root switch chooses the **least-cost path to the root**; ties broken by the
   **lowest-ID neighbor** (path cost is usually number of hops / link speed).
3. On each switch, **prune (block)** every link to the root that wasn't chosen; only the
   chosen root-port and designated ports forward.
4. Switches exchange **BPDUs** to converge; blocked links are re-enabled if the tree breaks.

**Ex 3.0 method.** Walk the given topology: pick the root (lowest ID), give each switch its
root-port, mark the blocked links, then draw the surviving tree.

Worked example **(Ex 2.0, official 34.3)**: root S1 keeps all three links (to S3, S4, S5).
S2 keeps only its link to **S3** (lowest ID among S3/S4/S5). Resulting path S2→S5 = `S2–S3–S1–S5`.

### 4.2 Distance-vector for Ethernet (Ex 4.0)

If switches could identify directly-connected hosts, the **distance-vector** algorithm (ch. 13)
could compute optimal forwarding in the presence of loops. Detection method suggested: let
switches **mark** packets; unmarked ⇒ directly connected. **Broadcast/multicast still breaks**
DV-free forwarding because those frames must flood and would loop.

### 4.3 SDN / OpenFlow (Ex 5.0, 6.0, 7.0, 8.0)

An **OpenFlow switch** has **flow tables** of rules `match → action`. Unknown packets may be
**reported to a controller** C; C installs rules. Typical learning behavior:

- On a packet with **unknown destination**, report to the controller, which **learns the
  source** (records `source → arrival port`) and may install it.
- The switch then **floods** (destination still unknown).
- If the destination is already known, the switch **forwards normally and does not report**.

**Ex 6.0 vs 7.0 differ in the trigger:** report on unknown *destination* only, versus
unknown destination **or source**. Walk the four/six packets in order and track the table on
each switch. **Ex 8.0** asks you to write the reversed flow tables (T0 matches `srcaddr`,
T1 matches `destaddr`) — mirror the given table's rows.

Worked **(Ex 5.0, official 34.3)**: h1→h2 report/learn-h1/flood; h2→h1 no report, forward;
h3→h1 no report, forward; h2→h3 report/learn-h2/flood.

---

## 5. Chapter 4 — Wireless LANs

### 5.1 Hidden-node problem (Ex 1.0, 2.0)

Two senders A and C are both in range of receiver B but **not of each other** — neither hears
the other's carrier, so both transmit and collide *at B*. **Ex 2.0** asks for a
**three-sender** version: place senders on a circle/triangle around a central receiver (or
behind walls). Generalizes to more senders (limited by geometry).

**Ex 4.0 (ACK):** a station may send an **ACK without sensing the medium** (after **SIFS**)
because the ACK is small and goes to a station that just transmitted. With a hidden node B, C's
ACK may still be **decodable** even though B is transmitting — a "collision" only matters if
*some* node cannot read *either* signal.

### 5.2 CSMA/CA contention timing (Ex 3.0)

**CSMA/CA** (Wi-Fi) senses the carrier and uses **random backoff** with a contention window,
**DIFS**, **SIFS** (SIFS < DIFS), and optional **RTS/CTS** to mitigate hidden nodes.

**Numeric (Ex 3.0).** avg contention 64 slot times; packet 1 kB at 54 Mbps ⇒ ~151 µs.
- (a) Slot time for 802.11g = **9 µs** → contention ≈ `64 × 9 = 576 µs`.
- (b) transmission and contention alternate `…151–T–151–T…`, so the fraction of bandwidth lost
  to contention is `T / (T + 151)`. With T≈576 µs, that's ≈ 576/727 ≈ **79 %**.

### 5.3 WiMAX / LTE versus Wi-Fi (Ex 4.0)

WiMAX/LTE are **scheduled** (base station allocates slots); subscriber stations need not hear
each other. Wi-Fi stations *do* hear neighbors in an infrastructure BSS and can react (defer,
set NAV, choose rate, etc.). The exercise wants examples of such reactions, same-AP and
different-AP.

### 5.4 WiMAX ranging / propagation delay (Ex 5.0)

Subscribers up to 33 m/s; ranging updates propagation delay once/minute; c ≈ 300 m/µs.
- (a) One packet can arrive early/late by the distance the station moved in the update
  interval: `33 m/s × 60 s = 1980 m` ⇒ `1980 / 300 ≈ 6.6 µs`.
- (b) Guard interval per sender = 2× (early *or* late): `≈ 13.2 µs`; for 5000 senders/s,
  `5000 × 13.2 µs ≈ 66 ms` per second ≈ **6.6 %** of the time spent on guard intervals.

### 5.5 Two-ray ground model (Ex 6.0)

Line-of-sight plus a ground reflection. Result: amplitude of the difference ∝ `1/r²`, so
**received power ∝ 1/r⁴** (not 1/r²). Steps: (a) reflected path length
`r′ = √(r² + (2h)²) ≈ r(1 + 2h²/r²)` for `r ≫ h`; (b) subtract the 180°-shifted signals
using `sin X − sin Y ≈ (X−Y) cos X`; (c) the `(X−Y)` factor introduces another `1/r`, giving
`1/r²` amplitude and `1/r⁴` power.

### 5.6 Wi-Fi security / 4-way handshake (Ex 7.0)

WPA2's 4-way handshake derives fresh session keys from a nonce. A **replay attack** by a
station B that recorded A's handshake breaks down at the point where the **AP sends a fresh
nonce (ANonce)** — the replayed messages contain A's **old nonce**, so B cannot produce the
matching **MIC** for the *new* exchange. (i.e. the handshake diverges on the nonce/MIC.)

---

## 6. Chapter 5 — Other LANs

### 6.1 VPN / tunneling / NAT (Ex 1.0, 2.0)

A **VPN** creates virtual links over the Internet: the packet destined for the private address
is **encapsulated** inside a normal Internet packet to the VPN gateway. Devices `eth0`
(public, e.g. 12.1.2.3) and `ppp0` (tunnel, e.g. 10.0.0.44).

**Ex 5.0 routing:** with the table "200.0.0.7 via eth0, everything else via ppp0", traffic to
the VPN server goes direct, but an intruder M at 12.1.2.3 is reached via `ppp0` (inside the
tunnel) — so replies are forced through the tunnel. If **all** traffic uses `ppp0`, the
encapsulated packets themselves (which must go to 200.0.0.7 over `eth0`) also go into `ppp0`
→ a **routing loop / tunnel breakdown**; the tunnel cannot carry its own transport packets.

**Ex 2.0:** A running the VPN on VM V with NAT — A can route VPN-bound traffic to V, but it
must **exclude the tunnel's own endpoint (200.0.0.7) and V's traffic** from the tunnel, otherwise
the encapsulated-over-VPN packets get re-encapsulated (recursion). Achievable by targeted
routing entries.

### 6.2 Token Ring / Token Bus (Ex 3.0)

A **token** circulates in a fixed order; only the token holder may transmit (**802.5**). On an
idle network with N stations, the average number of stations the token must pass is `N/2`.

**Numeric (Ex 3.0a).** 10 Mbps, token = 64 bytes = 512 bits = 51.2 µs, 40 stations → avg
`20` tokens → `20 × 51.2 µs ≈ 1.024 ms`.

**Ex 3.0b (ordering):** stations can sort themselves by numeric **MAC address** (smallest =
S0, next = S1, …), giving a deterministic ring order (official solution 34.5).

### 6.3 ATM / AAL-5 (Ex 4.0)

**ATM** uses fixed **53-byte cells** (5-byte header + **48-byte payload**). **AAL-5** segments
a packet into N cells. The **SM90** idea adds one cell = **XOR of all N cells**, letting the
receiver recover from any single lost cell.

**Numeric (Ex 4.0).** With the XOR cell, cost = 21 cells per packet (5 % overhead), **no**
retransmission. Without it, loss prob ≈ `20p` per packet and you resend 20 cells → extra
`20p × 20 = 400p` cells. Set equal: `1 = 400p` ⇒ **p ≈ 0.0025 (0.25 %)**. (Follow the book's
own cost model exactly as stated in the exercise.)

### 6.4 Virtual circuits & VCI tables (Ex 5.0–9.0)

Connection-oriented: a path is set up and each switch holds a **VCI table**:

| VCI_in | port_in | VCI_out | port_out |
|--------|---------|---------|----------|

- **port_in/port_out** are identified by the **node at the other end**.
- **VCI** is a small label; the same VCI can be reused on different links.
- To trace a connection: start at the ingress entry, follow `VCI_out` on `port_out` to the next
  switch, find the entry there with matching `VCI_in`/`port_in`, repeat until the endpoint.

**Ex 7.0 (official 34.5):** connections found from the given tables:
`A–1–S1–2–S2–3–S4–2–D` and `B–1–S2–2–S4–1–S3–3–S1–2–A` (VCIs between endpoints).

**Ex 9.0:** build a VCI table for a connection that leaves and **revisits the same switch
twice** — each visit is a separate row, distinguished by the incoming `port_in`/`VCI_in`.

---

## 7. How to study (suggested, ~1 day)

1. Read §2–§6 of this guide once, marking exercises you can't do unaided.
2. Do the **sample problems** (`problems/sample-problems.md`) with hints only.
3. Check against **worked examples** (`problems/worked-examples.md`) and the book's Ch. 34.
4. Drill the **flashcards** (`flashcards/flashcards.md` / `flashcards.csv`).
5. Watch the **animations** (`animations/index.html`) for CSMA/CD, learning switches, STP,
   and forwarding.
6. Compress everything onto the **one-page cheat sheet** (`cheatsheet/cheatsheet.html`).
7. **Quiz:** produced without LLM assistance — see `quiz/`.
