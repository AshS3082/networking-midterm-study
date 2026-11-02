# Sample Problems — Networking Midterm (Ch. 1–5)

Exercise-style questions with **hints only**. Full solutions are in `worked-examples.md`.
These are modeled on the real textbook exercises (numbers reference `study-guide/study-guide.md`).

---

## Set A — Datagram forwarding (Ch. 1)

**A1.** Switches S1–S4 are connected in a line: `A─S1─S2─S3─S4─D`, with `B` attached to S2 and
`C` attached to S3. Give the forwarding table of **each switch** for destinations A, B, C, D,
in neighbor form.

> *Hint:* next hop = first switch on the shortest path to the destination. Directly attached
> hosts are `local`.

**A2.** For A1, rewrite **S2's** table adding a `default` entry pointing toward S3, then remove
every entry implied by the default.

> *Hint:* remove rows whose next_hop equals the default's next_hop.

**A3.** Prove: in a datagram network, any forwarding loop created must either bounce between two
**adjacent** switches or follow a **topological cycle**. (Linear loops of length > 1 cannot occur.)

> *Hint:* on the loop `S1→S2→…→Sn→S1`, suppose some switch repeats; derive a contradiction.

**A4.** Network `A─S1─(cost 1)─S2─(cost 2)─S5─E` and `A─S1─(cost 5)─S4─(cost 1)─S5`. For the
transmission A→E find the **lowest-cost path**.

> *Hint:* add the link weights of each candidate path.

**A5.** Three Class C networks 200.0.1/24, 200.0.2/24, 200.0.3/24 are joined by routers
R1–R4. R1 attaches 200.0.1/24, R2 attaches 200.0.2/24, R3 attaches 200.0.3/24, and R4 joins
R1, R2, R3. Give R4's forwarding table.

> *Hint:* each network row points at the neighbor router toward it; directly attached networks
> are `local`.

---

## Set B — Ethernet (Ch. 2–3)

**B1.** Five Ethernet stations all attempt to transmit at T=0. Simulate the contention period
in slot times until all have succeeded, using exponential backoff. Show each station's backoff
choice per collision round.

> *Hint:* after collision round n, each involved station waits k ∈ [0, 2^n − 1] slots. One slot
> time to detect a collision.

**B2.** Topology `A─S1─S2─S3─C`, with `D` attached to S2 and `S4` attached to S2, and `B`
attached to S4. All tables empty.
Transmissions: A→D, D→A, A→B, B→D. For each switch list the source addresses it learns.

> *Hint:* update every switch that sees each frame, in order; a known destination prevents
> downstream switches from seeing the frame.

**B3.** Two switches S1, S2 form a loop; no STP. A sends to unknown destination B. What happens?

> *Hint:* layer 2 has no TTL.

**B4.** In the 3×2 switch grid below, which links does STP prune? Root is `S1` (ID = 1).
Edges: `S1–S4`, `S4–S6`, `S1–S3`, `S4–S5`, `S3–S5`, `S5–S2`, `S6–S2`.

```
S1──────S4──────S6
│       │       │
S3──────S5──────S2
```

> *Hint:* each non-root switch keeps only its lowest-cost, lowest-ID-neighbor path to the root.

**B5.** One OpenFlow switch S (ports 1,2,3) with controller C; tables empty. S reports packets
only on **unknown destination**; C learns the source and installs a rule. Packets: h1→h2,
h2→h1, h3→h1, h2→h3. For each packet state: reported? rule installed? flood or forward?

> *Hint:* after C learns h1 and h2, later packets to those destinations need no report.

---

## Set C — Wireless (Ch. 4)

**C1.** Give a three-sender hidden-node scenario. Can you extend it to four senders?

> *Hint:* put the senders on a circle around a central receiver; or use walls.

**C2.** Wi-Fi (802.11g): average contention interval 64 slot times; packet 1 kB at 54 Mbps
(≈151 µs). (a) average contention interval in µs? (b) fraction of bandwidth lost to contention?

> *Hint:* 802.11g slot time = 9 µs; loss = T_cont / (T_cont + T_tx).

**C3.** WiMAX subscriber moves up to 33 m/s; ranging updates propagation delay every 60 s;
c ≈ 300 m/µs. (a) how early/late can a packet arrive? (b) with 5000 senders/s, what fraction
of each second is guard time (double part (a))?

> *Hint:* distance in 60 s = 33 × 60 m; time = distance / 300 µs per m.

**C4.** Two-ray ground model: show received power ∝ 1/r⁴.

> *Hint:* reflected path r′ ≈ r(1 + 2h²/r²); subtract the 180°-shifted signals; the (r′−r)
> factor adds a 1/r term.

**C5.** A station B records A's successful WPA2 4-way handshake, waits for A to leave, and
replays A's messages. At what point does the attack fail?

> *Hint:* think about the nonce and the MIC.

---

## Set D — Other LANs (Ch. 5)

**D1.** Host A reaches VPN gateway B (200.0.0.7) via `eth0` (12.1.2.3); tunnel device `ppp0`
is 10.0.0.44. A's table: "200.0.0.7 via eth0, everything else via ppp0". An intruder M at
12.1.2.3 connects to A. What route do A's packets to M take, and why is this a problem?

> *Hint:* M is "everything else", so it goes into the tunnel.

**D2.** Token ring, 10 Mbps, 40 stations, token = 64 bytes. Average wait for the token on an
idle network (ignore propagation/gaps)?

> *Hint:* average passes = N/2; token transmit time = 512 bits / 10 Mbps.

**D3.** ATM AAL-5 with the SM90 XOR cell: N = 20. Find the cell-loss rate p at which the
with-XOR and without-XOR schemes need about the same number of cell transmissions.

> *Hint:* with XOR: 21 cells, no retransmit. Without: ≈20p loss probability, resend 20 cells.

**D4.** For the network `A─S1─S2─B` / `C─S3─S4─D` (S1–S3, S2–S4 vertical), suppose S1's table
has entries `1:A→2:S2` and `3:S3→2:A`. Trace the connection that uses the entry with
`VCI_in = 3`.

> *Hint:* follow VCI_out / port_out to the next switch, match VCI_in there, repeat.

**D5.** Define VCI-table entries so a virtual circuit from A to B follows a path that visits
each of S1–S4 **twice**.

> *Hint:* each visit is a separate row, distinguished by the incoming port/VCI.
