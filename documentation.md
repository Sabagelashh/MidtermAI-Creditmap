# CreditMap — Documentation

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Application Structure](#2-application-structure)
3. [Course Data](#3-course-data)
4. [Equivalency Algorithm](#4-equivalency-algorithm)
5. [Supported Programs](#5-supported-programs)
6. [How to Use the Application](#6-how-to-use-the-application)
7. [Improvement Plan: AI-Powered Syllabus Comparison](#7-improvement-plan-ai-powered-syllabus-comparison)

---

## 1. Project Overview

The **CreditMap** is a single-file web application that helps a **Computer Science** student evaluate a potential transfer to another engineering or computing program. It displays the student's full course history, lets them select a target program, and then generates a course equivalency table mapping completed CS courses to their nearest counterparts in the chosen program.

**Technologies used:**

- HTML5
- CSS3 (custom properties, animations, grid/flexbox layout)
- Vanilla JavaScript (no frameworks, no external files)

---

## 2. Application Structure

The entire application lives in a **single `.html` file** with three logical sections embedded inside it:

```
transfer_planner.html
├── <style>   — All CSS (variables, layout, components, animations)
├── <body>    — HTML markup (header, tables, controls, results)
└── <script>  — All JavaScript (data, rendering, transfer logic)
```

### Key UI Components

| Component | Purpose |
|---|---|
| Program badge (top-right) | Displays the student's current program |
| Course history table | Lists all courses with Passed / Not Passed status |
| Stats row | Summary pill counts (total / passed / not passed) |
| Target program dropdown | Three programs the student can transfer to |
| Transfer button | Triggers the equivalency computation |
| Equivalency table | Shows course mappings for all passed courses |

---

## 3. Course Data

The course list is stored as a JavaScript array of objects. Each object has:

```js
{ name: "Course Name as in Argus",  passed: true | false }
```

### Courses from Argus

| # | Course | Passed |
|---|---|---|
| 1 | Academic Writing and Research Skills | Yes |
| 2 | Introduction to Computer Science | Yes |
| 3 | Calculus I | Yes |
| 4 | Discrete Mathematics | Yes |
| 5 | Programming Fundamentals | Yes |
| 6 | Calculus II | Yes |
| 7 | Linear Algebra | Yes |
| 8 | Object-Oriented Programming | Yes |
| 9 | Data Structures and Algorithms | Yes |
| 10 | Probability and Statistics | Yes |
| 11 | Computer Architecture | Yes |
| 12 | Database Systems | Yes |
| 13 | Web Development | Yes |
| 14 | Physics I | No |
| 15 | Operating Systems | No |
| 16 | Computer Networks | No |
| 17 | Software Engineering | No |
| 18 | Artificial Intelligence | No |
| 19 | Machine Learning | No |

### 5 Additional Courses (always Passed = Yes)

| # | Course | Passed |
|---|---|---|
| 20 | Compiler Design | Yes |
| 21 | Computer Graphics | Yes |
| 22 | Cybersecurity Fundamentals | Yes |
| 23 | Cloud Computing | Yes |
| 24 | Mobile Application Development | Yes |

---

## 4. Equivalency Algorithm

### Overview

The equivalency system uses a **hand-coded static mapping** — one lookup table per target program. Only courses marked `passed: true` are included in the equivalency table. Courses marked `passed: false` are completely excluded.

### Algorithm Steps

```
1. Collect all courses where passed === true
2. Look up each course name in the target program's equivalency map
3. If a match exists → output the equivalent course name
4. If no match exists → output "N/A"
5. Display the full result table with a summary (found / not found)
```

### Mapping Logic and Rules

The following principles govern every mapping decision:

**Rule 1 — Shared mathematics is always equivalent.**
Calculus I, Calculus II, Linear Algebra, and Probability & Statistics appear in every engineering curriculum. These are mapped directly (possibly under a different local title such as "Engineering Mathematics I" in Civil Engineering).

**Rule 2 — Core CS foundations map to CE and EEE, not to Civil.**
Courses like Programming Fundamentals, Discrete Mathematics, and Computer Architecture are relevant to both Computer Engineering and Electrical & Electronics Engineering, where programming and digital systems are required. They are marked N/A for Civil Engineering.

**Rule 3 — Highly CS-specific courses are N/A in non-CS programs.**
Object-Oriented Programming, Database Systems, Web Development, Machine Learning, Cloud Computing, and Mobile Application Development have no standard equivalent in Civil or Electrical programs and are mapped to N/A.

**Rule 4 — Overlap in systems-level courses.**
Computer Architecture maps to "Digital Systems Design" in EEE and to "Computer Organization and Architecture" in CE, because these disciplines study the same hardware-software boundary from slightly different angles.

**Rule 5 — Communication and writing courses transfer universally.**
Academic Writing and Research Skills maps to "Technical Communication" or "Technical Writing" in all three programs, since written and oral communication skills are a graduation requirement everywhere.

**Rule 6 — Security and graphics have partial overlap.**
Cybersecurity Fundamentals maps to "Network Security" (CE) or "Information Security" (EEE). Computer Graphics maps to "Computer Graphics" in CE and "Engineering Drawing and CAD" in Civil, because both involve spatial/visual computation even if the tools differ.

### Example Mapping Table (Computer Engineering)

| CS Course | CE Equivalent |
|---|---|
| Calculus I | Calculus I |
| Discrete Mathematics | Discrete Mathematics |
| Programming Fundamentals | Introduction to Programming |
| Computer Architecture | Computer Organization and Architecture |
| Cybersecurity Fundamentals | Network Security |
| Database Systems | N/A |
| Web Development | N/A |

---

## 5. Supported Programs

The application assumes the university offers exactly 4 programs:

- Computer Science *(current — not shown in dropdown)*
- Computer Engineering
- Civil Engineering
- Electrical and Electronics Engineering

---

## 6. How to Use the Application

1. Open `transfer_planner.html` in any modern browser — no server required.
2. The course history table loads automatically on page open.
3. Use the **Target Program** dropdown to select one of the three available transfer destinations.
4. Click the **Transfer →** button.
5. The **equivalency table** appears below, showing:
   - Every passed course from your CS program
   - Its counterpart in the chosen program, or **N/A** if none exists
   - A summary showing how many courses transferred successfully

---

## 7. Improvement Plan: AI-Powered Syllabus Comparison

> This chapter describes a future enhancement that replaces the hand-coded equivalency maps with an automated, AI-driven pipeline that reads actual course syllabi and produces equivalency tables without manual work.

### 7.1 Motivation

The current algorithm is maintained by hand. Every time the university updates a syllabus, or a new program is introduced, a developer must manually revise the mapping tables. This does not scale and introduces human bias. An AI-powered approach would:

- Eliminate manual mapping effort
- Produce richer, evidence-backed equivalency decisions
- Handle partial equivalences (e.g., "70% overlap — conditional credit")
- Stay current as syllabi change

---

### 7.2 System Architecture

```
┌─────────────────────┐
│   Syllabus Sources  │  (university portal, PDF uploads, URLs)
└────────┬────────────┘
         │ raw documents
         ▼
┌─────────────────────┐
│  Document Ingestion │  Parse PDF/DOCX → extract plain text
│  & Chunking Layer   │  Split by section: objectives, topics, outcomes
└────────┬────────────┘
         │ structured chunks
         ▼
┌─────────────────────┐
│  Embedding Engine   │  Convert each syllabus section into a
│  (e.g. text-embed.) │  dense vector representation
└────────┬────────────┘
         │ vector store
         ▼
┌─────────────────────┐
│  Similarity Search  │  For each source course, retrieve the top-k
│  (cosine distance)  │  most similar courses in the target program
└────────┬────────────┘
         │ candidate pairs
         ▼
┌─────────────────────┐
│  LLM Judgment Layer │  Claude / GPT reads both syllabi side by side
│                     │  and decides: Full / Partial / No equivalent
└────────┬────────────┘
         │ structured JSON
         ▼
┌─────────────────────┐
│  Output: Equivalency│  Table with confidence score and reasoning
│  Table + Report     │  per mapping decision
└─────────────────────┘
```

---

### 7.3 Step-by-Step Algorithm

#### Step 1 — Syllabus Ingestion

For each academic program, collect all course syllabi. These can arrive as:
- PDF files uploaded by the student or registrar
- URLs to the university's online course catalog
- Structured data from an API

Each syllabus is parsed and split into semantic sections:

```
{
  "course_code": "CS301",
  "course_name": "Data Structures and Algorithms",
  "credit_hours": 3,
  "learning_objectives": ["..."],
  "weekly_topics": ["Week 1: Arrays and Linked Lists", ...],
  "prerequisites": ["CS201"],
  "assessment_methods": ["Midterm", "Final Exam", "Labs"]
}
```

#### Step 2 — Vector Embedding

Each structured section (especially `learning_objectives` and `weekly_topics`) is passed through a text embedding model. This transforms the text into a numerical vector that captures its semantic meaning.

Two syllabi about linked lists and memory management will produce vectors that are *close together* in the vector space — even if the exact wording differs (e.g., one says "dynamic data structures", the other says "heap-allocated containers").

#### Step 3 — Candidate Pair Generation

For each source course, compute the cosine similarity between its embedding vector and every target-program course's embedding vector. Keep the top 3 candidates.

```
Source: "Data Structures and Algorithms" (CS)
Candidates ranked by similarity:
  1. "Data Structures" (CE)            → similarity: 0.92
  2. "Algorithm Design" (CE)           → similarity: 0.74
  3. "Introduction to Programming" (CE)→ similarity: 0.41
```

Only candidates above a **minimum threshold** (e.g., 0.60) are passed to the next step. Below that threshold, the system immediately outputs N/A without invoking the LLM.

#### Step 4 — LLM Judgment

For every candidate pair above the threshold, a language model (such as Claude) is given both syllabi and asked to reason about equivalence:

```
PROMPT:
You are an academic registrar evaluating course equivalency.
Below are two course syllabi. Decide if Course B is a valid
equivalent of Course A for transfer credit purposes.

Course A (source): [full syllabus text]
Course B (target): [full syllabus text]

Respond in JSON:
{
  "equivalency": "full" | "partial" | "none",
  "confidence": 0.0–1.0,
  "reasoning": "One-sentence explanation",
  "overlap_percentage": 0–100
}
```

The LLM can reason about nuances that pure vector similarity misses — for instance, two courses may share vocabulary but differ in depth, or share topics but require different prerequisites.

#### Step 5 — Output Table

The final output is a rich equivalency table:

| Source Course | Target Equivalent | Equivalency | Overlap | Confidence | Reasoning |
|---|---|---|---|---|---|
| Data Structures & Algorithms | Data Structures | Full | 88% | 0.94 | Both cover arrays, trees, graphs, and sorting algorithms at the same depth |
| Cybersecurity Fundamentals | Network Security | Partial | 55% | 0.78 | Source includes cryptography; target focuses more on network protocols |
| Web Development | N/A | None | 12% | 0.91 | No equivalent course exists in the target program |

---

### 7.4 How AI Adds Value

| Manual Approach | AI-Powered Approach |
|---|---|
| Developer reads syllabi and codes rules by hand | AI reads syllabi automatically |
| Binary: equivalent or not | Graded: full / partial / none + confidence |
| Must be updated manually when syllabi change | Re-runs automatically when new syllabi are uploaded |
| Biased by developer's interpretation | Grounded in the actual learning outcomes and topic lists |
| Scales poorly (O(n²) human effort) | Scales automatically to any number of programs |

The embedding + LLM pipeline mirrors how a human academic advisor would approach the problem — they read both syllabi, look for matching topics and outcomes, and make a judgment call. AI simply does this faster, consistently, and at scale.

---

### 7.5 Implementation Sketch (Pseudocode)

```python
def build_equivalency_table(source_program, target_program):
    source_courses = load_syllabi(source_program)   # list of parsed syllabi
    target_courses = load_syllabi(target_program)

    # Embed all syllabi
    source_vecs = {c.id: embed(c.text) for c in source_courses}
    target_vecs = {c.id: embed(c.text) for c in target_courses}

    results = []

    for src in source_courses:
        # Find top-3 target candidates by cosine similarity
        candidates = top_k_similar(source_vecs[src.id], target_vecs, k=3)

        best = None
        for cand in candidates:
            if cand.similarity < THRESHOLD:
                continue
            # Ask the LLM to judge
            judgment = llm_judge(src.full_text, cand.full_text)
            if judgment.equivalency != "none":
                best = (cand, judgment)
                break

        results.append({
            "source_course":  src.name,
            "target_course":  best[0].name if best else "N/A",
            "equivalency":    best[1].equivalency if best else "none",
            "confidence":     best[1].confidence if best else 0,
            "reasoning":      best[1].reasoning if best else "—",
        })

    return results
```

---

### 7.6 Conclusion

The AI-powered syllabus comparison system replaces brittle, hand-maintained mapping tables with a robust, evidence-based pipeline. By combining **semantic embedding** for fast candidate retrieval and **large language model reasoning** for nuanced judgment, the system can automatically produce accurate, explainable equivalency decisions at scale — and update itself whenever a syllabus changes. This represents the natural next step for the Transfer Planner and would make it genuinely useful across an entire university, not just for a single student's manual lookup.


##by saba gelashvili