# Models and tools Isaac Flath talks about — a plain-language report

For Jash. Written for someone who works on document extraction but doesn't write code or
pick the underlying engines. "Model" means the thing that actually reads a page (or finds
one, or draws a box around text). "Tool" means a product or platform that one or more
models sit inside. I've labelled every tool clearly so you don't mistake a product name
for a model.

## Read this before anything else — how solid is this evidence?

Everything below comes from 177 Instagram posts by Isaac Flath (@isaac_flath), collected
June–November 2026. Four things to hold onto the whole time you read this:

1. **Every test is tiny.** Isaac is showing a demo on one to three pages, not running a
   benchmark. A model "winning" on one page is a lead worth trying on your own documents,
   never proof it will win on yours.
2. **Nobody watched the videos.** What's recorded here is the caption plus an automatic
   description of what was on screen. That description service admits it didn't always
   capture the exact text, numbers, or table cells shown — so some details are closer to
   "roughly this" than "exactly this."
3. **30 of the 177 posts have no description at all** — only the caption survived. A few of
   those are real tests whose numbers now have no second source (a redline comparison, a
   wide formula table, a timetable cost comparison). I've left those out rather than guess.
4. **This is leads, not a leaderboard.** Isaac says this himself: no tool wins on every
   page, two tools agreeing isn't proof either is right, and the pages people bring him to
   test are their hardest ones — scans, blueprints, messy tables — not the cleaner pages
   that public benchmarks tend to use. Treat every "it got this right" below as "worth
   trying on a similar page of yours," not "pick this one."

Through the whole report I mark everything one of two ways:
- **Tested** — Isaac (or a named collaborator) actually ran this model on a page and the
  source describes what happened.
- **Market-list only, not tested here** — the name shows up on a "what exists in the
  market as of November 2026" reference list in the source, written from general industry
  knowledge, with no test behind it anywhere in the 177 posts. I will never describe one of
  these as having "done" anything. If you see a name below with no example page attached to
  it, assume it's this category.

---

## 1. Document-specialist models

These are models built only to read pages — not general chatbots, just readers.

### Chandra — Tested, several times
**What it is:** A model built specifically to read documents, including messy and
handwritten ones.
**What was tested:** A degraded, hard-to-read declassified government cable; a handwritten
edit where a word was crossed out and a replacement written above it; a printed page with a
small typo; a dense government-style grid form; a state poverty-statistics table with two
states' numbers in adjacent cells; a printed tariff-table formula; a handwritten surname
written one letter per box; and a Federal Reserve table with a year printed twice (once as
an annual total, once spread over four quarterly columns).
**What it did well:** Read the cable's first sentence and its routing grid correctly where a
table-rebuilding tool (Docling) mangled it. Kept two states' figures in separate cells where
Docling merged them into one. Kept the minus signs in a printed formula that a cloud OCR
service (Textract) dropped. On the handwritten edit, it returned both the deleted and the
inserted word — most tools miss one of the two entirely.
**What it got wrong:** On that same handwritten edit, it never said *which* word was the
deletion and which was the insertion — so you can see both words but not which one the
document actually says now. It quietly "corrected" a printed typo without flagging that it
had changed anything. It broke badly on the dense grid form. On the one-letter-per-box
surname, it read "Jaferi" where a cloud service read the correct "Jaffri." On the Fed table,
it read every number correctly but put the annual total under the wrong quarter column and
mislabelled two quarters with the wrong year — every value right, every label wrong.
**Evidence level:** Several separate tests across different page types — one of the more
heavily tested models in the source, but still each test is a single page.

### MinerU — Tested once (as a tool, see also "Tools" section below)
MinerU is really a **tool** that uses a model to build a searchable local index of a PDF —
each passage gets an address (document, page, block) so an agent can fetch just that piece
later instead of reloading the whole file. **Tested on:** retrieving one payment clause out
of a 23-page financing agreement (worked), and a table comparing two similarly-named models.
**What it got wrong:** on that comparison table, it merged both model names into a single
cell that spanned both rows, while keeping their error-rate numbers in separate rows — so if
you imported the table as written, both results would get attached to the same model name.
Docling, tested on the same table, kept the two names apart.

### LightOnOCR (version named in the newest test: LightOnOCR-3-4B) — Tested twice
**What it is:** A newly released document-reading model. Isaac says openly he was "still
evaluating it generally" when he posted the first of these — so even by the standards of this
report, this is thin.
**What was tested:** (1) A census table with a cell merged across two columns (one column showed
6.9% for high-growth counties, the other 2.2% for low-growth counties). (2) A handwritten 1924
diary page, on the line "Left New Haven at 7:37 P.M." — this is the test where the version
number LightOnOCR-3-4B is actually named.
**What it did well:** On the census table, returned each number under the correct group, keeping
the merge intact. Docling, tested on the same table, duplicated both numbers into all four cells
and lost which number belonged to which group.
**What the diary test actually shows:** It read the time as "7:37 P.M.", the same as a vendor OCR
API (Datalab), while a layout tool (Surya) read "9:37 P.M." This is **not** a win — the
handwritten digit is genuinely ambiguous (7 or 9), Isaac says so, and his pipeline left the value
unverified rather than siding with the two that agreed. Record it as agreement between two
readers, not as a correct answer.
**Evidence level:** Two pages, one of which has no knowable right answer. Isaac's own framing on
the first was "one good sign," not a verdict.

### Market-list only, not tested here
Mistral OCR, PaddleOCR-VL, GLM-OCR, DeepSeek-OCR, Nanonets OCR, FireRed-OCR — none of these
appear being run on a page anywhere in the 177 posts. They're on the November 2026
market-reference list only.

One partial exception: **PaddleOCR-VL** does get a single mention, but not as a reading
test — it was compared against a layout tool (Datalab Accurate) on whether a bounding box
can follow angled, warped text. That's a test of box *shape*, not reading accuracy, and
PaddleOCR-VL is a different, newer product from classic PaddleOCR (see the "cheap classical
tools" entry below, which is tested much more).

**olmOCR** appears once, but only as an example used to explain *why* low-temperature AI
readers sometimes get stuck repeating themselves — it is named, not actually run as a reader
in any test shown here.

---

## 2. General vision models

These are broad AI models — built to do lots of things, including reading a page when you
point a document at them, rather than built only for documents.

### Gemini (Gemini Flash / Gemini 3.5 Flash) — Tested the most of any model in this report
**What it is:** A general AI model from Google that can look at an image (including a scanned
page) and describe or transcribe what it sees.
**What was tested:** A bank form with a handwritten name and six checkboxes; a Markdown table
with a heading that spans more than one column; a pill bottle's copyright line; a Federal
Reserve table with a doubled year label; a chip datasheet with temperature-dependent voltage
limits.
**What it did well:** It's the *only* reader in the source to get a handwritten name and all
six checkboxes correct on one bank form — and it was also the most expensive option tried on
that page. On the Fed table, it was the one reader that got every heading right, where a
document specialist (Chandra) slid headings out of place. It kept a printed formula's minus
signs intact.
**What it got wrong:** Misread a digit on at least one page. "Tidied up" a printed value
instead of transcribing it exactly. Returned a merged column heading under only one of the
columns it actually spanned in Markdown output — flattening something that needed
to stay merged. Invented a copyright line for the pill bottle that isn't printed on it
anywhere. On the chip datasheet, when asked with a plain "read this page" instruction, it
returned the two voltage limits but left the temperature condition that each one applies
under completely blank — so nothing in the output told you which limit applied. Naming the
condition explicitly in the instructions fixed that specific page.
**Note on versions:** Isaac's newest tests specifically name "Gemini 3.5 Flash." Earlier
posts just say "Gemini Flash" without a version number — don't assume a result from an
earlier post carries over to the newer version.
**Evidence level:** The single most heavily tested model in the whole source, across many
different page types — but still, every one of those is a one-off page test, not a
benchmark run.

### Qwen (Qwen 3 VL) — Tested the second-most of any model
**What it is:** An open-weight general vision model (meaning anyone can download and run it,
unlike Gemini).
**What was tested:** A colour-coded table where grey cells meant "No guidance / Not
applicable"; the transcription step inside a multi-step router test (see LiteParse below);
checkbox and map-label pages are referenced elsewhere in the source as places it was tested.
**What it did well:** When asked for structured records, it returned a cell's colour along
with its contents and correctly mapped a grey cell to the legend's "No guidance / Not
applicable" meaning. A table-rebuilding tool (Docling), tested on the same cell, just
returned it blank — losing the fact that grey meant something.
**What it got wrong:** The source doesn't record a clean specific failure for Qwen the way
it does for Gemini or Chandra — it comes across more consistently positive in what's
captured here, but that's partly because fewer of its failure cases got transcribed, not
proof it fails less.
**Evidence level:** Multiple tests, including one as the "transcription" step of a larger
pipeline test.

### Market-list only, not tested here
**GPT-5**, **Claude Sonnet**, **Gemini 3 Pro**, and **InternVL** are named on the market list
but none of them appear being run on a page anywhere in the 177 posts.

---

## 3. Classic OCR services

Older, established text-reading tools — not built around AI chat, just built to turn a scan
into text.

### AWS Textract — Tested, multiple times
**What it is:** Amazon's cloud OCR service.
**What was tested:** A big, clean table; a page with four signature lines (two clear, two
faint); a tariff table with a printed arithmetic formula; a handwritten surname written one
letter per box.
**What it did well:** Handled the big clean table well. Correctly read the handwritten
surname ("Jaffri") where a document-specialist model (Chandra) misread it as "Jaferi" — a
useful reminder that "cloud services are bad at handwriting" doesn't hold up on every page.
**What it got wrong:** Found only 3 of the 4 signatures on the faint-signature page. Dropped
the plus and minus signs from the tariff formula entirely — it returned "5, 1, and 2" after
the equals sign with no operators, which looks harmless but is a formula you can no longer
reconstruct or tell is broken just by looking at the numbers. A table-rebuilding tool, a
document specialist, and a general image model all kept that same formula intact on the same
page.
**Evidence level:** Several separate single-page tests.

### Tesseract — Tested twice, in different ways
**What it is:** A long-standing, free, non-AI OCR engine — the oldest and simplest tool in
this whole report.
**What was tested:** (1) Wrapped inside a tool called PDF-MCP, run against a private
22-question benchmark; (2) run on its own against a dense census income table with merged
headers.
**What it did well:** Nothing notably positive recorded in the source — it comes across as
the weakest performer among the tools tested here.
**What it got wrong:** Inside the PDF-MCP wrapper, giving an agent direct command-line
access to Tesseract scored slightly higher and ran faster than going through the wrapper —
so that test is really about the wrapper being a drag on Tesseract, not about Tesseract vs.
other readers. Run on its own against the census table, it returned a single number for the
entire 2020 row and silently dropped every other value in that row — including a $67,521
income figure — then carried on into the 2019 row as if nothing were missing. A vendor OCR
API (Datalab), tested on the same table, kept that row whole.
**Evidence level:** Two tests, each a single comparison — the 22-question benchmark is the
closest thing in this whole source to a multi-item test rather than a single page.

### Market-list only, not tested here
**Azure Document Intelligence**, **Google Document AI**, and **EasyOCR** — none of these
appear being run anywhere in the 177 posts.

---

## 4. Layout and location tools

These don't necessarily transcribe words — their job is finding *where* things are on the
page, so a user can click an answer and jump straight to the spot it came from.

### Surya — Tested
**What it is:** A layout/location tool: it draws a box around a region of text or a table so
software (or a person) can click through to the source.
**What was tested:** General layout-finding tasks, including a page with an equation block; and,
in the newest test, used as a transcriber on a handwritten 1924 diary line ("Left New Haven at
7:37 P.M.").
**What it did well:** Its equation block kept an exponent that a table-rebuilding tool
(Docling) lost. A box drawn by this kind of tool doesn't have to be a flat rectangle — it can
follow angled or warped text, so a click lands on the actual words instead of a loose box
around them.
**What it got wrong:** Weaker at reading the actual text than specialist readers; on one
occasion it invented a table that wasn't there.
**The diary line — outvoted, not proven wrong:** It read the time as "9:37 P.M." where a vendor
OCR API (Datalab) and LightOnOCR-3-4B both read "7:37 P.M." Nobody knows which is right: the
handwritten digit is ambiguous, and Isaac's pipeline held the value unverified rather than going
with the majority. What Surya's disagreement did was useful on its own — it's what flagged the
field for human review.
**Evidence level:** A few specific page mentions, not a sustained test.

### PP-DocLayoutV3 — named, not clearly tested
The source mentions this as one of the "newer" layout tools that also predicts the *order*
regions should be read in — but it's named as an example of the category ("and similar"),
with no specific page or result attached to it in the 177 posts. Treat as a lead, not
something Isaac tested and reported on.

---

## 5. Search / embedding models

These aren't readers at all — their job is finding the right page or passage in a large pile
of documents, not transcribing it.

### Ovis-VL-Embedding — Tested
**What it is:** A document-trained search/embedding model — trained specifically on document
questions, text embedded in images, and infographics, rather than general web text.
**What was tested:** Finding a place name on a map that was abbreviated and crossed by
contour lines; finding a specific fee buried inside a scanned table.
**What it did well:** Brought back the correct page first in both cases.
**What it's for, specifically:** This is the tool for "it never found the page" problems —
not for "it misread the page once it found it."
**Evidence level:** Two specific retrieval tests.

---

## 6. Tools and platforms (not models — the thing a model sits inside)

These names come up constantly in the source, but they're products, not models — each one
runs a model (sometimes more than one, sometimes swappable) underneath. I'm including them
because the brief asked for them, clearly labelled as tools.

### Docling — Tested extensively, the most-tested tool in the source
**What it is:** A table-rebuilding tool — strong on clean tables, fast and cheap, not an AI
chat model.
**What was tested:** Many tables and charts across different domains — a drug side-effects
table with merged column headings, a state poverty-statistics table, a conference-poster
regression table, a tax worksheet, a chart with labels and durations, PowerPoint/Keynote
slide decks, a research paper's reading order, a newspaper's column order.
**What it did well:** Strong and fast on clean tables generally. Correctly read a tax
worksheet's income limit where a cheap classical reader dropped a digit. A newer version
uses the printed section lines on a page to work out reading order, and that fixed two
separate problems: a research paper's abstract being read after its introduction, and a
newspaper's interview text finishing before the portrait captions underneath it. It kept the
minus signs in a printed formula that a cloud service (Textract) dropped. With a *larger*
image model plugged into it, it correctly read all six values on a chart that, with its
small default image model, it misread entirely (wrong labels and durations both) — the
clearest example in the whole source that "which tool" and "which model is inside it" are
two separate questions.
**What it got wrong:** On a drug side-effects table, it put a four-column merged heading into
a single cell and, when that heading was long, split it so the word "Disorders" landed in a
numbers column. On a state poverty table, it merged Texas's figure (3,718) and Utah's figure
(231) into Utah's cell and left Texas's cell blank — so an agent reading the output would
report Texas's number as Utah's. On a conference poster, it filed every finding from a
regression table under the last of three group headings; cropping the page down to just that
table (no other change) fixed it. It dropped an exponent boundary on a formula page and
mangled the first sentence of a degraded scan.
**Evidence level:** By far the most examples of any single name in the source — many pages,
across years of posts — but still individual page-by-page demonstrations, not a scored
benchmark.

### Datalab — Tested, but as three different products under one name
**What it is:** A vendor that sells a hosted OCR API — not a single product. The source
tests three separate things sold under the "Datalab" name: a general OCR API, a track-changes
feature, and "Datalab Accurate" (a layout tool).
**What was tested:** The general OCR API was tested on the same dense census income table
mentioned above under Tesseract — and kept the 2020 row whole, including the $67,521 median
figure, where the cheap classical tool dropped it. The track-changes feature was tested on a
handwritten edit, where it caught the inserted word but dropped the deleted one (the vendor's
own examples are digital revisions, so handwriting may be outside what it's built for).
"Datalab Accurate" was tested on whether its bounding regions can follow angled, warped text.
The general OCR API comes up again in the newest test, on a handwritten 1924 diary line: it read
the departure time as "7:37 P.M.", the same as LightOnOCR-3-4B, where a layout tool (Surya) read
"9:37 P.M." Treat that as two readers agreeing, not as a correct answer — the digit on the page
is ambiguous and the value was held for review.
**Why this matters for you:** If someone tells you "we use Datalab," ask which product —
the three behave differently and were tested on different things.

### LiteParse — Tested once, as a routing platform
**What it is:** A platform that hosts a route: it runs a fast first pass, and if that fails,
escalates to other tools.
**What was tested:** A Bolivian sovereign bond prospectus with complex tables, an electricity
bill, and a World Bank financing allocation table.
**What it did well:** The fast first pass failed on the prospectus's complex tables, so the
page escalated to a layout step (Docling, finding the table) and then a transcription step
(Qwen, reading it) — and the medium- and long-term debt figures came back correct. One
example in the comparison showed 20 percentage points of accuracy gained for one extra second
of processing time on a given page.
**The real point of this test (per Isaac):** it's not "LiteParse is good" — it's that
splitting "find the layout" and "transcribe it" into two separate, inspectable steps, and
measuring the accuracy-gained-per-second-spent on each page, is the useful habit, independent
of which specific tools you plug into the route.
**Evidence level:** One worked example, shown on three documents within it.

### Reducto — named as the category, not itself tested
The source uses a router (fast reader + slow reader working side by side, measuring whether
the slow one was worth it) as the example of this idea, and explicitly says "the tool pair
used to illustrate it was the source's own example, not the vendor's." Reducto is named as an
example of this category of tool ("and similar"), not a product Isaac ran a test on directly.

### MinerU, Surya — covered above as model/tool entries (local-index tool and layout tool respectively).

### MarkItDown — Tested, as a cautionary example
**What it is:** A general markdown converter — not built for scanned or structured documents,
and not an OCR engine at all.
**What was tested:** Converting a research paper's title and abstract.
**What it got wrong:** It turned the title and abstract into a fabricated multi-cell table,
because it treats any well-aligned block of text as a table whether or not it actually is
one.
**Evidence level:** One page, used specifically as a warning: alignment on a page is not
evidence of a real table.

### PDF-MCP — Tested once (see Tesseract above)
A wrapper tool around Tesseract. On a private 22-question benchmark, giving an agent direct
command-line access to Tesseract beat going through this wrapper, on both score and speed —
a test of the wrapper's overhead, not of Tesseract's reading quality.

### PaddleOCR (classic, distinct from PaddleOCR-VL) — Tested
**What it is:** A free, non-AI "cheap classical" OCR tool.
**What was tested:** A timetable page; a sideways/rotated scan; a printed typo; struck-through
handwriting; a tax worksheet with a dollar bracket.
**What it did well:** Matched a much more expensive image-reading model on one timetable,
while being faster and cheaper. Its rotation detector fixed a sideways page that nothing else
in the source managed to read.
**What it got wrong:** Left a printed typo exactly as printed where other tools silently
"corrected" it (right or wrong depending on whether you wanted an exact copy). Misread
struck-through handwriting badly. On a tax worksheet, it dropped the leading digit of
$250,525 and returned $50,525 — producing an income bracket that no real income could fall
into, and it reported this without flagging anything as wrong.

---

## Comparison table

| Name | Type | Tested in Isaac's posts? | Best at (from a real test) | Worst at (from a real test) |
|---|---|---|---|---|
| Chandra | Document-specialist model | Yes, several times | Degraded cable text, messy handwriting | Didn't say which word was deleted in an edit; mislabelled Fed table headers |
| MinerU | Local-index tool | Yes, once-twice | Retrieving one clause from a 23-page agreement | Merged two similarly-named models into one cell |
| LightOnOCR (3-4B) | Document-specialist model | Yes, twice (Isaac still evaluating) | Kept a merged census cell's two numbers separate | — (its other test, an ambiguous handwritten digit, has no knowable right answer) |
| Mistral OCR, PaddleOCR-VL*, GLM-OCR, DeepSeek-OCR, olmOCR, Nanonets OCR, FireRed-OCR | Document-specialist models | No — market list only | — | — |
| Gemini (3.5 Flash) | General vision model | Yes, the most-tested model overall | Handwritten name + 6 checkboxes on a bank form | Invented a pill-bottle copyright line; left a temperature condition blank |
| Qwen (3 VL) | General vision model | Yes, several times | Kept a grey cell's "not applicable" meaning | No clean failure case recorded |
| GPT-5, Claude Sonnet, Gemini 3 Pro, InternVL | General vision models | No — market list only | — | — |
| AWS Textract | Classic OCR service | Yes, several times | Read a one-letter-per-box surname correctly | Dropped plus/minus signs from a printed formula |
| Tesseract | Classic OCR service | Yes, twice | — | Lost an entire table row, including a $67,521 figure |
| Azure Document Intelligence, Google Document AI, EasyOCR | Classic OCR services | No — market list only | — | — |
| Surya | Layout/location tool | Yes, a few mentions | Kept an exponent on an equation box; its disagreement flagged a diary time for review | Once invented a table; read a diary time as 9:37 where two readers read 7:37 (page is ambiguous) |
| PP-DocLayoutV3 | Layout/location tool | No — named only | — | — |
| Ovis-VL-Embedding | Search/embedding model | Yes, twice | Found an abbreviated place name on a map | — |
| Docling | Table-rebuilding tool | Yes, extensively | Fixed reading order using section lines | Merged two states' figures into one cell |
| Datalab (3 products) | Vendor OCR API / tools | Yes, each product separately | Kept a dense table row whole | Dropped a deletion in a tracked-changes test |
| LiteParse | Routing platform | Yes, once | Escalating a bond prospectus's hard tables to the right specialist | — |
| Reducto | Router (category example) | No — named as category only | — | — |
| MarkItDown | Markdown converter | Yes, once | — | Fabricated a table out of plain aligned text |
| PDF-MCP | Wrapper tool | Yes, once | — | Added overhead vs. direct CLI access to Tesseract |
| PaddleOCR (classic) | Cheap classical OCR | Yes, several times | Fixed a rotated page nothing else could read | Dropped a leading digit, creating an impossible tax bracket |

*PaddleOCR-VL has one test, but of box shape on angled text, not reading accuracy — treated here as effectively untested for reading quality.

---

## The one thing to take from all of this

None of this tells you which model to use. It tells you what to go check on your own
pages. Every "did well" above is a reason to try that model on a similar page of yours;
every "got wrong" is a reason to build a check for that specific failure before you trust
any model's output blind. Isaac's whole point, repeated across 177 posts, is that the
reader that wins on his page might lose on yours — so the test is yours to run, not his to
hand you.
