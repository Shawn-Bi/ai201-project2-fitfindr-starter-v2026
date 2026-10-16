# FitFindr

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, every
> command, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> python app.py listings --full -n 6      # read the data (Milestone 1)
> python app.py fields                    # what you can filter on
> python app.py ask 'vintage graphic tee under $30'
> ```
>
> All three tools are stubs, so that last command will do nothing useful yet.
> That's the starting position.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     This is your submission. Fill each section in as you finish the milestone
     it belongs to — don't leave it all to the end.

     Unit 3 asks for the first five sections. Unit 4 adds the five below them.
     Leave the unit 4 sections alone until then; they're here so you know
     what's coming.

     Everything is pasted as TEXT. No screenshots, no images, no video links.
     A typed block of output gets full credit; a picture of the same output
     gets none.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 3 — THE BUILD ═══════════════════════ -->

## What This Does

A user types what they want, like "vintage graphic tee under $30". FitFindr searches thrift listings, picks the best match, suggests outfits using the user's wardrobe, and writes a short caption they could post. If nothing matches, it stops and tells them what to change.



---

## Tool Inventory

### `search_listings`

- **What it does:** Finds listings that match a description, with optional size and price filters.
- **Inputs:** `description` (str), `size` (str or None), `max_price` (float or None)
- **Returns:** list of listing dicts (id, title, price, size, platform, ...), best match first
- **When it has nothing:** returns an empty list `[]`, not None or an error
- **Size rule:** the listing size is split into words (on spaces, "/", "(", ")"), and it matches if the user's size equals one of those words, case-insensitive. "M" matches "S/M" but "S" does not match "US 9".

### `suggest_outfit`

- **What it does:** Suggests 1-2 outfits pairing the new item with the user's wardrobe.
- **Inputs:** `new_item` (dict), `wardrobe` (dict with an 'items' list)
- **Returns:** non-empty str of outfit suggestions naming owned pieces
- **When it has nothing:** empty wardrobe returns general styling advice, never "" or an error

### `create_fit_card`

- **What it does:** Writes a short, postable caption about the find.
- **Inputs:** `outfit` (str), `new_item` (dict)
- **Returns:** str, 2-4 sentences, mentions item, price, platform once each
- **When it has nothing:** empty outfit returns a descriptive message instead of raising

---

## Planning Loop




**Branch rule:** If search_listings returns an empty list, put a message in the session naming what to change and stop. Otherwise take the first result and go to suggest_outfit.

**Where it lives:** `agent.py::run_agent`

**How the query is parsed:** regex. It pulls the price from "$30" and the size from "size M"; the rest of the text becomes the description.

**What moves through the session:** parsed → search_results → selected_item → outfit_suggestion → fit_card (or error, if search is empty).

---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```
$ python app.py ask 'vintage graphic tee under $30'

  Found:    Y2K Baby Tee — Butterfly Print — $18.0 on depop

  Outfit:   Pair the baby tee with your baggy dark-wash straight-leg jeans and chunky white sneakers for an effortless, authentic Y2K look. Layer the black cropped zip hoodie on top and add your black crossbody bag to complete the casual, nostalgic outfit.

  Fit card: Still not over how cute this butterfly Y2K baby tee is, and it's up on my Depop right now for just $18! Honestly just picturing this with baggy dark-wash straight-leg jeans, chunky white sneakers, and a black cropped zip hoodie for the ultimate effortless, nostalgic fit. Grab it before I change my mind and keep it for myself! ✨🦋

2 model calls this session, 300 prompt + 129 output tokens
```

**Empty search (the branch)**

```
$ python app.py ask 'designer ballgown size XXS under $5'

  No listings matched 'designer ballgown' in size XXS under $5. Try broader keywords, a different size, or a higher price limit.

0 model calls this session
```

**The three tools, tested one at a time**

```
$ python -c "from tools import search_listings; print(search_listings('graphic tee', max_price=30))"
[{'id': 'lst_002', 'title': 'Y2K Baby Tee — Butterfly Print', 'description': 'Super cute early 2000s baby tee with butterfly graphic. Fitted crop length. Tag says medium but fits like a small.', 'category': 'tops', 'style_tags': ['y2k', 'vintage', 'graphic tee', 'cottagecore'], 'size': 'S/M', 'condition': 'excellent', 'price': 18.0, 'colors': ['white', 'pink', 'purple'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_006', 'title': 'Graphic Tee — 2003 Tour Bootleg Style', 'description': 'Vintage-style bootleg tee with faded graphic. Slightly boxy fit. 100% cotton, soft and worn-in.', 'category': 'tops', 'style_tags': ['graphic tee', 'vintage', 'grunge', 'streetwear', 'band tee'], 'size': 'L', 'condition': 'good', 'price': 24.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_017', 'title': 'Mesh Long-Sleeve Top — Black', 'description': 'Sheer black mesh long-sleeve. Great for layering under a graphic tee or over a bralette. Stretchy material, fits true to size.', 'category': 'tops', 'style_tags': ['y2k', 'grunge', 'goth', 'layering'], 'size': 'S/M', 'condition': 'excellent', 'price': 15.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_033', 'title': 'Vintage Band Tee — Faded Grey', 'description': 'Faded grey band-style tee with distressed graphic. Crew neck. Fits boxy. Well-loved but no holes or major damage.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'band tee', 'graphic tee', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 19.0, 'colors': ['grey', 'charcoal'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_011', 'title': 'Low-Rise Cargo Pants — Khaki', 'description': 'Y2K era low-rise cargo pants. Lots of pockets. Khaki color, slightly distressed at the hems. Great for layering with a long tee.', 'category': 'bottoms', 'style_tags': ['y2k', 'cargo', '2000s', 'streetwear'], 'size': 'W29', 'condition': 'fair', 'price': 27.0, 'colors': ['khaki', 'tan'], 'brand': None, 'platform': 'poshmark'}, {'id': 'lst_012', 'title': 'Oversized Crewneck Sweatshirt — Vintage Navy', 'description': 'Perfectly faded navy crewneck. Genuinely vintage — not manufactured distressed. Ribbed cuffs and hem. No graphics, clean.', 'category': 'tops', 'style_tags': ['vintage', 'basics', 'oversized', 'classic'], 'size': 'XL (fits oversized)', 'condition': 'good', 'price': 20.0, 'colors': ['navy'], 'brand': None, 'platform': 'thredUp'}, {'id': 'lst_015', 'title': 'Vintage Graphic Hoodie — Faded Black', 'description': 'Faded black pullover hoodie with barely-visible vintage graphic on the chest. Cozy interior. Some pilling but adds to the worn-in look.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'graphic', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 26.0, 'colors': ['black', 'charcoal'], 'brand': None, 'platform': 'depop'}]
```

```
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_empty_wardrobe, load_listings; print(suggest_outfit(load_listings()[0], get_empty_wardrobe()))"
Pair these vintage Levi's 501s with a crisp white t-shirt, a classic leather belt, and black ankle boots for an effortlessly cool, timeless look. Alternatively, layer an oversized flannel shirt or a vintage hoodie over a graphic tee and finish with chunky sneakers for a relaxed, streetwear-inspired outfit.
```

```
$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('jeans and a white tee', load_listings()[0]))"
Literally living in this medium wash vibe all autumn long. Found these classic vintage Levi's 501s and they just fit like a dream with a simple white tee. Grab them on my Depop right now for $38 before I change my mind and keep them.
```

---
## How I Used AI

**Moment 1**
- *What I asked for:* I asked Claude to draft Tool Inventory specs for suggest_outfit and create_fit_card.
- *What came back:* It guessed the inputs without seeing my code, using wardrobe as a list and a 1-3 sentence caption.
- *What I changed:* I checked tools.py and changed wardrobe from list to dict, and caption length to 2-4 sentences.

**Moment 2**

- *What I asked for:* I asked Claude to help write the code for the three tools and run_agent, then asked why my fit cards came out identical.
- *What came back:* Working code, and it said identical captions came from either the cache or TEMPERATURE = 0.
- *What I changed:* I found TEMPERATURE was already 0.9, so I set AI201_CACHE=0, confirmed the captions changed, then turned the cache back on.

<!-- ═══════════════════════ UNIT 4 — THE TEST ═══════════════════════

     Don't fill these in during unit 3.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Run Log — Before

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1. Matching query completes all three tools | 4 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 2. Impossible query stops before tool 2 | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 3. Item in session matches item passed on | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 4. Fit card mentions item, price, platform | 4 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5, revised reading) |
| 5. Empty wardrobe returns advice | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |

Produced by `run_eval.py::main` → `results/run_2026-10-16_0102_before.md`. Caching off, temperature 0.9.

**Criterion 1, try 1** (`vintage graphic tee under $30`):
```
Fit card: Obsessed is an understatement—just scored this butterfly Y2K baby tee on Depop for only $18 and it is giving total 2000s mall-goth realness. Throw it on with some baggy dark-wash straight-leg jeans, chunky white sneakers, and a cropped zip hoodie for the ultimate effortless fit. Snag it before I change my mind and keep it for myself! 🦋✨
```

**Criterion 2, try 1** (`designer ballgown size XXS under $5`):
```
stopped early: yes — No listings matched 'designer ballgown' in size XXS under $5. Try broader keywords, a different size, or a higher price limit.
[2] search_listings (via MCP) → out: 0 listings
```

**Criterion 3, try 1** (`flannel shirt under $40`):
```
selected_item: Oversized Flannel Shirt — Plaid Red/Black ($22.0, thredUp)
[3] suggest_outfit
      in:  Oversized Flannel Shirt — Plaid Red/Black
```

**Criterion 4, try 1** (`corduroy pants under $40`, item: Corduroy Wide-Leg Pants — Rust, $32, depop):
```
obsessed is an understatement for these rust corduroy wide-leg pants. 🍂 I'm styling them with a white ribbed tank, black combat boots, and a cropped zip hoodie for the ultimate 70s grunge-meets-earthy vibe. Grab them on my Depop right now for just $32 before I change my mind and keep them!
```

**Criterion 5, try 1** (`denim jacket under $50`, empty wardrobe):
```
Outfit: Pair this cropped denim jacket with high-waisted black trousers and a fitted white tank top for an effortless, classic look. Add chunky sneakers or leather boots to tie the streetwear vibe together seamlessly.
```

---

## Verdicts and Diagnoses

<!-- MET or MISSED per criterion against LAST UNIT's target, plus a sentence on
     how you decided.

     Then, for every miss: which of the four places it happened — a tool, the
     loop's branch, the session, or the model's output — AND the mechanism.

     Not a diagnosis:  "The fit card was bad."
     A diagnosis:      "The fit card criterion missed on 2 of 5 items. Both had
                        an empty brand field. My prompt puts the brand in the
                        first sentence, so the card opened with a blank and read
                        like a fragment. The tool worked; the prompt assumed a
                        field that isn't always there."

     Look for a pattern. Three misses on the same tool is one problem, not
     three. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**



---

## Loop Trace

```
$ python app.py ask 'vintage graphic tee under $30' --trace
[1] parse_query
      in:  vintage graphic tee under $30
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 10 listings
[3] suggest_outfit
      in:  Y2K Baby Tee — Butterfly Print
      out: Pair the baby tee with your baggy dark-wash straight-leg jeans and chunky white sneakers for an effortless, au…
[4] create_fit_card
      in:  Y2K Baby Tee — Butterfly Print
      out: Still not over how cute this butterfly Y2K baby tee is, and it's up on my Depop right now for just $18! Honest…
```

**Failure modes, triggered on purpose**

- Empty search (`'designer ballgown size XXS under $5'`): "No listings matched 'designer ballgown' in size XXS under $5. Try broader keywords, a different size, or a higher price limit." Stops before suggest_outfit, 0 model calls.
- Empty wardrobe (`--empty-wardrobe`): returned general styling advice (pleated tennis skirt, platforms, wide-leg denim), no crash, not empty.
- Model unavailable (one character of the key changed): "ModelUnavailable: The model rejected your API key. Check GEMINI_API_KEY in your .env file, or create a fresh key at aistudio.google.com." No stack trace. This message is clear to a developer but not to an end user.

**Happy path**

```

```

**Empty search**

```

```

**On the MCP move:** <!-- what changed in your code, and whether anything
behaved differently afterwards. If the rewire didn't work, say exactly where it
broke — the error text and the last thing that worked. That earns the point in
full. -->



---

## The Improvement

<!-- What you changed, why your diagnosis pointed at it, and the after-run in
     the same table format. One change, measured properly.

     `python run_eval.py --label after` -->

**What I changed:**

**Which failure it was meant to fix:**

### Run Log — After

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |  |  |
| 2.  |  |  |  |  |  |  |  |
| 3.  |  |  |  |  |  |  |  |
| 4.  |  |  |  |  |  |  |  |
| 5.  |  |  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it made things worse, say that. Honestly reported, that earns full
     credit and is more interesting than one that worked. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped where
     you did. "I ran out of time" is fine if it's true. Pretending nothing is
     left is not. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 3

       [ ] criteria.md has five numbered criteria, each with a target
       [ ] Each criterion has a reason underneath it
       [ ] All five unit 3 sections above have real content
       [ ] Tool Inventory: all three tools, inputs WITH TYPES, a specific
           return value, and the empty case
       [ ] Planning Loop names the branch rule and agent.py::run_agent
       [ ] Sample Run: one full query plus the three per-tool tests, as text
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN, you submit the same one
           next unit

     SUBMISSION CHECKLIST — unit 4

       [ ] mcp_server.py exists with one tool registered
           (or a written record of exactly where the rewire broke)
       [ ] Run Log — Before, five criteria, five tries each
       [ ] Real output pasted underneath, naming file and function
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, naming a place AND a mechanism
       [ ] Loop Trace, with the MCP call visible in it
       [ ] All three failure modes triggered and handled
       [ ] One improvement, with Run Log — After in the same format
       [ ] What's Still Broken
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository. Your commit history is what
     shows your criteria existed before your results did.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
Listing fields: title, price, size, category, style_tags, colors, platform