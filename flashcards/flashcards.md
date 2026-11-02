# Flashcards — Networking Midterm (Ch. 1–5)

Two forms:
- **This file** — human-readable, grouped by chapter.
- **`flashcards.csv`** — import into Anki (*File → Import*, comma-separated, fields: *Front, Back, Tags*).

---

## Ch. 1 — Datagram forwarding & routing

**Q:** What structure does a switch/router consult to forward a packet, and what two fields does each row have?  
**A:** A **forwarding table**; each row is `⟨destination, next_hop⟩` (optionally with a `default` row).

**Q:** In these exercises, what is "neighbor form" vs "interface form" for next_hop?  
**A:** Neighbor form names the **neighbor switch/node**; interface form gives the **port number**. The exercises ask for neighbor form.

**Q:** How is a directly attached destination written in a forwarding table?  
**A:** `direct` or `local`.

**Q:** What is flooding?  
**A:** Forwarding a frame out of **all ports except the arrival port**; used when the destination is unknown or for broadcast.

**Q:** What does a `default` forwarding entry do, and which explicit entries can you delete when you add it?  
**A:** It catches all unlisted destinations. Delete any explicit entry whose **next_hop equals the default's next_hop** (those are implied).

**Q:** Two forms a forwarding loop can take?  
**A:** (1) two **adjacent switches** bouncing a packet; (2) an actual **topological cycle**. **Linear loops of length > 1 are impossible.**

**Q:** How is IP forwarding loop prevention done in practice?  
**A:** **TTL** (decrement per hop, drop at 0); routing side uses split horizon / path vectors.

**Q:** How do you choose a path when links have weights?  
**A:** Minimum **total weight** (shortest-path problem).

**Q:** Inverse weight problem ("make this route optimal") trick?  
**A:** Give all links weight 1 except one/two "bad" links with weight 10; the target route is the one avoiding the bad links.

---

## Ch. 2 — Ethernet basics

**Q:** What does CSMA/CD stand for / do?  
**A:** **Carrier Sense Multiple Access with Collision Detection** — listen before sending; on collision, jam and back off.

**Q:** Exponential backoff: after the *n*-th collision, from what range is the backoff `k` chosen?  
**A:** `k` uniform in `[0, 2^n − 1]` **slot times**. (n caps at 10; give up after 16 collisions.)

**Q:** Slot time on 10-Mbps classic Ethernet?  
**A:** **512 bit times = 51.2 µs**.

**Q:** Minimum Ethernet frame size, and why?  
**A:** **64 bytes** — it guarantees collisions are detected within the slot time.

**Q:** Standard Ethernet max payload?  
**A:** **1500 bytes** (max frame 1518 bytes).

**Q:** Learning-switch algorithm — the two steps on each arriving frame?  
**A:** (1) **Learn** the **source** address → arrival port. (2) Look up the **destination**: known ⇒ single-port forward; unknown ⇒ flood; broadcast ⇒ flood.

**Q:** Why do two switches in a loop (no STP) cause a problem?  
**A:** An unknown/broadcast frame is flooded forever — layer 2 has **no TTL** — so it **circulates endlessly** and tables thrash.

**Q:** How does a VLAN distinguish traffic, and what tag does it use?  
**A:** By a **"color" / VLAN id**; trunk frames carry an **802.1Q** tag.

---

## Ch. 3 — Advanced Ethernet

**Q:** Spanning Tree Algorithm — how is the root chosen?  
**A:** The switch with the **lowest ID**.

**Q:** After the root, what does each switch do?  
**A:** Chooses the **least-cost path to the root** (tie-break by **lowest-ID neighbor**) and **blocks** its other root-ward links.

**Q:** What messages do switches exchange to build/repair the tree?  
**A:** **BPDUs** (Bridge Protocol Data Units).

**Q:** Purpose of STP?  
**A:** Turn a physical mesh into a **loop-free logical tree**.

**Q:** In OpenFlow/SDN, what is a flow table?  
**A:** A set of rules `match → action` installed on the switch (some by a controller).

**Q:** OpenFlow learning behavior when the destination is unknown?  
**A:** Report to the **controller**; controller learns the **source**; switch **floods**. If the destination is known, forward normally and do **not** report.

**Q:** What does the controller install when it learns a source address?  
**A:** A forwarding entry `⟨source address → arrival port⟩`.

---

## Ch. 4 — Wireless LANs

**Q:** Hidden-node problem?  
**A:** Two senders in range of a common receiver but **not of each other** transmit simultaneously and collide *at the receiver*.

**Q:** Wi-Fi medium-access method (not CSMA/CD)?  
**A:** **CSMA/CA** — collision **avoidance** (no collision detection over radio).

**Q:** In Wi-Fi, is SIFS shorter or longer than DIFS, and what uses SIFS?  
**A:** **SIFS is shorter**; it's used for ACKs and other immediate responses.

**Q:** Why can an ACK be sent without sensing the medium?  
**A:** It's small and follows a just-finished transmission; a hidden node transmitting may still leave the ACK decodable, since a "collision" only matters if a node can read **neither** signal.

**Q:** 802.11g slot time?  
**A:** **9 µs**.

**Q:** Fraction of bandwidth lost to contention when transmission T_tx and contention T_c alternate?  
**A:** `T_c / (T_c + T_tx)`.

**Q:** WiMAX/LTE vs Wi-Fi on sharing the medium?  
**A:** WiMAX/LTE are **scheduled** (base station allocates); Wi-Fi stations **listen and defer**.

**Q:** Two-ray ground model: received power ∝ ?  
**A:** `1 / r⁴` (fourth power), not 1/r².

**Q:** Where does a recorded Wi-Fi 4-way-handshake replay attack break down?  
**A:** When the AP sends a **fresh nonce** — the replay carries the victim's **old nonce**, so the attacker can't produce the matching **MIC**.

---

## Ch. 5 — Other LANs

**Q:** What is a VPN, in one line?  
**A:** Virtual private network — **encapsulates** private-addressed packets inside normal Internet packets sent to a VPN gateway.

**Q:** Why does "route *all* traffic over the tunnel" break the tunnel itself?  
**A:** The tunnel's **own encapsulated packets** must reach the gateway (e.g. 200.0.0.7) over the real interface; sending those into the tunnel too **recurses / loops**.

**Q:** What must a split-tunnel route exclude?  
**A:** Traffic to the **VPN gateway address** and the local/VM traffic used to carry the tunnel.

**Q:** Token Ring: who may transmit?  
**A:** Only the station currently holding the **token**.

**Q:** Average number of stations a token passes on an idle N-station ring?  
**A:** `N / 2`.

**Q:** How can stations set up a token order?  
**A:** Sort by numeric **MAC address** (smallest → S0, next → S1, …).

**Q:** ATM cell size?  
**A:** **53 bytes** = 5-byte header + **48-byte payload**.

**Q:** AAL-5 / SM90 error-recovery idea?  
**A:** Send N cells plus **one extra cell = XOR of the N cells**, so any single lost cell is recoverable.

**Q:** VCI table columns?  
**A:** `VCI_in | port_in | VCI_out | port_out`.

**Q:** In a VCI table, what identifies a port?  
**A:** The **node at the other end** of the link.

**Q:** How do you trace a virtual circuit through VCI tables?  
**A:** Follow `VCI_out` on `port_out` to the next switch, match its `VCI_in`/`port_in`, repeat to the endpoint.

**Q:** Can the same VCI value appear on different links?  
**A:** Yes — VCIs are **per-link** labels.
