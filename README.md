# marufahmed.github.io

Personal portfolio of **Maruf Ahmed** — AI / ML engineer building production systems
across edge computing, computer vision, and agentic AI.

🔗 **Live site:** https://marufahmed.github.io/

![Repo views](https://visitor-badge.laobi.icu/badge?page_id=marufahmed.marufahmed.github.io)

> ⚠️ **Note:** the badge above counts **README / repository views on GitHub** — *not*
> visits to the live website. See [Tracking real site visitors](#tracking-real-site-visitors)
> for how to measure actual traffic to the deployed page.

---

## The journey

The work in this portfolio follows one through-line: **earn the result with the leanest
thing that works** — pushing intelligence to the edge, matching the model to the job, and
paying only for what runs.

- **Research roots.** It started in the lab — an SSD-based crack-detection network running
  on an Nvidia Jetson TX2 mounted on an autonomous vehicle, built as NSF REU Fellowship
  research. Edge-first, even then.
- **National scale.** That instinct grew into a production **Bengali OCR system** deployed
  behind a public government service (`ocr.bangla.gov.bd`), trained on the largest curated
  Bengali OCR dataset to date.
- **Industry impact.** Then **Argus** — the world's first AI-powered garment measurement
  platform using Structured Light 3D sensing, with inference cut 82% (4.5s → 0.8s) via
  Triton + TensorRT and validated by BGMEA board members.
- **Agentic & AI-native.** Most recently, end-to-end AI products: a constraint-solver-driven
  special-education operations platform, 3D body reconstruction from photos, document-
  intelligence finance tracking, and a real-time Bengali voice agent.

Read the full write-ups in [`project-details.md`](project-details.md) — the same notes that
power the deep-dive popups on the site.

---

## Featured work

| # | Project | What it is |
|---|---------|-----------|
| 01 | **Argus** | AI garment QA/QC via Structured Light 3D sensing (edge / IoT) |
| 02 | **Bloomridge Springs** | AI-native special-education ops platform (OR-Tools CP-SAT + agents) |
| 03 | **SwiftFit** | 3D body measurement from photos (MExECON / SMPL-X) |
| 04 | **SwiftWallet** | Receipt & statement → categorized finance (vision-OCR + LLM) |
| 05 | **Bengali Voice Agent** | Real-time, full-duplex appointment booking in Bengali |
| 06 | **Melody** | Enterprise RAG chat assistant (hybrid retrieval) |
| 07 | **GPU-on-Demand MLOps** | On-demand multi-cloud GPU provisioning (95% cost cut) |
| 08 | **Bengali OCR** | National-scale OCR (`ocr.bangla.gov.bd`) |
| 09 | **Pavement Crack Detection** | On-vehicle SSD detection on Jetson TX2 (NSF REU) |

---

## Tech

A hand-built static site — no framework, no build step.

- **HTML / CSS / vanilla JS**
- **GSAP + ScrollTrigger** for scroll animation
- Custom **auto-playing carousel** (vertical pan reveal), **zoom lightbox**, and
  **deep-dive project modals** — all written from scratch
- Deployed with **GitHub Pages**

## Structure

```
index.html           # markup
styles.css           # styles
script.js            # animations, carousel, lightbox, modals
project-details.md   # source-of-record for the deep-dive popup notes
assets/images/       # project media (images, video, gifs)
```

## Local development

No build required. Open `index.html` directly, or serve it:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

---

## Tracking real site visitors

To count actual visits to `marufahmed.github.io`, add a small analytics script to
`index.html` (a README badge can't do this). Privacy-friendly, free options:

- **[GoatCounter](https://www.goatcounter.com/)** — easiest for a personal site. Register a
  free account, then add one line before `</body>`:
  ```html
  <script data-goatcounter="https://YOURCODE.goatcounter.com/count"
          async src="//gc.zgo.at/count.js"></script>
  ```
- **[Cloudflare Web Analytics](https://www.cloudflare.com/web-analytics/)** — free, no
  cookies; paste the provided beacon snippet into `index.html`.
- **Google Analytics 4** — free and detailed, but uses cookies (needs a consent notice in
  many regions).

Once you have a code/token from any of the above, drop it in and I'll wire it into the page.

---

© 2025 Maruf Ahmed
