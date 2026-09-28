# The Unofficial Guide

Ismah Hassan - Campus Life

---

# Unit 1

## What This Does
This is a RAG system for campus life. The system answers questions like "When is the last day to drop a class?", "How to declare your major?", and other campus related questions. The answers are grounded in the relevant documents that are retrieved.

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->

## Chunking Strategy

**Chunk size: 250**
**Overlap: 50**

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

My documents run around 200 characters each, so a chunk size of 250 means most documents fit inside a single chunk without being split or cut off without finishing a thought. The 50
overlap is there just in case there's a longer document.


## Sample Chunks

======================================================================
Chunk 1  |  source: thread_bike_commute.txt#0  |  produced by: chunker.py::fallback_split
======================================================================
THREAD: Is a bike worth it for a 20 minute walk commute?

--- reply 1 (14 votes) ---
Yeah. Cuts an 18 minute walk to about 6. The thing nobody mentions is storage — covered bike parking exists at three buildings and is full by 9am at all three.

---

======================================================================
Chunk 2  |  source: thread_first_gen.txt#1  |  produced by: chunker.py::fallback_split
======================================================================
or it by name.

--- reply 2 (41 votes) ---
The thing I'd say: the unwritten rules are the hard part, not the coursework. Ask about the unwritten rules explicitly. People are happy to explain them and nobody volunteers them.

--- reply 3 (16 votes) --

======================================================================
Chunk 3  |  source: thread_laptop_specs.txt#2  |  produced by: chunker.py::fallback_split
======================================================================
years on an 8GB machine and it was fine until the last project, at which point it very much wasn't. 16 is the answer.

======================================================================
Chunk 4  |  source: thread_parking.txt#0  |  produced by: chunker.py::fallback_split
======================================================================
THREAD: Worth getting a parking permit?

--- reply 1 (15 votes) ---
West lots sell out in about three days in August. East lot never sells out but it's a 12 minute walk, at which point you might as well have parked on the street.

--- reply 2 (21 vot

======================================================================
Chunk 5  |  source: thread_roommate_conflict.txt#2  |  produced by: chunker.py::fallback_split
======================================================================
ous cases.

--- reply 3 (33 votes) ---
Write down specifics before the meeting. 'It's not working' is hard to act on; 'guests four nights a week past 2am' is not.

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** When is the last day to drop a class without it being on my record?

**Answer:** Based on the provided document, the last day to drop a course
without it showing on your transcript is the end of the second week (drops
after week two show as a W).

```
```

**My relevance cutoff:**

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---|
| Can I change my major if I'm a junior? | Yes | 0.595 |
| When can I apply for graduation? | Yes | 0.526 |
| Where are dining dollars accepted? | Yes | 0.380 |
| When is the last day to drop a class without it being on my record? | Yes | 0.392 |
| What are the meal plans available? | Yes | 0.517 |

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.** I used Claude to check my 2 criteria and see if they could be seen plainly or observed. I wrote "comprehensible on its own," for my 5th point, but Claude said it was too vague. I changed it to whether the chunk can be comprehensible on its own without other chunks' contexts.

**2.** I used Claude to check if the cutoff wasn't working for my questions, but it clarified that the cutoff is for questions that are unrelated, not questions that are related but not answerable with relevant documents. So I kept the cutoff value the same because it worked for me.

**3.** Every question came back "fail" on my first run. I asked Claude why, and it pointed at my scorer. I found that four of my five expects said "no information," so I was grading refusals. I replaced them with questions the documents answer.

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
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. At least 3 chunks are relevant by themselves | 5 of 5 | 3/5 | 3/5 | 3/5 | MISSED |
| 5. Answers are grounded | 4 of 5 | 4/5 | 4/5 | 5/5 | MET |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

### Criterion 1: Retrieved chunk contains the answer

Question: "How many credit hours do I need to graduate?" (run 1, best distance 0.3445)
Produced by `store.py::search`, over chunks from `chunker.py::split_documents`.

Sources retrieved: admin_graduation_requirements.txt, admin_pass_fail_option.txt, admin_transcript_requests.txt, advising_registration.txt

The chunk that contained the answer, from `admin_graduation_requirements.txt`:

```
On the graduation requirements

120 credit hours, a completed major, and the general education requirements. The one that trips people is the writing-intensive requirement: two courses, and they must be taken in different departments. Check this in y
```

### Criterion 2: Every answer names a source

Question: "Does the health center take walk-ins? What time?" (run 1)
Produced by `run_eval.py::main`, using `generate.py::answer_from_chunks`. File: `results/run_2026-09-27_2019_after.md`.

```
Yes, the health centre takes walk-ins from 8am to 11am (from health_center.txt).
```

### Criterion 3: The gate stops out-of-corpus questions

Produced by `run_eval.py::check_out_of_scope`, cutoff 0.6. Refused 5 of 5.

| Out-of-scope question | Best distance | Gate |
|---|---|---|
| What is the capital of Mongolia? | 0.825 | refused |
| How do I change the oil in a diesel engine? | 0.815 | refused |
| Who won the 1994 World Cup? | 0.845 | refused |
| What is the recommended dosage of ibuprofen for a headache? | 0.832 | refused |
| How do I write a for loop in Rust? | 0.854 | refused |

### Criterion 4: Chunks are comprehensible on their own

Produced by `chunker.py::split_documents`, printed with `python app.py ask "..." --show-prompt`.
Chunks that were comprehensible, per question: 2/5, 3/5, 5/5, 4/5, 2/5. That is 3 of 5 questions with at least 3 comprehensible chunks.

A chunk that passes (`health_center.txt`):

```
The health centre

Walk-in hours are 8am to 11am; everything after that is by appointment and appointments run about a week out. If something is urgent, go at 8am and wait rather than booking.

Counselling is separate, in the same building, and has i
```

A chunk that fails (`dining_verrill_street_grill_followup.txt`). It opens mid-word, so "it's a different building" has nothing to refer to:

```
sses, go before 11:45 and it's a different building entirely.

Also worth saying: one register, so the queue is a single line no matter how busy. Nobody tells you this at orientation.
```

### Criterion 5: Answers are grounded

Question: "When is the last day to drop a class without it being on my record?"
Answers produced by `generate.py::answer_from_chunks`, via `run_eval.py::main`. File: `results/run_2026-09-27_2019_after.md`.

The cited chunk (`admin_add_drop_deadline.txt`):

```
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says th
```

Run 1 answer. It does not pass, because the chunk says the drop window runs through week six:

```
You can drop a course through the end of the second week; drops after week two will show as a W on your transcript. This information comes from `admin_add_drop_deadline.txt`.
```

Run 3 answer. It passes:

```
The last day to drop a class without a "W" appearing on your transcript is through the end of the second week, as drops after week two show as a W.

Source: admin_add_drop_deadline.txt
```

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunk contains the answer | MET | for each run, out of all the chunks at least one contained the answer |
| 2 | Every answer names a source | MET | every single run for every question named a source |
| 3 | Gate stops out-of-corpus questions | MET | the gate stopped all irrelevant questions for each run |
| 4 | At least 3 chunks are relevant by themselves | MISSED | 3 chunks were comprehensible by themselves for only 3 questions, only 2/5 chunks were comprehensible for the other 2 questions |
| 5 | Answers are grounded | MET | For two runs, 4/5 answers were grounded, and for the last run, 5/5 answers were grounded |
I revised criterion 5 to require 5/5 answers to be grounded because hallucination shouldn't be tolerated, as it's misinformation. With the revision, my criterion would be missed, but it's a better evaluation standard.


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

### Criterion 4: chunks that stand on their own (MISSED, 3 of 5 questions)

**Stage: chunking.** My chunker cuts at a fixed 250 characters with 50 overlap and ignores word and sentence boundaries. Any document longer than 250 characters gets split wherever the count lands, so every chunk after the first in a document opens in the middle of a word or sentence. Examples from my output:

- `dining_verrill_street_grill_followup.txt` opens "sses, go before 11:45 and it's a different building entirely." "It's" has nothing to refer to, and the chunk doesn't say which restaurant it's about.
- `advising_registration.txt` opens "ed by credit hours, same as the housing lottery."
- `admin_pass_fail_option.txt` produced a chunk that is only "C- or better. Two per year, maximum eight across a degree." Two of what is lost.

The first chunk of a document always starts with its title, and those were the ones that passed. My library question got 5 of 5 because all five retrieved chunks were first chunks.

My Milestone 3 reasoning assumed my documents were about 200 characters, so most would fit in one chunk. The graduation, pass/fail, and add/drop documents are longer than 250, and they are the ones that got split.

**Secondary cause, retrieval.** The same results came back for unrelated questions. `admin_transcript_requests.txt` and `advising_registration.txt` showed up for both the credit hours and study abroad questions, and dining chunks filled the health center results. When only one or two documents really match a question, the remaining three or four get filled with whatever is nearest. The gate only looks at the best distance. 

**Pattern:** this is one problem, the chunker cuts without regard for word boundaries. The answers were still correct on every question because the chunk that mattered was always a clean first chunk.

### Criterion 5: grounded answers (drop-deadline question)

**Stage: generation.** In runs 1 and 2 of "When is the last day to drop a class without it being on my record?", the model wrote that you can drop through the end of the second week. The retrieved chunk from `admin_add_drop_deadline.txt` says the add deadline is the end of week two, that dropping runs through week six, and that a drop after week two shows as a W. The model combined the add deadline and the drop window under one category. Its final conclusion (week two is the last day with no W) was right, but the sentence it used to get there isn't in the source. Run 3 stated it correctly.

Retrieval and chunking weren't the cause. The chunk held all the right information, so this is a model error. It happened on one question, where two similar rules were together.

## The Improvement

**What I changed:** 

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 4/5 | 4/5 | 4/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Chunks are comprehensible on their own | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Answers are grounded | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

My old chunker had 250 characters in every chunk and made 184 chunks, and some were very short. The new one split nothing: 88 documents became 88 chunks, and no chunk stopped or started in the middle of a sentence.
anymore. Criterion 4 went from 3 of 5 questions to 5 of 5. However, criterion 1 dropped from 5/5 to 4/5. The whole study_library_hours.txt document is now one chunk with hours, reading week, and seating. Five 
housing documents are retrieved now, the library document isn't retrieved, so the model says the documents don't cover the question. Before, the first 250 characters of that document were their own
chunk and ranked in the top 5.

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

**The library question still fails: criterion 1** "Does the library have a quiet floor?" failed all three runs after my change. Criterion 1 still shows MET only because my target is 4 of 5. I stopped because the milestone asked for one change measured properly, and my diagnosis was chunking. 


## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->

**Criterion 5.** I would change it to 5 of 5 because hallucination is the failure I care about most, and I'd keep it. 

