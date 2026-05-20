# Dyania Health Hackathon 2025 — Team Submission Repository

**Challenge:** Early patient risk detection — Using ML to identify liver disease (MASH) in patients with compounding metabolic conditions (CKM)  
**Event:** May 27–28, 2025 (36 hours)  
**Team:** `[Your team name]`  
**Members:** `[Name — Role]`, `[Name — Role]`, `[Name — Role]`

---

## Problem Statement

> *Fill in: 2–3 sentences framing the clinical problem your team is solving. What is the detection gap? Who is affected? What is the cost of late identification?*

## Our Approach

> *Fill in: Briefly describe your team's strategy. What data sources are you targeting? What ML approach did you choose and why? What makes your pre-screening design clinically actionable?*

## Key Design Decisions

> *Fill in: List 3–5 deliberate choices your team made (e.g., model choice, feature selection philosophy, how you defined ground truth, how you handled missing data). For each, explain the reasoning.*

| Decision | Rationale |
|---|---|
| | |
| | |
| | |

---

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/dyania-health/dyania-hackathon-case-study.git
cd dyania-hackathon-case-study
```

### 2. Create your team branch

Branch names must follow this format: `team/<your-team-name>` (lowercase, hyphens for spaces).

```bash
git checkout -b team/your-team-name
```

Examples: `team/panathinea`, `team/liver-hawks`, `team/ckm-busters`

### 3. Work on your branch

Edit the template files inside `protocol/`, `ml/`, `data/`, and `presentation/`. Every `> *Fill in:*` block is a placeholder — replace it with your team's content.

```bash
# Stage and commit as you go
git add .
git commit -m "your message"
```

### 4. Submit — push your branch before the deadline

```bash
git push origin team/your-team-name
```

> **Deadline: May 28, 2025 — before the presentation session.**  
> Only the last commit pushed before the deadline will be evaluated.  
> Do **not** open a Pull Request — just push your branch.

---

## Repository Structure

```
.
├── README.md                        # This file — team overview and key decisions
├── protocol/
│   └── study_protocol.md            # Full study design (main deliverable)
├── ml/
│   └── approach.md                  # ML methodology and validation strategy
├── data/
│   └── data_plan.md                 # Data sources, preprocessing, availability
├── presentation/
│   └── slides.pdf                   # 5–10 slide deck for expert panel
└── notebooks/                       # (Optional) Proof-of-concept implementation
```

---

## Submission Checklist

- [ ] `README.md` — team overview, problem framing, key design decisions
- [ ] `protocol/study_protocol.md` — complete study protocol
- [ ] `ml/approach.md` — ML methodology
- [ ] `data/data_plan.md` — data plan
- [ ] `presentation/slides.pdf` — slide deck
- [ ] `notebooks/` — proof-of-concept (optional, evaluated positively if present)
