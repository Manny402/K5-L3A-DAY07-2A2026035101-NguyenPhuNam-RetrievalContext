# Day 07 benchmark protocol — frozen before retrieval

Scope: basic lab only (85 points); no advanced/bonus document or benchmark.

## Data and split

Use the exact Day 06 B snapshot: 418 unique chunks, 68 normalized source units
from five CPython 3.13.9 documents, same pinned MiniLM and pgvector index.
Corpus permissions and original notices remain in the Day 06 repository;
Day 07 records source hashes and lawful reproduction instructions.

Minimum 24 cases: dev=15 answerable+2 unanswerable; test=5 answerable+2
unanswerable. The answerable dev cases originate from Day 06 dev, with known
equivalent evidence checked before Day 07 retrieval. Day 06 test cases are
not reused. New test cases target skipkeys, DictWriter extrasaction,
Path.unlink missing_ok, GzipFile compresslevel and initial logger levels.

The author sees source/gold while constructing questions; this is a procedural
holdout, not blind independent evaluation. Source documents overlap across
splits, and the sample is small. No test metric may tune the pipeline.

Unanswerable cases are near-topic requests absent from the fixed corpus:
JSON encryption key, CSV per-file row maximum, gzip encryption password,
and guaranteed logging delivery latency. Whole-corpus term searches plus a
qualitative review of the relevant complete module establish the limited
corpus-scope absence claim; top-k failure alone is insufficient.

## Compared configurations

G0=dense, G1=TF-IDF, G2=hybrid RRF. All share the same 418 chunks, verified
input filters, model, prompt, max one search, top-3, whole-chunk context
policy and 2400-character context budget including headers. Hybrid pools
20 results per branch, constant 60, and combines ranks rather than raw scores.
G1 and G2 are the two improvement experiments, not assumed improvements.

G3 is an additional dev experiment: hybrid, top-6, 4800 characters, three
searches. It changes multiple factors and must be reported as a pipeline
experiment; it cannot isolate the effect of any one factor.

Model: user-selected OpenAI gpt-5.4-mini, resolved to official snapshot
gpt-5.4-mini-2026-03-17, reasoning effort none. Model and code hashes must be
consistent between compared runs. Each agent decision is a real model
function call; deterministic fixtures are restricted to unit tests.

## Metrics and reviews

Original-query Hit@1 and Evidence Hit@3 compare the same ranking cutoff and
only answerable cases. The main operational automatic hit requires a valid
source/unit and a complete necessary evidence quote (or any predeclared
alternative). No union of separate chunks counts as a single retrieval hit.

Context Evidence Rate measures the final context sent to the agent; contiguous
source intervals may jointly contain evidence, but missing source content
must never be silently bridged. Report first tool-query rankings separately
from original-query rankings; query rewriting is recorded explicitly.

Context grade: 0=no evidence; 1=partial/missing condition/source; 2=sufficient.
Grounded answer: 0=wrong/unsupported/false abstention; 1=partly correct;
2=fully correct, sufficiently evidenced. Judge each citation for semantic
support as well as membership in final context. Abstention requires an
explicit refusal without unsupported additional factual assertions.

Report tool-use n/successful cases, all planned/success/error counts, citation
validity n/total citations (0/0=N/A), citation coverage, abstention on missing
evidence and false abstention on answerable cases. Provider errors are
reported separately; also retain all-scheduled denominators for selection,
retrieval/context and success coverage.

Qualitative reviews use a real-model evaluator with an explicit saved prompt,
reference evidence, candidate answer and exact delivered context. This is
AI-assisted assessment by the same model family as the answerer, not
independent human review. Record model usage, score and a concrete rationale.
Inspect at least two dev errors and every test answer/context/citation;
retain any author corrections with reasons. Do not infer answer quality
from string match or citation IDs alone.

## Dev selection and test

Select lexicographically on dev: number of grade-2 answerable responses
over all 15 scheduled answerable cases (errors/false abstentions fail),
then correct abstentions over both unanswerable cases, then final Context
Evidence Rate, then original-query Evidence Hit@3, then lower estimated
agent cost, then lower mean latency. Unknown cost is never treated as zero.

Freeze selection.json with model/config/prompt/code/corpus/benchmark/protocol
hashes before test. Run G0 and the selected config on all seven test cases.
If G0 wins, one baseline run serves both roles. Test is not used for choosing
a model, prompt, budget or method. No score threshold is promised.

## Trace, cost and reproducibility

Store each original input, rewritten tool query, tool arguments/filter,
full ranked results, final context/citation map, final response, stop reason,
provider request/model IDs, tokens, retries, latency, API errors and
reviewer labels. Agent payloads never contain gold, expected answer,
answerability labels or evidence regions.

API costs are estimates from observed token usage and the published standard
model rates verified on 2026-11-14; they are not billing records and exclude
credits, custom account discounts, hardware/electricity and local embedding
runtime. Report evaluator cost separately from agent cost. Unknown prices or
missing usage remain unknown. Never save API keys or authentication headers.

Existing completed outputs are not overwritten. Partial runs can be resumed
only when the same fingerprints still match; preserve original failures.
