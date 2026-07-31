# daily-build-prompt.md — StockSense 30-Day Growth Plan

Copy this prompt exactly, changing only the day number, and paste it at the start of a fresh conversation each day during the 30-day growth plan.

---

```
StockSense — 30-Day Growth Plan — Day [X]

I'm continuing the 30-day growth plan for StockSense, a live web app built as a 10-day
capstone for the AB Talks 60-Day Claude AI Challenge (repo:
https://github.com/noobie007-sigma/stocksense, live:
https://noobie007-sigma.github.io/stocksense/).

Please read docs/30-day-growth-plan.md from the repo (I'll upload or paste it if you
don't have it) and use it as the source of truth. Today is Day [X] of that plan —
complete only today's milestone. Do not redesign the project, skip ahead, or
introduce scope not listed for today.

Also read docs/ARCHITECTURE.md, docs/SCHEMA.md, and docs/API.md so you understand
the existing conventions (vanilla JS, no framework, escapeHtml everywhere, null-safe
ratio/flag logic, render* function naming pattern) and follow them rather than
introducing new patterns.

Standing rules:
- Assume I need step-by-step guidance for any manual task (installing tools,
  configuring services, deploying, running commands) using exact button/menu names
  and terminal commands. Wait for my confirmation before continuing.
- Prioritize implementation over explanation — generate complete, copy-pasteable
  files, not snippets or placeholders.
- Only use free tools/services unless I explicitly approve a paid one.
- If today's milestone reveals a real blocker (e.g. a chosen tool/API turns out
  unreliable), stop, explain the issue plainly, propose the smallest reasonable
  adjustment, and ask for my approval before deviating from the plan — the same
  approach we used successfully on Day 2 of the original capstone.
- Verify today's work actually functions (ask me to test and confirm) before
  considering the day done.
- End by updating any affected docs, giving me a commit message, and a one-line
  summary of what's ready for Day [X+1].

Begin with Day [X]'s milestone now.
```