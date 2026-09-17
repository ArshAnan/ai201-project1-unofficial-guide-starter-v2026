# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**
The internship date lives in one thread, but the bike-commute question sits
next to a separate commuting thread, and pass/fail is mentioned both in its
own thread and in first-year regrets. I expect retrieval to mix those up
once. 5 of 5 would pretend that overlap isn't there; 3 of 5 would ignore
that most answers sit in a single sentence of a labelled reply.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**
The grounding prompt already tells the model to name the filename given in
each excerpt, and every answer that passes the gate still goes through that
prompt. Four of five would let the model skip the citation on one question
and still count as working. The only way this fails is if the model ignores
an instruction it is given every time, so all five is the right bar.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

<!-- The five questions are the ones in `OUT_OF_SCOPE` at the bottom of
     `questions.py`, and `run_eval.py` puts them through the gate and writes
     what happened into your run log. Swap them for your own if you'd rather —
     just keep five of them, or the "4 of 5" above has nothing to be 4 of. -->

**Why this target:**
The five out-of-scope questions (Mongolia, diesel oil, the World Cup,
ibuprofen, Rust) share almost no vocabulary with these campus threads, so
they should sit far from every chunk. I am not claiming 5 of 5 because I
have not measured distances yet, and a question that happens to share a
common word with a thread could land closer than I expect. 4 of 5 leaves
room for that without treating the gate as optional.

---

## 4. Chunks are complete replies, not leftover tails

Every chunk contains at least one complete reply: a `--- reply` header and
the full reply body, with no sentence or word cut off at either end. I will
check all 26 chunks against this.

**Why this target:**
The starter's 800-character window produced 26 chunks from 23 threads. The
three extras are leftover overlap tails — the bike commute thread fits in
739 characters and still left a 59-character stub starting mid-word
("nd it's the only reason I got mine back after it was taken."). Nobody
could answer a question from that stub without the rest of the thread,
which is the check the chunk printer asks. I am not requiring every thread
to stay one chunk: first-year regrets is already 793 characters and five
replies, so splitting *between* replies is fine. Splitting *inside* a reply
is not.

---

## 5. The named source is a file retrieval actually returned

For all 5 of my test questions, the source file named in the answer is one
of the files that retrieval returned for that question.

**Why this target:**
Criterion 2 only asks that a source is named. That is not enough here:
each thread is a different conversation, and a model that cites
`thread_bike_commute.txt` when retrieval actually returned
`thread_parking.txt` has named a source and still pointed at the wrong
one. The prompt already labels every excerpt `[from filename]`, so naming
a different file means the model ignored the grounding it was given. Four
of five would let that happen once, which is the failure this pipeline is
supposed to catch.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->
