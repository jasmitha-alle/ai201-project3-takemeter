TakeMeter Planning Document


Community

r/anime (~4M members) and associated episode discussion threads. Anime discussion is extremely active and text-heavy — every major airing episode gets a dedicated thread with hundreds of comments ranging from detailed narrative analysis to pure reaction. The community has strong implicit norms about what counts as a good take vs. noise, and the signal-to-noise ratio varies enormously across posts. This makes it a good fit for discourse quality classification.


Label Taxonomy

Analysis: Makes a specific, reasoned claim about an anime's writing, direction, themes, or structure, supported by evidence from the show itself. The argument is the point.
Example 1: "Chainsaw Man's refusal to give Denji a traditional hero arc is the whole point — he never grows into a savior because he was never given the conditions to develop real selfhood. Every choice he makes is shaped by deprivation, not agency."
Example 2: "The reason Evangelion's final two episodes work despite the budget collapse is that Hideaki Anno realized the psychological drama was always internal. The abstract animation forces you to engage with Shinji's mind directly rather than through spectacle."

Hot Take: Asserts a strong opinion or ranking about a show or anime broadly, without substantive reasoning. The verdict is the point, not the argument.
Example 1: "Demon Slayer is carried entirely by Ufotable's animation. The story is mid and would be forgotten if it looked like an average production."
Example 2: "One Piece is the greatest story ever told in any medium and nothing else comes close. If you dropped it you just weren't patient enough."

Low-Effort: Reaction, hype, a simple question, or a brief observation with no real claim.
Example 1: "THAT EPISODE. I am not okay."
Example 2: "Does anyone know if season 2 has been confirmed yet?"


Hard Edge Cases

The main hard case is a Hot Take with a thin reason attached, e.g. "Demon Slayer is overrated — the plot is too simple." Decision rule: if the reasoning could be deleted and the post still feels complete, it's a Hot Take. If the post is building toward the reason, it's Analysis.

A secondary case is a highly specific reaction, e.g. "The animation in episode 19 was insane, the way they handled the fight choreography was on another level." This names something specific but doesn't analyze it. Label as Low-Effort unless the post explains what the direction actually did and why it worked.


Data Collection Plan

Source: r/anime episode discussion threads for currently airing and recently finished shows (Frieren, Dungeon Meshi, Mushishi, Vinland Saga, JJK). Episode discussion threads are ideal because they concentrate discourse from casual viewers and serious fans in the same place, giving natural variation across all three labels.

Target distribution is roughly 70 Analysis, 70 Hot Take, and 60 Low-Effort. Low-Effort is easiest to find in volume so I will cap collection there and prioritize Analysis, which is rarer. If any label falls below 20% after 150 examples, I will collect more targeted examples — search "theory" or "writing" threads for Analysis, "unpopular opinion" or seasonal ranking posts for Hot Takes.


Evaluation Metrics

Primary metric is per-class F1. Accuracy alone is misleading if the model just predicts the majority class — all three labels need to show meaningful F1. Secondary metric is the confusion matrix, to see whether errors are directional (e.g. Analysis predicted as Hot Take but not vice versa), which reveals which boundary the model failed to learn.


Definition of Success

Minimum viable: overall accuracy at or above 65%, per-class F1 at or above 0.55 for all labels, fine-tuned model beats zero-shot baseline by at least 8 percentage points.

Good enough to deploy: overall accuracy at or above 75%, per-class F1 at or above 0.65, no label pair confused at more than 30%.


AI Tool Plan

Label stress-testing: Ask Claude to generate 10-15 posts at the Hot Take/Analysis boundary before annotating. If I can't classify them cleanly, tighten the definitions first.

Annotation assistance: Use Claude to pre-label batches of ~50 examples using my label definitions, then review and correct every label myself. Track pre-labeled examples with a prelabeled column in the CSV.

Failure analysis: After evaluation, paste wrong predictions into Claude and ask it to identify patterns. Verify any proposed pattern by re-reading and counting manually before including in the report.
