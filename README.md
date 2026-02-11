# SkyGeni Sales Intelligence Challenge

## Overview

This project analyzes B2B SaaS sales data to help the CRO understand why win rates are dropping and where the sales team should focus their efforts.

**Dataset**: 5,000 deals from 2023-2024  
**Problem**: Win rate dropped from ~48% to ~44% despite healthy pipeline volume  
**Goal**: Identify actionable insights to reverse the trend

---

# PART 1 – Problem Framing

## 1️⃣ What is the REAL business problem?

The CRO is seeing a troubling pattern: **more deals are entering the pipeline, but fewer are closing**. This isn't just a win rate problem – it's a resource allocation problem.

Here's what's actually happening:

- **Surface issue**: Win rate dropped 4 percentage points over 2 quarters
- **Underlying issue**: The sales team is chasing the wrong deals
- **Real impact**: Reps are burning time on deals that won't close, missing real opportunities

Think of it like a leaky bucket. We can keep pouring more water in (generating leads), but if the holes keep getting bigger (poor conversion), we'll never fill it up.

**The business problem isn't "why are we losing deals?" It's "how do we help our sales team focus on winnable deals before they waste time on duds?"**

This matters because:
- Sales capacity is finite (can't just hire our way out)
- Long sales cycles on lost deals = opportunity cost
- Morale suffers when reps work hard but lose deals
- Revenue targets are at risk if trend continues

## 2️⃣ What questions should the AI answer?

A CRO doesn't need a fancy dashboard – they need answers to specific questions that drive decisions:

### Deal-Level Questions
- **Which open deals are likely to die?** (so we can intervene or cut bait)
- **What's different about deals we win vs lose?** (so we can replicate success)
- **Are deals dying early or late?** (determines where to fix the process)

### Segment Questions
- **Which industries/regions are underperforming?** (resource reallocation)
- **Is this a rep problem or a product problem?** (training vs product-market fit)
- **Are we pursuing the wrong deal sizes?** (should we focus on SMB vs Enterprise?)

### Trend Questions
- **What changed in the last 2 quarters?** (find the inflection point)
- **Are we getting worse at specific stages?** (where's the breakdown?)
- **Is our sales cycle getting longer?** (efficiency issue)

The system should answer: **"Here are the 3 things you should change this week to improve win rate."**

## 3️⃣ Metrics that matter most

Standard metrics (win rate, deal size) tell you WHAT happened. We need metrics that tell you WHY and WHERE TO ACT.

### Core Metrics
1. **Win Rate by Stage**
   - Why: Shows WHERE deals fall apart (qualification? pricing? final decision?)
   - Action: If losses happen late, improve objection handling. If early, better qualification.

2. **Win Rate by Cohort** (time-based)
   - Why: Isolates whether problem is seasonal, market shift, or process breakdown
   - Action: Compare apples to apples – Q4 2023 vs Q4 2022

3. **Sales Cycle Length** (won vs lost)
   - Why: Deals that drag usually die. Fast decisions = strong fit.
   - Action: If lost deals have longer cycles, implement time-based qualification

4. **Win Rate by Deal Size**
   - Why: Tells us if we're chasing too big or too small
   - Action: Might need to shift target customer profile

### Advanced Metrics
5. **Pipeline Velocity** (how fast deals move through stages)
   - Why: Stalled deals = indecisive buyers = low win probability
   - Action: Flag deals stuck >30 days in same stage

6. **Coverage by Segment** (pipeline $ / quota $)
   - Why: Some segments might be starved for attention
   - Action: Reallocate resources to undersupplied segments

## 4️⃣ Assumptions (and what could be wrong)

Being honest about assumptions is more important than being right. Here's what we're assuming:

### Data Assumptions
**Assumption**: All deals are logged in CRM consistently  
**Reality check**: Reps probably update deals unevenly. Some might skip stages or backdate entries.  
**Impact**: Stage-based analysis could be noisy

**Assumption**: "Lost" means permanently lost (not just delayed)  
**Reality check**: Some "lost" deals might come back in 6 months under new budget cycle  
**Impact**: We might be overstating true loss rate

**Assumption**: Deal stages mean the same thing across all reps  
**Reality check**: One rep's "Qualified" might be another rep's "Demo"  
**Impact**: Stage win rates could be misleading

### Business Assumptions
**Assumption**: Past patterns predict future (historical data is still relevant)  
**Reality check**: If market changed dramatically (new competitor, economic shift), old data is less useful  
**Impact**: Model recommendations might be based on outdated patterns

**Assumption**: Sales process hasn't changed  
**Reality check**: If we changed methodology, pricing, or team in Q4, data isn't comparable  
**Impact**: Can't attribute causation correctly

**Assumption**: Deal amount and sales_cycle_days are accurately recorded  
**Reality check**: Amounts might be estimates; close dates might be when entered in CRM, not true close  
**Impact**: Efficiency metrics could be off

### What we DON'T have (and wish we did)
- Email/call activity (engagement signals)
- Buyer sentiment (are they ghosting us?)
- Competitive intel (who else are they talking to?)
- Champion strength (do we have an internal advocate?)
- Budget confirmation (are they real buyers?)

**Bottom line**: This analysis will give directional insights, not gospel truth. Use it to ask better questions, not as the final answer.

---

## Project Structure

```
skygeni-sales-intelligence/
│
├── data/
│   └── skygeni_sales_data.csv          # Raw sales data
│
├── notebooks/
│   ├── 01_eda.ipynb                    # Part 2: Exploration & Insights
│   └── 02_decision_engine.ipynb        # Part 3: Win Rate Driver Analysis
│
├── src/                                 # Helper functions (optional)
│
└── README.md                            # This file (Part 1)
```

---

## Next Steps

1. **Read this README** to understand the business context
2. **Run `01_eda.ipynb`** to discover insights from the data
3. **Run `02_decision_engine.ipynb`** to build the win rate driver analysis
4. **Review findings** with business lens (not just technical metrics)

---

# PART 4 – System Design (Conceptual)

## How This Works in Production

Not building a complex architecture – just a simple system that runs and delivers value.

### Architecture (Simple)

```
┌─────────────┐
│ Salesforce  │  (CRM data - deals, reps, stages)
│    CRM      │
└──────┬──────┘
       │
       │ Nightly extract (CSV export)
       ▼
┌─────────────────┐
│  Data Pipeline  │  (Clean data, handle missing values)
│   (Python)      │
└──────┬──────────┘
       │
       │ Cleaned data
       ▼
┌─────────────────────────┐
│  Decision Engine        │  (Run logistic regression model)
│  - Score all open deals │  (Generate health scores)
│  - Calculate metrics    │  (Pipeline health, efficiency)
└──────┬──────────────────┘
       │
       │ Scores + Alerts
       ▼
┌─────────────────────────┐
│   Alert Router          │  (Filter by priority)
│  - High risk deals      │
│  - Segment trends       │
└──────┬──────────────────┘
       │
       ├─────────────┬─────────────┐
       ▼             ▼             ▼
   ┌─────┐      ┌──────┐     ┌────────┐
   │Email│      │Slack │     │Dashboard│
   │Alerts│     │Channel│    │(Tableau)│
   └─────┘      └──────┘     └────────┘
```

### Example Alerts

**Deal-Level Alerts** (sent to reps):
```
🚨 HIGH PRIORITY
Deal: ABC Corp - $95K Enterprise
Health Score: 28/100
Issue: Partner lead + Long cycle (95 days) + Large deal
Action: Get exec sponsor involved or disqualify by Friday
```

**Segment Alerts** (sent to CRO):
```
⚠️ TREND WARNING
APAC region win rate dropped 12% WoW
Current: 38% | Last week: 50%
Root cause: 3 large Partner+Enterprise deals lost
Action: Review lead routing with APAC team
```

**Weekly Digest** (sent to sales leadership):
```
✅ WEEKLY INSIGHTS

Pipeline Health Index: 64/100 (↓ from 68 last week)

Top opportunities (prioritize these):
1. Healthcare + Inbound deals (72/100 avg health)
2. Pro product deals under $40K (68/100 avg health)

Risk areas (need attention):
1. 12 deals in "Qualified" stage >45 days
2. Partner channel win rate down to 41%

Recommendation: Run qualification audit this week
```

### How Often It Runs

**Daily batch** (good enough, no need for real-time):
- Runs at 7 AM every morning
- Processes previous day's CRM updates
- Scores all open deals
- Sends alerts if thresholds hit

**Weekly** (deeper analysis):
- Monday morning full report
- Trend analysis over 4 weeks
- Segment performance changes

**Real-time not needed** because:
- Sales cycles are weeks/months, not minutes
- CRO needs strategic insights, not second-by-second updates
- Simpler = more reliable = actually gets used

### What Could Go Wrong (Limitations)

**1. Data Quality Issues**
- Problem: Reps don't update CRM consistently
- Impact: Garbage in = garbage out
- Mitigation: Flag deals with missing data, require fields to be filled

**2. Model Assumes Past = Future**
- Problem: If market changes (new competitor, recession), old patterns break
- Impact: Model gives bad advice
- Mitigation: Retrain quarterly, monitor for drift, A/B test recommendations

**3. Missing the Human Element**
- Problem: No data on rep skill, buyer enthusiasm, champion strength
- Impact: Model can't see "soft" signals that matter
- Mitigation: Use as decision support, not decision maker. Rep overrides allowed.

**4. Alert Fatigue**
- Problem: Too many alerts = people ignore them
- Impact: System gets turned off
- Mitigation: Smart thresholds (only alert on >20% drop), weekly digest not daily spam

**5. Integration Complexity**
- Problem: Salesforce custom fields vary by company
- Impact: Hard to deploy at new customer
- Mitigation: Map fields at setup, document assumptions

---

# PART 5 – Reflection (Critical Thinking)

Being honest about what's weak and what's next.

## Weakest Assumptions

**1. "Qualified" means the same thing across reps**
- Reality: One rep's "qualified" is another's "demo requested"
- This makes stage-based analysis noisy
- Fix: Standardize qualification criteria with scorecard

**2. Lost deals stay lost**
- Reality: Some "lost" deals come back in 6 months
- We're treating them as permanently dead
- Fix: Track "lost-reopened" separately

**3. Historical patterns predict future**
- Reality: If we hired new product team or competitor launched, old data is stale
- Model recommendations could be wrong
- Fix: Monitor win rate weekly, retrain quarterly

## What Breaks in Real Life

**Scenario 1**: New competitor enters market
- Our model says "FinTech deals are great!"
- But new competitor targets FinTech
- Win rate drops but model still recommends FinTech
- **Solution**: Add competitor tracking, monitor model accuracy monthly

**Scenario 2**: Sales team changes methodology
- We implement new qualification process
- Historical data based on old process
- Model trained on old patterns = bad advice
- **Solution**: Retrain after major process changes, version the model

**Scenario 3**: Economic downturn
- Buyers suddenly care more about price
- Our "deal size doesn't matter much" finding breaks
- Large deals start losing more
- **Solution**: Add macroeconomic indicators, retrain in crisis

## What I'd Build in 1 Month (If I Had Resources)

**Week 1: Add Engagement Data**
- Email open rates (from Outreach/SalesLoft)
- Meeting frequency (from calendar)
- Response times (from email)
- Why: These predict interest better than demographics

**Week 2: Build Champion Strength Indicator**
- Use Gong/Chorus call transcripts
- NLP to detect: "I'll push this internally" vs "I need to think about it"
- Score champion commitment 1-10
- Why: Strong champion = 2x win rate (from experience)

**Week 3: Add Competitive Intelligence**
- Track when we compete against who (from loss reasons)
- Win rate by competitor
- Pricing comparisons
- Why: Knowing "we lose to Competitor X on price" = actionable

**Week 4: Build Next-Best-Action Engine**
- Not just "this deal is risky"
- But "this deal is risky BECAUSE X, so DO Y"
- Specific playbooks by risk type
- Why: Moves from diagnosis to prescription

## Least Confident Parts of This Analysis

**1. Custom metric weights**
- Pipeline Health Index uses 40/30/20/10 weights
- These are educated guesses, not data-driven
- Could be way off
- **Should**: A/B test different weights, see which predicts outcomes

**2. Deal size buckets**
- Chose $20K and $40K as cutoffs
- Based on eyeballing distribution
- Might miss non-linear effects
- **Should**: Try multiple bucketing schemes, pick best

**3. Feature interactions**
- Model treats each feature independently
- Reality: Partner + Enterprise is worse than sum of parts
- Not capturing these interactions well
- **Should**: Add interaction terms, maybe decision tree

**4. Causation vs correlation**
- "Long cycles = low win rate"
- But does long cycle CAUSE loss? Or do weak deals naturally take longer?
- Can't tell from this data
- **Should**: Run experiments (force shorter deadlines), measure impact

## What This System Can't Do (Important!)

**Can't predict**:
- When a deal will close (only IF it will close)
- Exact win probability (model is directional, not precise)
- Individual rep performance (confounded by territory, product mix)

**Can't replace**:
- Sales judgment (model is decision support, not decision maker)
- Customer conversations (still need to talk to buyers)
- Sales coaching (tells you WHAT but not HOW to fix it)

**Can't detect**:
- Buyer budget changes mid-cycle
- Competitor undercutting on price
- Champion leaving the company
- Economic shocks

## How I'd Measure Success (6 Months After Launch)

**Not** "model accuracy improved to 80%"  
**But** "business metrics improved":

1. **Win rate increased** from 45% to 50% (+5 points)
2. **Sales cycle decreased** from 64 days to 55 days (-14%)
3. **Pipeline quality up** (more deals in sweet spot segments)
4. **Sales productivity up** (reps spend time on winnable deals)
5. **Revenue per rep up** by 15-20%

**How to measure**:
- A/B test: Half of reps use the system, half don't
- Compare win rates after 3 months
- Survey reps: "Does this help you prioritize?"
- Track CRO decisions: "Did we shift marketing spend based on this?"

## Final Honest Take

This analysis is **directional, not definitive**.

It tells us:
- Which segments perform better (directionally correct)
- What patterns exist in historical data
- Where to focus investigation

It doesn't tell us:
-  Exactly why deals are lost (need buyer interviews)
-  How to fix individual rep performance (need coaching)
-  What competitors are doing (need market research)

**The value isn't in perfect predictions** – it's in helping leaders ask better questions:
- "Why is Partner + Enterprise failing?"
- "Should we stop selling to EdTech?"
- "Is our qualification process working?"

Use this as a flashlight in the dark, not a GPS to the destination.

---

**Note**: This is a business problem disguised as a data science problem. The best solution isn't the fanciest model – it's the one that changes how the sales team operates on Monday morning.
