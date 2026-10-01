[devpost-submission.md](https://github.com/user-attachments/files/32933157/devpost-submission.md)
# Not_Just_Searching (AlgoGuard AI)
**Semantic deduplicator and problem safety engine for competitive programming coordinators**

## Tagline
Finds repeated contest problems by what they *do*, not by the words they use.

---

## Inspiration
Every season, AspiHazar's beginners division picks new problems. Coordinators change, and the new one often has no idea what earlier coordinators already assigned. The obvious fix is to search the archive, but search fails here. An "apples in baskets" story and a "prefix sum" problem share almost no words, yet they are the same problem. A keyword search for "sum" finds neither. We wanted a tool that compares the mathematics underneath the story.

## What it does
A coordinator pastes a candidate problem. AlgoGuard AI then:
1. **Parses it.** It names the core archetype (Prefix Sums, Two Pointers, DP, Graphs...), states the time complexity the constraints imply, and pulls out the key equations and limits.
2. **Scores duplication risk (0–100%).** It compares the problem's mechanics with every problem in the repository. Green means original, yellow means partial overlap, red means likely duplicate. Two sliders set where those colours begin.
3. **Explains why.** A side-by-side view shows the shared mechanics, the words that are only story, and the nearest neighbours. It also shows what a keyword search would have scored, to make the gap visible.
4. **Recommends safe replacements.** It suggests unrepeated, verified beginner problems (rating 1200 or lower) that are not too close to the candidate.
5. **Tracks the process.** The dashboard shows problems indexed, duplicates blocked, category spread, coordinator handoffs and an audit log, and it exports a safety report.

## How we built it
Plain HTML5, Tailwind CSS and vanilla JavaScript in a single file, with no backend.

The engine has three steps:
- **Ignore the story.** Each problem is reduced to a short mechanics summary.
- **Turn it into a vector.** We count signals for 10 algorithm families (for example "from l to r" and "cumulative" for prefix sums), 3 intents (count, optimise, decide) and a few input-shape hints. Repeated words grow slowly, so one word cannot dominate.
- **Compare with cosine similarity**, then scale the result down when two problems belong to different algorithm families.

The code is split into five commented sections (data, concepts, engine, views, start), and each result card is its own small function.

## Challenges we ran into
- **Same family, different problem.** Early on, a "count the ways to pay with coins" problem scored 96% against "minimum cost to climb stairs" because both are DP. Adding the count/optimise/decide intent fixed it (now 63%).
- **Wrong limits.** `2·10^5` was being read as `2`. We wrote a proper parser for `2·10^5`, `10^5`, `2e5` and `5,000`.
- **Partial matches looked like duplicates.** A digit-substring problem scored 83% against palindromes because both involve strings. Weighting by how many algorithm families the two problems share brought it to 52%, which shows as yellow.
- **A dialog that would not close.** Tailwind's `flex` class overrode the `hidden` attribute. One CSS rule fixed it.
- We tested the engine on 11 statements and the interface flow on 21 checks, and confirmed the readable rewrite gives identical results to the earlier version.

## Accomplishments we're proud of
- A story-wrapped prefix-sum problem, with only 6% word overlap with the repository, is flagged at 91% as a duplicate of two earlier problems.
- Every score comes with a reason a coordinator can read and challenge.
- It runs from one file, works on mobile, and needs no setup.

## What we learned
- Similarity is not one number. Family, intent and input shape each need their own weight.
- Explaining a flag matters as much as producing it, because coordinators will only trust what they can check.
- Test cases find engine bugs faster than reading the code.

## Honest limitations
- The similarity engine is **rule-based**, not a neural embedding model. It works like a vector search over hand-defined concepts, so it can miss problem types outside the 10 families.
- The repository (16 problems), coordinator names and audit history are **sample data**.

## What's next
1. Replace the rule-based vectors with real text embeddings (for example sentence-transformers behind a small Python API), keeping the current engine as a fallback.
2. Import AspiHazar's real problem storage.
3. Pull candidate replacements from the Codeforces API and filter out anything already in the storage.
4. Store data in a database so several coordinators share one repository and history.
5. Expand to more algorithm families and to intermediate divisions.

---

## Built with
HTML5, Tailwind CSS, JavaScript, Google Fonts (IBM Plex Sans, IBM Plex Mono)

## Try it out
- GitHub: `<your repo link>`
- Live demo: `<your GitHub Pages or Netlify link>`

## 60-second demo script (for a video or live judging)
1. Open the inspector and click **Story-wrapped prefix sums**, then **Analyse problem**.
2. Point at the red gauge and read the reason: high mechanic match, very little shared wording.
3. Show the side-by-side view and the safe beginner alternatives.
4. Click **Ticket counting** to show a second duplicate, and **Digit substrings** to show a yellow partial overlap.
5. Drag the threshold sliders and watch the verdict change.
6. Open **Problem pool** and filter to beginner-safe problems.
7. Open **Dashboard**, then **Export safety report**.

---

## README.md (paste into your GitHub repo)

# AlgoGuard AI

Finds duplicate or near-duplicate contest problems by comparing their mechanics, not their wording.

**Live demo:** `<link>`

## Run it
Open `index.html` in any browser. There is no build step and no server.

## How it works
1. `toVector` counts signals for 10 algorithm families, 3 intents and 4 input shapes.
2. `semanticSimilarity` compares two vectors with cosine similarity, scaled down when the algorithm families differ.
3. `findMatches` ranks every stored problem, and the views explain the top result.

Code layout inside `index.html`: data, concepts, engine, views, start.

## Limits
The engine is rule-based, not a neural model, and the repository is sample data. Real embeddings and AspiHazar's real storage are the next steps.

## Licence
MIT
