# The Unofficial Guide

Name: Ashritha Harish; corpus: campus_life.

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does
The corpus I picked is 'campus_life',I asked questions about campus dining hours, ransit schedules, study spaces in the library, health center walk-in procedures, course assesments. This is a unofficail RAG system built to answer student queries and this refuse to answer out-of scope questions instead of hallucinating.

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->

## Chunking Strategy
Splitting by pragaraphs - '\n\n' becuase most documents in campus life corppus have few short sentences with some new line characters in between.

**Chunk size:**
Paragraph based splitting with a minimum length of 50 characters to filter the headings. Based on the describe() output, chunks range from 72 to 270 characters on average.
The 300–500 character target in criteria.md was aspirational, the corpus paragraphs are shorter than that is measured in Criteria 4.

**Overlap:**
0 overlap characters

By splitting on natural paragraph boundaries (\n\n) with 0 overlap and filtering out small parts (< 50 characters), each chunk can be understood on its own without unnecessary noise or sentence fragmentation.

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: admin_add_drop_deadline.txt#0 `` — produced by: chunker.py::split_documents``

```
You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** — source:course_cs_340_exams.txt#1 `` — produced by:chunker.py::split_documents ``

```
Start the term project in week three, not week eight; everyone learns this the hard way.
```

**Chunk 3** — source:course_phys_130_workload.txt#1 `` — produced by:chunker.py::split_documents ``

```
It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.
```

**Chunk 4** — source: health_center.txt#0 `` — produced by: chunker.py::split_documents``

```
Walk-in hours are 8am to 11am; everything after that is by appointment and appointments run about a week out. If something is urgent, go at 8am and wait rather than booking.
```

**Chunk 5** — source: housing_morrow_house.txt#1  `` — produced by:chunker.py::split_documents ``

```
The good: cheapest housing tier by about $900 a year, and the singles are real singles.
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:**
At what one should go to kestrel commons to get fresh salads?
**Answer:**

```
Based on the documents, to get fresh salads at Kestrel Commons, you should go before 1:30, because the salad bar wilts after that time (found in *dining_kestrel_commons.txt* and *dining_kestrel_commons_followup.txt*).

Sources retrieved: dining_kestrel_commons.txt, dining_kestrel_commons_followup.txt, dining_north_kitchen_followup.txt, housing_fenwick_court.txt

1 model calls this session, 510 tokens (449 in, 61 out)
```

**My relevance cutoff:**

My in-corpus questions had best distances between 0.1257 and 0.5829, while all out-of-scope questions had best distances between 0.8243 and 0.9106. A cutoff of 0.65 sits in between 0.58 to 0.82 gap, allowing campus questions through while stopping irrelevant queries.

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---|
| At what one should go to kestrel commons to get fresh salads? |  Yes | 0.5167   |
| How long will be the wait time for first Counselling session? |  Yes | 0.1257   |
| What is the frequency of transit shuttle on weekdays? |  Yes | 0.5333    |
| Where in library one can find white board to study?|  Yes | 0.5829  |
| How many assessments are there in CS 210 Data Structures? |  Yes | 0.5576   |
| What is the capital of Mongolia? |  No |  0.8641   |
| How do I change the oil in a diesel engine? |  No |  0.9106    |
| Who won the 1994 World Cup? |  No | 0.8736    |
| What is the recommended dosage of ibuprofen for a headache? |  No | 0.8243   |
| How do I write a for loop in Rust? |  No | 0.8313   |

## How I Used AI
<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.**
Tuning the chunker
I asked AI agent how to chunk the campus_life corpus and received answer suggesting 40-character threshold. After inspecting the actual corpus documents, I saw there are longer headings and wanted to avoid breaking them in middle, so I adjusted the threshold to 50 characters.

**2.**
Reviewing the criteria:
I asked AI agent for the feedback on the acceptance criteria in criteria.md and initially I wrote criteria 4 about answer length, but realized it needed to measure chunk properties directly then I updated it to evaluate chunk length bounds (300–500 chars) and absence of empty headings.

**3.**
Choosing and implementing the Milestone 4 improvement:
Once I identified my 3 missed criteria, I asked the AI agent to review my plan to fix the chunking strategy and whether any other failure deserved higher priority. The agent confirmed that chunking was the most contained and measurable fix given the time and complexity of alternatives like hybrid search. It then helped me implement the paragraph merging strategy with a buffer. I spotted a bug in the generated code: it was saving 'piece' instead of 'buffer' to the chunk and I fixed that before running the evaluation.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 4/5 | 4/5 | 4/5 | MET |
| 2. Every answer names a source | 5 of 5 | 4/5 | 3/5 | 4/5 | MISSED |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks between 300-500 chars with no empty header| 3 of 5 | 0/5 | 0/5 | 0/5 | MISSED |
| 5. Answer includes keyword from expects without contradiction | 4 of 5 | 4/5 | 3/5 | 4/5 | MISSED |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

**Criterion 1: Retrieved chunk contains the answer**
- Produced by: `store.py::search`
- Question: "At what one should go to kestrel commons to get fresh salads?"
- Retrieved Sources: `dining_kestrel_commons.txt`, `dining_kestrel_commons_followup.txt`, `dining_north_kitchen_followup.txt`, `housing_fenwick_court.txt` (Best distance: 0.5167)
- Chunk text: Based on the documents, to avoid the salad bar wilting after 1:30, you should go before 1:30 (dining_kestrel_commons.txt)


**Criterion 2: Every answer names a source**
- Produced by: `generate.py::answer_from_chunks`
- Question: "How long will be the wait time for first Counselling session?" (Run 1)
- Answer text: The wait time for a first counselling session is usually three or four days. 

Source: health_center.txt


**Criterion 3: Gate stops out-of-corpus questions**
- Produced by: `gate.py::check_relevance`
- Cutoff: 0.65
- Question: "What is the capital of Mongolia?"
- Distance: 0.864 (> 0.65)
- Gate output: refused


**Criterion 4: Sampled chunks between 300 to 500 chars with no empty header**
- Produced by: `chunker.py::split_documents`
- Sample chunk (Chunk 1 from admin_add_drop_deadline.txt#0 — length 270 characters):"You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other."


**Criterion 5: Answer includes keyword from expects without contradiction**
- Produced by: `generate.py::answer_from_chunks`
- Question: "What is the frequency of transit shuttle on weekdays?"
- Answer texts: The transit shuttle runs a loop every 20 minutes on weekdays (from transit_shuttle.txt).


## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunk contains the answer (Target: 4 of 5)  | MET | Across all three runs, 4 of 5 questions consistently retrieved chunks containing the factual answer (only the CS 210 question failed retrieval). |
| 2 | Every answer names a source (Target: 5 of 5)  |  MISSED | The target was 5 of 5, but our runs achieved 4/5, 3/5, and 4/5 because whenever the model lacked enough context (like CS 210), it gave a refusal without citing any source document. |
| 3 | Gate stops out-of-corpus questions (Target: 4 of 5) | MET | The gate successfully rejected all 5 out-of-scope questions on every run, with distances ranging between 0.824 and 0.911, cleanly above the 0.65 cutoff. |
| 4 | Sampled chunks between 300-500 chars with no empty header (Target: 3 of 5) |  MISSED |  All 5 sampled chunks had lengths well below 300 characters (ranging from 86 to 270 chars) because the source corpus documents are brief individual paragraphs. |
| 5 |  Answer includes keyword from expects without contradiction (Target: 4 of 5) | MISSED |  Although runs 1 and 3 got 4 of 5, run 2 dipped to 3 of 5 due to difference in answering the library whiteboard question; because 4 of 5 did not hold across all runs, it is a miss. |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

Criterion 2 — Every answer names a source 
Stage: Generation (generate.py::answer_from_chunks)
Reason: When the retrieval stage failed to pull the relevant chunk (specifically for the CS 210 question), the model returned a refusal with the message "I do not have enough information" without citing any source document. The source citation only appears when the model actively uses a chunk. A refusal produces no source line, so this criteria failed.

Criterion 4 — Chunks between 300–500 chars
Stage: Chunking (chunker.py::split_documents)
Reason:The chunker splits on paragraph breaks (`\n\n`) and discards paragraphs under 50 characters. The campus_life corpus is written as short posts and most source files contain a single paragraph of 80–270 characters. Because the source text itself is shorter than 300 characters, no paragraph-based chunking strategy can produce chunks in the 300–500 character range without merging paragraphs across topic boundaries.

Criterion 5 — Answer includes keyword from expects
Stage: Retrieval (store.py::search)
Reason: The CS 210 question ("How many assessments are there in CS 210 Data Structures?") retrieved chunks from biology, economics, English, and physics, never from `course_cs_210_exams.txt`. The embedding for the question matched general course structure documents rather than the specific CS 210 file. Without the right chunk, generation had no fact to include and produced a refusal, missing the expected keyword "two midterms and one final". The Q4 whiteboard question also failed in run 2 because the corpus says "Rooms 210 and 211" with no mention of the library, causing the model to refuse rather than name the location.

**Pattern across misses:** 2 out of 3 missed criteria trace back to the same root cause, the CS 210 question was never retrieved correctly. The embedding for that question matched general course documents rather than the specific CS 210 file. Fixing retrieval for that one question would most likely repair both criteria 2 and 5.


## The Improvement

**What I changed:**
I updated `chunker.py::split_documents` to buffer and merge consecutive short paragraphs from the same document until they reach `CHUNK_TARGET_MIN = 300` characters (configured in `config.py`) instead of splitting every paragraph into its own chunk.

**Why I picked it:**
My diagnosis is for Criteria 4, that splitting on individual paragraphs caused all chunks to fall below 300–500 character target because most corpus files contain very short paragraphs

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 3/5 | 3/5 | 3/5 | MISSED |
| 2. Every answer names a source | 5 of 5 | 3/5 | 3/5 | 3/5 | MISSED |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks between 300-500 chars with no empty header| 3 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Answer includes keyword from expects without contradiction | 4 of 5 | 3/5 | 3/5 | 3/5 | MISSED |

**Did it help?**
It helped Criteria 4 by turning it from a miss (0/5) into MET (5/5) by merging short text into properly sized chunks. But merging paragraphs slightly degraded semantic retrieval for Question 3 (transit shuttle), because combining distinct topics diluted the transit keywords, causing Q3 to miss retrieval and lowering Criterion 1 and 5 from 4/5 down to 3/5.
<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

**Criterion 1 & 5 — Missing expected facts for Question 3 (transit shuttle) and Question 5 (CS 210 assessments)**
- **What is broken:** Retrieval missed the exact document for CS 210 (`course_cs_210_exams.txt`) and Transit shuttle (`transit_shuttle.txt`), causing generation to output refusals.
- **What I would do about it:** Increase `TOP_K` to 8–10 or hybrid search which might help with queries containing exact terms and codes like "CS 210" or "transit shuttle".
- **Why I stopped here:** Milestone 4 asked for one improvement. Changing chunking resolved criteria 4, and implementing hybrid search might become separate pipeline change by itself.
**Criterion 2 — Source citation omitted on model refusals**
- **What is broken:** When retrieval fails to return relevant chunks, the model refuses to answer ("I do not have enough information") and does not print any source line.
- **What I would do about it:** Update `generate.py` prompt or post processing to cite the closest retrieved sources.
- **Why I stopped here:** This is the downstream of retrieval misses in Question 3 and Question 5, fixing retrieval is the higher priority root cause.
<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently
Knowing what I know now, I would write **Criteria 4** differently:
- **Original Criteria:** "Sampled chunks between 300-500 chars with no empty header"
- **Revised Criteria:** "Sampled chunks contain 1–2 complete paragraphs (average length 150–350 chars) without splitting sentences across chunk boundaries."
- **Why:** The original character target (300–500 chars) did not fit this corpus. For a short-post corpus like `campus_life`, by forcing chunks into a 300+ character window by merging paragraphs diluted distinct topics and hurt retrieval precision. Checking that chunks keep complete thoughts together is more useful than counting characters.
<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
