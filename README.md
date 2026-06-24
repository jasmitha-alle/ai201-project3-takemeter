
Community

r/anime (~2.4M members). Discourse ranges from detailed structural critiques to one-word reactions, making the hot-take-vs-analysis distinction real and community-recognized. High post volume and consistent discussion norms make it a good fit for classification.


Label Taxonomy

analysis — Makes a specific, reasoned claim about an anime's writing, themes, or structure, supported by evidence. The argument is the point.
Example 1: "Vinland Saga Season 2 is better than Season 1 precisely because it's less exciting. Season 1 is a revenge fantasy. Season 2 is an argument that the revenge fantasy itself is the problem."
Example 2: "Mushishi's key isn't the anthology structure — it's that Ginko never solves problems, he manages them. The mushi aren't evil, they're alive. That framing makes the show more honest than anything with a clear antagonist."

hot_take — Asserts a strong opinion or verdict without substantive reasoning. The conclusion is the point.
Example 1: "Demon Slayer is the most overrated anime of the past decade. Remove Ufotable's animation and you have a completely average shonen."
Example 2: "Death Note drops off a cliff after L dies and anyone who argues otherwise is coping."

reaction — A reaction, simple question, or brief observation with no real argument.
Example 1: "Just finished Frieren. I don't know how to go on."
Example 2: "Is the Steins;Gate movie necessary to watch after the series?"


Data Collection

Dataset was generated to reflect authentic r/anime discourse patterns after direct Reddit scraping was blocked by network restrictions. All 200 examples were manually reviewed for label consistency.

Label distribution: hot_take: 70, analysis: 69, reaction: 61

Three difficult examples:

1. "Seasonal anime discourse rewards spectacle over craft. Shows like Frieren get less engagement because there's less to screenshot." — Has a structural argument but no textual evidence from any show. Labeled analysis because the argument is the point, not the verdict.

2. "Mushishi is the most boring masterpiece in anime history." — Acknowledges quality while asserting a verdict. Labeled hot_take because the claim is the entire post.

3. "Cid from The Eminence in Shadow is a better written OP protagonist than Saitama and I'll explain if anyone actually wants to engage." — Promises reasoning without providing it. Labeled hot_take because the reasoning is absent.


Fine-Tuning

Base model: distilbert-base-uncased

Setup: 3 epochs, learning rate 2e-5, batch size 16 (defaults). Split: 140 train / 30 val / 30 test, stratified by label. No hyperparameters were changed — with only 140 training examples, additional epochs would risk overfitting.


Baseline

Model: Groq llama-3.3-70b-versatile, zero-shot. Prompt included community name, one-sentence label definitions, one example per label, and instruction to output only the label name. All 30 test responses were parseable.


Evaluation Report

Accuracy

| Model | Accuracy |
|-------|----------|
| Zero-shot baseline (Groq) | 0.967 |
| Fine-tuned DistilBERT | 0.533 |

Per-Class Metrics — Baseline

| Label | Precision | Recall | F1 | Support |
|-------|-----------|--------|----|---------|
| analysis | 1.00 | 1.00 | 1.00 | 11 |
| hot_take | 1.00 | 0.90 | 0.95 | 10 |
| reaction | 0.90 | 1.00 | 0.95 | 9 |
| accuracy | | | 0.97 | 30 |

Per-Class Metrics — Fine-Tuned

| Label | Precision | Recall | F1 | Support |
|-------|-----------|--------|----|---------|
| analysis | 0.70 | 0.64 | 0.67 | 11 |
| hot_take | 0.45 | 0.90 | 0.60 | 10 |
| reaction | 0.00 | 0.00 | 0.00 | 9 |
| accuracy | | | 0.53 | 30 |

Confusion Matrix (Fine-Tuned)

|  | Pred: analysis | Pred: hot_take | Pred: reaction |
|--|----------------|----------------|----------------|
| True: analysis | 7 | 4 | 0 |
| True: hot_take | 1 | 9 | 0 |
| True: reaction | 2 | 7 | 0 |

The model predicted "reaction" zero times. Almost all errors are things being collapsed into hot_take.

Wrong Predictions Analyzed

1. "Steins;Gate's time travel is not actually about time travel. The mechanics are a delivery system for a story about obsession..." — True: analysis, Predicted: hot_take (0.35)

The post opens with a confident declarative claim, which the model learned to associate with hot takes. It cannot distinguish between a verdict and a thesis. Both labels use strong declarative language — the difference is whether reasoning follows, and DistilBERT can't parse argument structure.

2. "Just watched Madoka Magica knowing nothing about it. Not okay." — True: reaction, Predicted: analysis (0.34)

Pure reaction, but "knowing nothing about it" may have triggered an association with analytical framing. Confidence is 0.34 — the model had no real signal and guessed. Short posts gave the model nothing to work with.

3. "Cid from The Eminence in Shadow is a better written OP protagonist than Saitama and I'll explain if anyone actually wants to engage." — True: hot_take, Predicted: analysis (0.35)

The phrase "I'll explain" signals reasoning exists, which the model read as analysis. But the reasoning is absent — it's a verdict with a deferred promise of argument. The model confused the form of analysis with the presence of analysis.

Systematic pattern: The dominant error is reaction being predicted as hot_take (7 of 9 reactions wrong). The model never learned the reaction label. With only ~43 reaction examples in training, DistilBERT couldn't separate short opinionated-sounding reactions from actual hot takes when surface language overlaps.

Sample Classifications

| Post (truncated) | Predicted | Confidence |
|------------------|-----------|------------|
| "Vinland Saga Season 2 is better than Season 1 precisely because it's less exciting..." | analysis | 0.38 |
| "Demon Slayer is the most overrated anime of the past decade..." | hot_take | 0.41 |
| "Just started One Piece. Send help." | hot_take | 0.35 |
| "Hunter x Hunter's Chimera Ant arc is the longest and also its best..." | hot_take | 0.36 |
| "Death Note drops off a cliff after L dies and anyone who argues otherwise is coping." | hot_take | 0.44 |

The Vinland Saga prediction (analysis, 0.38) is correct and reasonable — it's a long structured argument that reads differently from the single-sentence hot takes. Low confidence across the board reflects how uncertain the model is even when right.


Reflection

I intended the model to learn the distinction between posts where an argument is the point versus posts where a verdict is the point. It instead learned a crude proxy: long posts tend to be analysis, everything else tends to be hot_take. It never learned reaction as a distinct category.

The core problem is that the analysis/hot_take boundary is semantic, not lexical. Both labels use confident declarative language. DistilBERT fine-tuned on 140 examples could only learn surface patterns. The zero-shot LLM could make the distinction because it actually comprehends text. That gap — 96.7% vs 53.3% — is the result of asking a small model to learn a task that requires language understanding, not pattern matching.


Spec Reflection

The spec's requirement to define success criteria before training was the most useful constraint — it forced me to decide what good enough meant before seeing results, which made the 53% accuracy interpretable rather than just disappointing.

Implementation diverged in data collection. The spec assumes manual collection from Reddit; network restrictions blocked this so the dataset was generated to reflect authentic r/anime discourse. This likely made the data cleaner than real posts, which may explain why the zero-shot baseline performed unusually well (96.7%). Real posts are messier and would have made the task harder for both models.


AI Usage

1. Dataset generation: 

2. Failure pattern analysis:

3. Baseline prompt: The Groq classification prompt was drafted with Claude using my label definitions from planning.md, then reviewed to confirm it matched the notebook's parsing requirements.
