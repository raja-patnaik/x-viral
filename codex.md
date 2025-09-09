# The X “For You” Algorithm: A Creator Playbook

This guide distills how the For You ranking system in this repo surfaces posts, the signals it rewards, and the common ways posts get filtered or down‑ranked. It translates code paths from Home Mixer, candidate sources, feature hydration, model scoring, and heuristics into concrete tactics to help your posts travel further.

Sections
- How For You Works (high level)
- What The Model Rewards (positive signals)
- What Hurts Reach (negative signals, filters)
- Heuristics That Re‑rank (boosts, decays, diversity)
- Social Context & Out‑of‑Network Expansion
- Content Type Tips (text, links, images, video, replies)
- Topic, Community, and Similarity Systems
- Author & Network Considerations
- Practical Posting Playbook

---

## How For You Works

Pipeline overview (home-mixer/README.md):
1) Candidate generation
   - In‑network: your follow graph via Earlybird search.
   - Out‑of‑network: UTEG (tweets liked by people you follow), TweetMixer, FRS, Communities, SimClusters ANN, etc.
2) Feature hydration (~6k features)
   - Real‑time aggregates (engagements), Tweet metadata, language, view counts, social graph, SimClusters, content embeddings, topic signals, user history, etc.
3) Scoring with ML
   - Heavy ranker (Navi) predicts multiple engagement probabilities per tweet and combines them into a weighted score.
4) Heuristics and filters
   - Diversity (author, source, topic, content), reply and out‑of‑network scaling, feedback fatigue, cold start, safety and quality filters, deduplication.
5) Mixing and serving
   - Ads and modules are inserted; final list is sorted by score and returned.

Core code paths
- Product: `home-mixer/product/scored_tweets` (Recommendation + Scoring pipelines)
- Scorer: `NaviModelScorer` combines per‑engagement predictions
- Heuristics: `HeuristicScorer` applies multiplicative boosts/penalties
- Filters: Grok NSFW/Gore/Violent/Spam, language, duplicates, OON reply limits

---

## What The Model Rewards

The heavy model predicts likelihoods of many engagements; a weighted mix becomes the main ranking score. Driving the following behaviors increases your score (from `home_mixer/.../scorer/PredictedScoreFeature*` and `.../model/PredictedScoreFeature*`):

- Likes, Replies, Retweets
  - Predicted probabilities of like/favorite, reply, and retweet.
  - Also “reply engaged by author” when your reply leads to meaningful follow‑up.

- Good Clicks and Profile Interest
  - Good clicks (V1/V2): clicks that lead to downstream positive actions (reply/like, multiple user actions). Avoid empty/low‑quality clicks.
  - Profile clicks and profile dwell (e.g., 20s) are positive—bio/links/pinned post matter.

- Dwell and Detail Views
  - Tweet detail dwell (e.g., 15s) and broader dwell features are rewarded; clear, curiosity‑driving posts that invite open/thread reading help.

- Shares and Bookmarks
  - Sharing and using the share menu are explicitly modeled. Bookmarks are a strong “save for later” signal (evergreen value).

- Video Quality Views and Watch Time
  - Video “quality viewed” (including immersive) and watch time (ms) are explicit targets.
  - Longer than 10s gates some video features; high initial retention and completion rate matter.

Notes
- The weighted sum uses tunable weights (see HomeGlobalParams.Scoring.ModelWeights). Some negative weights exist for negative feedback (below). The sum is normalized and never goes negative in final blending.
- When video and dwell features overlap, the system may choose one or gate contribution (e.g., VQV vs Dwell binary thresholds).

Practical implications
- Aim for actions beyond a superficial click: invite replies, incentivize saves/bookmarks, and create “shareable” insights.
- Make your profile and pinned content worth visiting to convert profile clicks into follow/engagement loops.
- For video, hook within the first seconds, deliver value early, and sustain attention to raise the probability of 50%+ playback and watch time.

---

## What Hurts Reach

Negative engagement signals
- “Not interested/See fewer,” weak/strong negative feedback, and reports are directly down‑weighted.
- Repeated recent “See fewer” on you, your retweeters, or your likers causes per‑viewer “feedback fatigue” penalties.

Safety/quality filters (Grok annotations)
- Explicit filtering for content labeled as NSFW, Gore, Violent, Spam; “low‑quality”/“slop” may be down‑ranked.
- Slop score can apply decay; low‑signal users can trigger SlopFilter removing certain OON authors.

Out‑of‑network constraints
- OON tweets are scaled down by default (param ~0.75). Replies are also down‑weighted by default (~0.75).
- OON replies are generally filtered unless you replied to someone the viewer follows.
- Some competitor URL/author filters can remove OON posts with certain links/authors when enabled.

Duplication and fatigue
- Media deduping, quote/tweet deduping, previously served preview filtering, and author diversity prevent stacking similar content.

Language and visibility
- Language filters and auto‑translate gates can reduce delivery to viewers unlikely to understand your post.
- User visibility rules, blocks/mutes, and NSFW settings reduce eligible audience.

---

## Heuristics That Re‑rank

These are multiplicative factors applied to the ML score (HeuristicScorer):

- Out‑of‑Network Scale
  - OON candidates get a scale factor (< 1). You overcome this with strong social context and engagement.

- Reply Scale
  - Replies receive a scale factor (< 1). Original posts have an easier time spreading. If replying, do it on posts/accounts your audience follows.

- MTL Normalization
  - Adjusts scores based on author follower size and post type to calibrate multi‑task output; you can’t tune this, but it helps fairness across creators.

- Author Diversity Decay
  - Repeated posts from the same author are exponentially down‑weighted (different floors/decays for in‑ vs out‑of‑network). Spacing out posts and varying content avoids steep decay.

- Candidate Source Diversity Decay
  - Too many posts from the same source/reason are decayed. Appearing via several sources (in‑network, UTG, communities, SimClusters) helps.

- Feedback Fatigue
  - Per‑viewer decay if they recently chose “See fewer” on you or related engagers.

- “Slop” Score Decay
  - Certain Grok slop labels apply a configurable decay (< 1.0).

- Media/Image Cluster Fatigue
  - If a viewer has seen many posts from the same media/image cluster, similar new posts are decayed. Vary visuals and topics.

- Diversity Re‑ranking (MMR and category)
  - Rescoring encourages semantic and topical diversity using embeddings (Transformer/Twhin/SimClusters). Don’t post many near‑duplicates in a row.

---

## Social Context & Out‑of‑Network Expansion

Out‑of‑network posts usually need social proof to be shown:
- Liked‑by or Followed‑by social context from people the viewer follows.
- Topic context (recommended/followed topics) can gate OON eligibility.
- Replies from authors you don’t follow are filtered unless they reply to someone you do follow.

Tactics
- Earn early engagement from accounts that overlap many follow graphs (mutuals matter more than raw count).
- Participate in relevant conversations with large, followed accounts so replies can be eligible.
- Make topical signals clear (hashtags, named entities) to anchor topic social proof.

---

## Content Type Tips

Text‑only
- Lead with a strong first line; provoke curiosity to earn detail views/dwell. Use concise structure that invites replies or saves.

Links
- Visible links are recognized. “Good clicks” (that lead to engagement) score; empty clicks don’t. Summarize value so clickers engage afterward.

Images
- Vary image clusters over time; repeated exposure to similar clusters gets decayed. Use distinctive visuals that complement the text’s value.

Video
- First 1–3 seconds are critical to pass 50% playback thresholds and drive watch time.
- 10s+ duration unlocks certain “quality view” features; design for fast hooks and sustained retention.
- Vertical/immersive viewing can contribute; optimize captions, clarity, and pacing.

Replies
- Replies are down‑scaled; maximize reach by replying to posts from accounts your audience follows or that are widely followed. Keep replies substantive to drive “reply engaged by author” and downstream engagement.

---

## Topic, Community, and Similarity Systems

SimClusters & ANN
- Content is embedded into topic/interest space; tweets similar to what a viewer likes are retrieved. Clear topical signals help the right audience find you.

Topic Social Proof (TSPS)
- Tweets annotated with topics can be recommended to users who follow or are likely to follow those topics. Use consistent terminology and context so annotators/embeddings pick up the topic.

Communities & Lists
- Communities and lists are separate sources—participation adds retrieval paths. Contribute where your target audience is active.

---

## Author & Network Considerations

- In‑network advantage
  - In‑network candidates aren’t penalized by the OON scale. Strong relationships with followers (and their mutuals) accelerate breakout.

- Verified/organization signals
  - Verification and organization details are present as features; they can affect downstream systems. Consistent, trustworthy behavior reduces safety friction.

- Language
  - Matching viewer languages helps both model and filters. If posting in multiple languages, consider separate posts or clear language markers; auto‑translate paths are gated.

---

## Practical Posting Playbook

Before posting
- Define the action you want: reply, bookmark, share, profile visit, or video watch. Design the opening to drive that action.
- Anchor a clear topic: use precise phrasing and context so topic/embedding systems classify you correctly.

At publish
- Time for your followers: the first minutes determine social proof and UTG pickup.
- If replying, choose targets your audience follows; add unique value that elicits engagement.

Early momentum
- Engage with early commenters to stimulate replies and “reply engaged by author”.
- Encourage saves/shares explicitly when content is reference‑worthy.

Sustain and diversify
- Avoid posting many near‑identical items consecutively; vary authorship timing and topics to dodge diversity decay.
- With video/images, mix formats and clusters; keep hooks fresh.

Avoid pitfalls
- Don’t bait low‑quality clicks; model favors “good clicks” with downstream actions.
- Steer clear of content flagged by safety/quality (NSFW, violent, spammy, slop). These get filtered or decayed.
- Don’t overexpose the same viewers; feedback fatigue hurts per‑viewer scores for weeks.

Grow out‑of‑network
- Cultivate engagers with broad mutual follow overlap; a few right likes can beat many random ones.
- Participate in trending topics and communities aligned to your niche to open additional candidate sources and social contexts.

Measure and iterate
- Track which posts drive replies/bookmarks/shares vs. empty clicks.
- For video, focus on first‑frame clarity, pacing, and captioning to lift 50% playback and watch time.

---

References to code (non‑exhaustive)
- Scoring: `NaviModelScorer`, `PredictedScoreFeature` (engagement signals)
- Heuristics: `HeuristicScorer`, `RescoringFactorProvider` (OON/reply scale, diversity, fatigue, slop)
- Filters: `GrokNsfwFilter`, `GrokGoreFilter`, `GrokViolentFilter`, `GrokSpamFilter`, `LanguageFilter`, `OONReplyFilter`, media/quote dedup.
- Diversity: `DiversityRescoringFeatureHydrator`, `CategoryDiversityRescoringFeatureHydrator`, author/source diversity params in `ScoredTweetsParam`.
- Social context gating: `ScoredTweetsSocialContextFilter`, earlybird `InNetworkEngagement` features.
- Retrieval sources: `ScoredTweets*CandidatePipelineConfig` and `Earlybird*QueryTransformer` families, UTG/FRS integrations, `simclusters-ann`.

