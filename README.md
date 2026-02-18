# Finding the Perfect Spot: WiFi Analytics for Premium Space Advertising

> A graph-inspired approach to identifying high-value advertising locations using anonymous foot traffic data. Footfall alone is not value — this analysis captures reach, dwell time, throughflow, and repeat exposure to score every zone in a venue.

---

## Executive Summary

Venue operators and media buyers traditionally rely on foot traffic counts to price advertising space. This analysis challenges that assumption using a **graph-inspired scoring model** applied to anonymized WiFi session data.

By modeling the venue as a network of connected zones, we capture not just *where* people are, but *how they move* — distinguishing between destination zones (where people linger) and pathway zones (where people pass through at high volume). Both have advertising value, but for completely different reasons and to different advertisers.

**Core model — Premium Space Score (PSS):**

| Signal | Weight | Why It Matters |
|---|---|---|
| Unique Reach (Footfall) | 35% | More devices = more impressions |
| Avg. Dwell Time per Device | 30% | Longer exposure = better ad absorption |
| Throughflow (Transitions) | 25% | Being on high-traffic paths matters even without long dwell |
| Repeat Exposure Rate | 10% | Repeat passes reinforce brand recognition |

**Top result:** Main Corridor 2 (PSS 0.89) and Food Court (PSS 0.85) emerge as the top premium placements — for opposite reasons. COR_2 wins on throughflow (8,234 transitions); Food Court wins on dwell time (5.80 min/device avg.).

---

## Visual Insights

<img width="890" height="242" alt="image" src="https://github.com/user-attachments/assets/876470fa-aa48-4cbf-9da1-5c89f80c11c9" />

### 1. Venue Zone Map — Premium Placement Candidates
Zones are plotted at their physical (x, y) coordinates. Highlighted (orange rings) are the top premium placement candidates identified by the PSS model. Central corridor zones (COR_1, COR_2, COR_3) sit on the main circulation spine and capture the highest throughflow. Gallery zones (GALL_1, GALL_2) and the Food Court sit off the spine but score well on dwell and repeat metrics.


### 2. Footfall by Zone
The Entrance leads with ~395 sessions, followed by Transit Hub (~360) and Food Court (~340). However, raw footfall is a misleading proxy for ad value — the Lounge sits near the bottom at ~205 sessions yet ranks 2nd in PSS due to its dwell time and repeat rate.


### 3. Average Dwell Time by Zone
The Lounge (~29 min) and Food Court (~24 min) dominate dwell time. The Entrance and Exit average under 3 minutes — visitors are purely in transit and unlikely to engage with any ad content.


### 4. Repeat Visitor Rate by Zone
Food Court leads at ~60% repeat rate, followed by Transit Hub (~51%) and Lounge (~50%). The Entrance has only a ~33% repeat rate despite its volume, and the Exit drops to ~9%, making both poor candidates for frequency-based brand campaigns.

---

### 5. Hourly Traffic Heatmap
Traffic peaks differ sharply by zone. The Entrance spikes at hour 13 (1 PM). Transit Hub peaks at hours 13–15. Food Court and Retail Zone A show strong mid-morning (10 AM) surges alongside afternoon peaks. The Lounge has the most even distribution — ideal for always-on brand awareness campaigns.

<img width="630" height="292" alt="image" src="https://github.com/user-attachments/assets/47c8c740-62fb-44f0-9732-9f84c2148ad9" />


---

### 6. Premium Space Score (PSS) by Zone
Food Court ranks first (0.855), driven by the combination of high footfall, long dwell time, and the highest repeat rate. Lounge ranks second (0.693) on the strength of dwell time alone. The Entrance ranks 5th (0.482) despite being the busiest zone. The Exit scores 0.000 — no premium placement is recommended there.

<img width="492" height="242" alt="image" src="https://github.com/user-attachments/assets/0be6834e-e2a6-4a88-ad31-6fa3a7bb065e" />


---

### 7. Zone Transition Flow Matrix
The Entrance is the dominant origin — 48 self-transitions (repeat passes), 41 to Transit Hub, and 35 to Retail Zone A. Food Court and Transit Hub show strong self-loops (32 and 33 respectively), confirming habitual return behavior. The primary circulation spine runs: **Entrance → Transit Hub → Food Court**, forming the highest-value sequential ad placement path.

<img width="1023" height="784" alt="image" src="https://github.com/user-attachments/assets/a0ce3587-0661-4428-9dd4-535a024350cb" />

---

## Recommendations

| Zone | PSS | Ad Format | Rationale |
|---|---|---|---|
| Food Court | 0.855 | Video (15–30 sec), QR codes, loyalty sign-ups | Highest dwell + highest repeat rate — captive and returning audience |
| Lounge | 0.693 | Multi-panel narrative, interactive displays | Longest avg. dwell (~29 min) — visitors will read and engage |
| Transit Hub / COR_2 | 0.625 | Sequential hook ad (Part 1 of 2) | Massive throughflow + strong self-loop (33) — ideal for campaign continuity |
| Retail Zone A / GALL_1 | 0.609 | Product feature + single CTA | Mid-dwell (~17 min), high inflow from Entrance and COR_2 |
| Entrance | 0.482 | Single phrase or logo only | Highest footfall but lowest dwell (~2.5 min) — 3 seconds to register, no reading |
| Retail Zone B | 0.475 | Product feature + single CTA | Similar profile to Zone A but lower inflow — secondary placement |
| Exit | 0.000 | Brand recall tagline only | Near-zero dwell and repeat — avoid premium pricing |

**Day-parting tip:** Schedule Food Court and Retail Zone A digital creatives for 10 AM and 1 PM surges. Use the Lounge's even traffic distribution for always-on brand awareness.

**Sequential campaign tip:** Use the Entrance → Transit Hub → Food Court circulation spine for a 3-part progressive ad: hook at Entrance (1 phrase), reinforce at Transit Hub (product name + CTA), convert at Food Court (full narrative or QR code).

---

## Ad Format Guide by Zone Profile

| Zone Profile | Dwell | Repeat Rate | Best Ad Format |
|---|---|---|---|
| High traffic, low dwell | < 5 min | Low | Single phrase or logo — billboard style, 3 seconds to register |
| High traffic, medium dwell | 5–15 min | Medium | Short copy + one CTA — product name, price, availability |
| Medium traffic, high dwell | 15–30 min | High | Long-form, video, multi-panel — brand story, QR code, sign-up |
| Low traffic, high dwell | > 20 min | High | Experiential or interactive — touch screen, survey kiosk |
| Any traffic, high repeat | Any | > 50% | Sequential / retargeting — two-part creative across visits |
| Low traffic, low dwell | < 5 min | < 15% | No premium placement recommended |

---

## How It Works (Technical Summary)

The venue is modeled as a **graph** — nodes are zones, edges are walkable connections. WiFi pings are reconstructed into sessions using a 15-minute inactivity gap threshold. Four metric categories are computed per zone:

1. **Reach** — unique devices per zone
2. **Dwell Time** — avg. minutes per device (capped at 8 min per ping interval to prevent sparse-ping inflation)
3. **Throughflow** — total inbound + outbound transitions between zones
4. **Repeat Rate** — share of sessions where a device re-enters the same zone

All metrics are min-max normalized to [0, 1] before applying the weighted PSS formula:

```
PSS = 0.35 × norm_reach + 0.30 × norm_dwell + 0.25 × norm_throughflow + 0.10 × norm_repeat
```

**Tools:** Python · Pandas · NumPy · NetworkX · Matplotlib · Seaborn · Jupyter Notebook

[View Full Analysis Notebook](wifi_analytics_premium_advertising.ipynb)

---

## Key Design Decisions

- **Graph structure over silo counting:** Corridor zones score high on throughflow even with low dwell — purely counting visitors would miss this entirely.
- **Dwell cap at 8 min/ping:** Prevents sparse WiFi pings from inflating dwell times for high-stickiness zones like the Food Court.
- **Throughflow weight (25%):** A hallway with 8,000 daily transitions is more valuable than a quiet corner — even if nobody lingers there.
- **Weights are adjustable:** For timed campaigns (movie posters), increase throughflow weight. For detailed product displays, increase dwell weight.

---

## Privacy and Ethics

| Must Do | Must Never Do |
|---|---|
| Track anonymous device IDs only (hashed MACs) | Track or identify specific individuals |
| Aggregate before analysis | Link WiFi data to payment or personal info |
| Post clear venue signage about WiFi analytics | Share raw movement data with third parties |
| Comply with GDPR / local privacy laws | Use data beyond disclosed purposes |
| Delete raw pings after aggregation | — |

---

## Next Steps and Limitations

- Expand to 50K+ real sessions across multiple venues for statistically robust zone benchmarks
- Integrate device-type proxies as demographic signals (where permissible) to layer audience quality
- Run A/B tests using PSS-ranked vs. footfall-ranked zones to validate revenue uplift in practice
- Extend to path analysis — model full journeys (Entry → Corridor → Food Court → Exit) for sequential ad sequencing
- Add causal uplift modeling to estimate the counterfactual: what would engagement be *without* a placement?
- **Limitation:** Simulated data reflects realistic behavioral patterns but is not sourced from a live venue — real-world results may vary by venue type, size, and layout

---

## Technical Note
All data is simulated for educational purposes. Real deployments require proper WiFi infrastructure, privacy compliance, and ground-truth validation.
---
Inspired by: [Finding the Perfect Spot — WiFi Analytics for Premium Space Advertising](https://medium.com/data-science-collective/finding-the-perfect-spot-wifi-analytics-for-premium-space-advertising-b8470dd9181b) by Herman Wandabwa
