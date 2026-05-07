# DevSecOps with AiOps and AWS

---

## Table of Contents

1. [What is DevOps?](#what-is-devops)
2. [DevSecOps](#devsecops)
3. [AiOps](#aiops)

---

## What is DevOps?

### The Software Development Life Cycle (SDLC)

Every software product goes through a set of phases before it reaches the customer — whether it is a banking app, an e-commerce site, or a school management system. This is called the **Software Development Life Cycle (SDLC)**:

1. **Requirements** — what does the customer want?
2. **Planning & Analysis** — how will we build it, how long, how much will it cost?
3. **Development** — engineers write the code
4. **Testing** — testers verify it works correctly
5. **Deployment / Handover** — the software is released to users
6. **Maintenance** — bugs are fixed, improvements are made

The way teams organize these phases has changed a lot over the years.

---

### Waterfall — The Old Way

Imagine a company gets a $1 million contract to build a school management system, to be delivered in 2 years. They hire architects, developers, testers, database admins, Linux engineers, network engineers — everyone needed.

The project unfolds like this:
- 1–2 months → Requirements gathering
- 2–3 months → Design and Architecture
- 9 months → Development
- 4 months → Testing and Deployment

**What goes wrong:** development and testing are separated by months. By the time testers see the code, there are 100 defects waiting. 20 are invalid (wasted effort), and the rest take weeks to fix. The teams barely talked during those 9 months, so nobody knows why certain decisions were made.

Worse — by the time the product is delivered, the customer's needs have changed. You built a "Maruti 800" when they now want something entirely different.

This model is called **Waterfall** because phases only flow downward — you cannot go back. It was the standard for decades and caused enormous waste.

---

### Agile — Deliver in Sprints

Agile fixed the "deliver everything at the end" problem by breaking the product into small pieces and releasing them in cycles called **sprints** (usually 2–4 weeks).

For the same school management system, instead of building everything at once:

- **Sprint 1** → User Management (registration, login, forgot password)
  - 20 days: development
  - 10 days: testing and deployment
  - Customer sees working software and gives feedback
- **Sprint 2** → Product Catalogue
- **Sprint 3** → Order Management
- ...and so on

Think of it like a school switching from one big year-end exam to daily slip tests. When students get feedback every day instead of once a year, pass rates jump from 40% to 99%. Agile brings the same idea to software — feedback comes continuously, not all at once at the end.

Agile introduced **standup meetings** (daily 15-minute syncs) and **sprint reviews** (demo to the customer at the end of each sprint). Defects got caught within days instead of months.

But Agile still had a gap: developers and operations (the team managing servers) worked separately. Developers would "throw code over the wall" and operations would try to deploy it. That friction is exactly what DevOps was built to fix.

---

### DevOps — Continuous Everything

Think about something as simple as a developer adding one text input field to a form:

```
Enter your first name: _________________
```

With DevOps, **the moment that change is committed**, an automated pipeline kicks off:

1. **Build** — code is compiled and packaged
2. **Scan** — checked for security vulnerabilities
3. **Test** — automated tests run immediately:
   - Enter 40 characters → should pass
   - Enter 1 character → should pass
   - Enter special characters → should it pass or fail?
   - Enter numbers → should it pass or fail?
4. **Deploy** — pushed to an environment
5. **Feedback** — developer knows within minutes if anything broke

> **DevOps is the process of developing, building, scanning, and testing the application continuously. Even if a developer writes a single line of code, it should be built, scanned, tested, and deployed immediately — and feedback should be given to the developer.**

Defect rates drop to 3–4% because problems are caught immediately. Teams coordinate better because the pipeline makes issues visible the moment they happen.


---

## DevSecOps

**DevSecOps** adds security explicitly into every stage of the DevOps pipeline. Traditionally, security scanning happened right before a release. At that point, fixing a vulnerability is expensive and delays everything.

DevSecOps means:
- Code is scanned as it is written (SAST — Static Application Security Testing)
- Dependencies are checked for known vulnerabilities on every build
- Running applications are probed for weaknesses (DAST — Dynamic Application Security Testing)
- Infrastructure configurations are audited for misconfigurations

The principle: **security is not a gate at the end — it is built into every step, and it is everyone's responsibility.**

---

## AiOps

Traditional monitoring is reactive: CPU crosses 80% for 5 minutes → alert fires → engineer wakes up at 3 AM → customers are already affected.

**AiOps (AI for IT Operations)** makes monitoring smarter in three ways:

### Anomaly Detection

Instead of fixed thresholds, AI learns what "normal" looks like for your system at different times. Monday at 3 AM with 10% CPU is normal. 60% CPU at 3 AM on a Monday is not — the system flags it automatically, even if it is below your configured threshold.

This is like a heart monitor in a hospital. The device does not just alert when the heart rate crosses 120 BPM — it understands what is normal for this specific patient and flags deviations from their baseline.

### Predictive Alerting

If CPU usage is at 10% in hour 1, 20% in hour 2, 30% in hour 3 — AI predicts it will cross 80% before it happens. You get warned in advance, not after customers start calling.

### Auto Remediation

For known failure patterns, the system fixes itself automatically. A crashed service restarts. Servers scale up when traffic spikes. A bad deployment rolls back. No human needs to wake up at 3 AM for problems the system already knows how to handle.

---
