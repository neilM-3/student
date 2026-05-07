---
toc: True
layout: post
title: AP CSP Study Plan - Week of 4/30
description: My personal end-of-week study plan for the AP Computer Science Principles exam. Built around six pillars - Teach Lesson, Personal Goals, Personal Blog, code_runners, AP Classroom MCQs, and Study Partner work - plus a full breakdown of the Create Performance Task and the StudySmart Python tool I built to keep myself on track.
categories: [AP CSP, Study Plan]
author: Neil Manjrekar
permalink: /apcsp/study-plan/
breadcrumb: True
---

## Why this plan exists

The AP CSP exam is on its way and "study harder" is not a plan. This post turns the 4/30 Study Week End Slack message into something I can actually follow day-by-day. Each section below is one of the six pillars from the message, plus a breakdown of the Create Performance Task (the big written/code portion) and the Python tool I wrote to rank what to study first.

The goal: walk into the exam knowing exactly what I practiced, why, and how I tracked it.

---

## The 6 Pillars

| # | Pillar | What it is | How often |
| :-- | :-- | :-- | :-- |
| 1 | Teach Lesson | Re-teach a CSP topic to a peer or to myself out loud | 2x / week |
| 2 | Personal Goals | A weekly goal sheet I check off | Every Monday |
| 3 | Personal Blog | A short post on whatever I learned that day | 3x / week |
| 4 | code_runners | Hands-on coding from the code_runners repo / hacks | Daily, 30 min |
| 5 | AP Classroom MCQ | Official MCQs from College Board's AP Classroom | Daily, 10-15 Qs |
| 6 | Study Partner / Group | Quiz each other, trade Create PT feedback | 2x / week |

---

### 1. Teach Lesson

If you can teach it, you understand it. For each Big Idea, I pick one sub-topic and explain it to a partner (or to a webcam) in under 5 minutes.

Topics I rotate through:

- Big Idea 1 - Creative Development (collaboration, program design)
- Big Idea 2 - Data (binary, compression, metadata)
- Big Idea 3 - Algorithms and Programming (loops, lists, procedures, logic)
- Big Idea 4 - Computer Systems and Networks (Internet, fault tolerance, parallel vs. distributed)
- Big Idea 5 - Impact of Computing (digital divide, bias, crowdsourcing)

When I get stuck mid-explanation, that is the topic I add to my next blog post.

### 2. Personal Goals

A short Monday checklist, kept honest by writing it down:

- [ ] 5 AP Classroom MCQ sets this week
- [ ] 1 Teach Lesson session
- [ ] 1 code_runners hack pushed to GitHub
- [ ] 1 blog post written
- [ ] 30 min reviewing the AP pseudocode reference sheet
- [ ] 1 Create PT working session (60+ min)

The point is small wins, not all-nighters.

### 3. Personal Blog

Every blog post follows the same 3-question template so I do not stare at a blank page:

1. **What did I study today?**
2. **What confused me?**
3. **What is one example I can show in code or pseudocode?**

This becomes a searchable record of weak spots before the exam.

### 4. code_runners

This is where the hours go. I treat code_runners hacks like reps at the gym - short, frequent, painful in a good way. Focus areas for the final stretch:

- Hand-tracing loops and `REPEAT UNTIL`
- List operations: `INSERT`, `REMOVE`, `APPEND` (remember: AP pseudocode lists start at index **1**)
- Procedures with parameters, return values, and abstraction
- Boolean logic with `AND`, `OR`, `NOT` and truth tables

### 5. AP Classroom MCQ Work

College Board's AP Classroom is the closest thing to the real exam. My rule: **do not just check answers - read every explanation, even on the ones I got right.** That is where the actual learning is hiding.

Daily target: **10-15 questions**, mixed across all 5 Big Ideas, not just one.

### 6. Study Partner / Group

Two things that only work with another human:

- **Quiz swaps** - we each write 5 MCQs and swap. Writing a question forces you to know the topic better than answering one.
- **Create PT feedback** - read each other's code and written responses. If your partner cannot follow your written response, the College Board reader will not either.

---

## The Create Performance Task (the "FRQ" part)

The Create Performance Task is the 30% of the exam that is **not** multiple choice. It is submitted to the College Board digital portfolio **before exam day** - so the work happens in class, not on May 7.

### What you submit

1. **Program code** (PDF of your code)
2. **Video** of your program running (max 1 minute, no narration required)
3. **Written Responses** answering 4 prompts (3a, 3b, 3c, 3d)

### What your program MUST contain (rubric requirements)

To earn full points, your code has to clearly show:

- **A list** (or other collection type) used to manage complexity
- **A student-developed procedure** with at least one parameter that affects the result
- **Sequencing, selection, and iteration** inside that procedure
- **Calls** to that procedure

These are not "nice to have" - they are the row-by-row checkboxes a College Board reader uses to score you. If any one is missing, you lose multiple points.

### The 4 written response prompts (high level)

| Prompt | What it asks | Common trap |
| :-- | :-- | :-- |
| 3a | Describe the overall purpose of your program | Going too vague - say what input/output actually happens |
| 3b | Describe how a list manages complexity | Saying "it stores data" is not enough - explain what would be harder *without* the list |
| 3c | Show your student-developed procedure and explain the algorithm inside it | Forgetting to point to sequencing AND selection AND iteration |
| 3d | Describe two calls to the procedure with different arguments and the different results | The arguments must actually be different in a meaningful way |

### My Create PT: StudySmart

I wrote a small Python program called **StudySmart** that takes a list of assignments and ranks what I should study first based on difficulty, hours, and how soon it is due. It hits every rubric requirement:

- **List**: `assignments` holds every assignment dict
- **Procedure with parameter**: `calculate_priority(assignment)` - the parameter changes the result
- **Selection**: `if/elif/else` for urgency tiers
- **Iteration**: loops through every assignment to score it
- **Procedure calls**: called from `optimize()` for each assignment

Full code:

```python
# ============================================================
#  StudySmart - studysmart.py
#  Run with: python studysmart.py
#
#  CPT Elements:
#    [LIST]      -> assignments list
#    [PROCEDURE] -> calculate_priority(assignment)
#    [SELECTION] -> if/elif/else inside calculate_priority()
#    [ITERATION] -> loops through every assignment to score
#    [INPUT]     -> input() prompts
#    [OUTPUT]    -> print_ranked_list(), print_focus_today()
# ============================================================

from datetime import date, datetime


# ============================================================
#  [LIST] - stores all assignment dictionaries
# ============================================================
assignments = []


# ============================================================
#  [PROCEDURE] - calculate_priority(assignment)
#  Takes one assignment dict, returns score + urgency info.
# ============================================================
def calculate_priority(assignment):
    today = date.today()
    due = datetime.strptime(assignment["due_date"], "%Y-%m-%d").date()
    days_left = (due - today).days

    # [SELECTION] - urgency bonus based on days remaining
    if days_left <= 1:
        urgency_bonus = 20
        urgency_label = "CRITICAL"
    elif days_left <= 3:
        urgency_bonus = 10
        urgency_label = "HIGH"
    elif days_left <= 7:
        urgency_bonus = 5
        urgency_label = "MEDIUM"
    else:
        urgency_bonus = 0
        urgency_label = "LOW"

    score = (assignment["difficulty"] * 2) + (assignment["hours"] * 1.5) + urgency_bonus
    return {
        "score": round(score, 1),
        "days_left": days_left,
        "urgency_bonus": urgency_bonus,
        "urgency_label": urgency_label
    }


# ============================================================
#  [OUTPUT] - print ranked list
# ============================================================
def print_ranked_list(scored):
    print("\n" + "=" * 60)
    print("  PRIORITY RANKINGS")
    print("=" * 60)

    # [ITERATION] - loop through every scored assignment
    for i, a in enumerate(scored):
        if a["days_left"] <= 0:
            due_str = "PAST DUE"
        elif a["days_left"] == 1:
            due_str = "due tomorrow"
        else:
            due_str = f"due in {a['days_left']} days"

        stars = "*" * a["difficulty"] + "-" * (5 - a["difficulty"])
        print(f"\n  #{i + 1}  {a['name']} ({a['subject']})")
        print(f"       Score: {a['score']}  |  Urgency: {a['urgency_label']}  |  {due_str}")
        print(f"       Difficulty: {stars}  |  Est. {a['hours']}h")


# ============================================================
#  [OUTPUT] - print today's focus plan
# ============================================================
def print_focus_today(scored):
    print("\n" + "=" * 60)
    print("  FOCUS TODAY")
    print("=" * 60)

    top = scored[:3]

    # [ITERATION] - loop through top assignments
    for a in top:
        # [SELECTION] - hours based on urgency
        if a["urgency_bonus"] == 20:
            today_hours = a["hours"]
        elif a["urgency_bonus"] == 10:
            today_hours = -(-a["hours"] * 0.6 // 1)  # ceiling
        else:
            today_hours = -(-a["hours"] * 0.3 // 1)  # ceiling

        print(f"\n  -> {a['name']} ({a['subject']})")
        print(f"     Spend {int(today_hours)}h on this today")

    print("\n" + "=" * 60 + "\n")


# ============================================================
#  [INPUT] - prompt user to enter an assignment
# ============================================================
def add_assignment():
    print("\n--- Add Assignment ---")
    name = input("Name: ").strip()
    subject = input("Subject: ").strip()

    while True:
        try:
            difficulty = int(input("Difficulty (1-5): "))
            if 1 <= difficulty <= 5:
                break
        except ValueError:
            pass
        print("  Enter a number 1-5.")

    while True:
        try:
            hours = float(input("Estimated hours: "))
            if hours > 0:
                break
        except ValueError:
            pass
        print("  Enter a positive number.")

    while True:
        due_date = input("Due date (YYYY-MM-DD): ").strip()
        try:
            datetime.strptime(due_date, "%Y-%m-%d")
            break
        except ValueError:
            print("  Use format YYYY-MM-DD, e.g. 2026-05-10")

    # [LIST] - append new assignment dict to list
    assignments.append({
        "name": name,
        "subject": subject,
        "difficulty": difficulty,
        "hours": hours,
        "due_date": due_date
    })
    print(f'  Added "{name}"')


# ============================================================
#  Optimize - score + sort + display
# ============================================================
def optimize():
    if not assignments:
        print("\n  No assignments to optimize. Add some first.\n")
        return

    scored = []

    # [ITERATION] - loop through [LIST] and score each one
    for a in assignments:
        result = calculate_priority(a)
        scored.append({**a, **result})

    scored.sort(key=lambda x: x["score"], reverse=True)

    print_ranked_list(scored)   # [OUTPUT]
    print_focus_today(scored)   # [OUTPUT]


# ============================================================
#  List current assignments
# ============================================================
def list_assignments():
    if not assignments:
        print("\n  No assignments yet.\n")
        return
    print("\n--- Current Assignments ---")
    for i, a in enumerate(assignments):
        print(f"  {i + 1}. {a['name']} ({a['subject']}) | diff {a['difficulty']} | {a['hours']}h | Due: {a['due_date']}")
    print()


# ============================================================
#  Remove an assignment
# ============================================================
def remove_assignment():
    list_assignments()
    if not assignments:
        return
    try:
        num = int(input("Remove which number? "))
        if 1 <= num <= len(assignments):
            removed = assignments.pop(num - 1)
            print(f'  Removed "{removed["name"]}"')
        else:
            print("  Invalid number.")
    except ValueError:
        print("  Enter a valid number.")


# ============================================================
#  Main menu loop
# ============================================================
def main():
    print("\n=================================")
    print("       S T U D Y S M A R T        ")
    print("   Know what to study first       ")
    print("=================================")

    while True:
        print("\nWhat do you want to do?")
        print("  1. Add assignment")
        print("  2. List assignments")
        print("  3. Optimize priority")
        print("  4. Remove assignment")
        print("  5. Quit")
        choice = input("\nChoice: ").strip()

        if choice == "1":
            add_assignment()
        elif choice == "2":
            list_assignments()
        elif choice == "3":
            optimize()
        elif choice == "4":
            remove_assignment()
        elif choice == "5":
            print("\nGood luck studying!\n")
            break
        else:
            print("  Pick 1-5.")


if __name__ == "__main__":
    main()
```

### How I will write up StudySmart for the 4 prompts

- **3a (purpose):** "StudySmart takes a list of school assignments and outputs a priority-ranked study plan based on difficulty, estimated hours, and days until due. The user adds, lists, or removes assignments through a menu, then runs Optimize to see what to focus on first."
- **3b (list managing complexity):** Without the `assignments` list, I would need a separate variable for every assignment - I could not loop, sort, or rank them. The list lets one procedure work on any number of assignments.
- **3c (procedure):** `calculate_priority(assignment)` - shows sequencing (compute days_left, then bonus, then score), selection (the urgency `if/elif/else`), and iteration is shown in the *caller* `optimize()` which loops the list and calls this procedure.
- **3d (two calls, different results):** Call 1 with an assignment due in 1 day -> `urgency_bonus = 20`, label `CRITICAL`. Call 2 with an assignment due in 10 days -> `urgency_bonus = 0`, label `LOW`. Same procedure, different results, driven by the parameter.

---

## My Final 7-Day Schedule

| Day | code_runners | AP Classroom | Other |
| :-- | :-- | :-- | :-- |
| Mon | 30 min - loops | 15 MCQ - Big Idea 3 | Set weekly goals |
| Tue | 30 min - lists | 10 MCQ - Big Idea 2 | Teach Lesson w/ partner |
| Wed | 30 min - procedures | 15 MCQ - Big Idea 3 | Blog post |
| Thu | 30 min - logic | 10 MCQ - Big Idea 4 | Create PT writing session |
| Fri | 30 min - tracing | 15 MCQ - Big Idea 5 | Quiz swap with partner |
| Sat | 60 min - mixed review | Full 30-question timed set | Blog post |
| Sun | Rest / light review | Review wrong answers only | Reset for next week |

---

## Quick Reference: AP Pseudocode Gotchas

- Lists are **1-indexed**, not 0-indexed
- `<-` is assignment, `=` is comparison
- `MOD` is the modulo operator
- `RANDOM(a, b)` returns an integer between `a` and `b` **inclusive**
- `DISPLAY` prints, `INPUT` reads

If I confuse any of these on exam day, I lose points I already know how to earn. Worth re-reading the official reference once a week until exam day.

---

## End-of-week check-in

Every Sunday I answer three questions in a blog post:

1. Which pillar did I actually do this week, and which did I skip?
2. What is the one topic I am still weakest on?
3. What is the one thing I will change next week?

That is the whole plan. Small, repeatable, honest.
  