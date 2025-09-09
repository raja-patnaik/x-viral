# AI Analysis of X's Algorithm: Creating Viral Posts

## Project Overview

This repository contains the results of an experiment where three leading AI coding assistants were given identical instructions to analyze X's (formerly Twitter) open-sourced ranking algorithm and produce comprehensive guides on creating viral posts.

## The Task

Each AI assistant was provided with access to [X's open-sourced algorithm repository](https://github.com/twitter/the-algorithm) and given the following prompt:

> "This repo contains the code for the ranking algo for the For You page on X. Can you go through the entire repo very carefully and thoroughly and write a detailed [assistant_name]_viral.md document explaining how to best make viral X posts?"

## Participating AI Assistants

- **Claude Code** (Anthropic) - Output: `claude_viral.md`
- **Codex** (OpenAI) - Output: `codex_viral.md`
- **Gemini** (Google) - Output: `gemini_viral.md`

## Methodology

Each AI assistant was expected to:

1. **Analyze the Codebase**: Thoroughly examine X's algorithm implementation including:
   - Ranking mechanisms and scoring functions
   - Engagement weight calculations
   - Feature extraction and importance
   - Timeline mixing strategies
   - Trust and safety filters
   - User relationship graphs
   - Content quality signals

2. **Extract Key Insights**: Identify the actual weights, multipliers, and factors that influence post visibility and engagement

3. **Create Actionable Guide**: Transform technical findings into practical strategies for content creators

## Repository Structure

```
.
├── README.md                 # This file
├── claude_viral.md          # Claude Code's analysis and guide
├── codex_viral.md           # Codex's analysis and guide  
├── gemini_viral.md          # Gemini's analysis and guide
└── the-algorithm/           # X's open-sourced algorithm (submodule)
```

## Key Areas of Analysis

The AI assistants were expected to investigate:

### Algorithm Components
- **home-mixer**: Timeline generation and mixing logic
- **timelineranker**: Post ranking mechanisms
- **trust_and_safety_models**: Content filtering and safety
- **visibilitylib**: Visibility rules and filters
- **graph-feature-service**: Social graph features
- **follow-recommendations-service**: User recommendation signals

### Ranking Factors
- Engagement metrics (likes, replies, retweets, clicks)
- User relationships (in-network vs out-network)
- Content features (media, text quality, language)
- Temporal signals (recency, engagement velocity)
- Negative signals (blocks, mutes, reports)
- Author credibility (verification, TweepCred scores)

### Technical Implementation
- Multi-stage ranking pipeline
- Neural network architectures
- Feature normalization parameters
- Scoring thresholds and limits
- Diversity controls

## Comparing the Analyses

Each AI assistant approached the task differently, providing unique insights:

- **Depth of Analysis**: How thoroughly each examined the codebase
- **Technical Accuracy**: Correctness of extracted weights and parameters
- **Practical Application**: Usefulness of recommendations for content creators
- **Code Understanding**: Ability to interpret complex ranking algorithms
- **Presentation**: Clarity and organization of findings

## Key Findings Across All Analyses

While each AI produced different outputs, common discoveries included:

1. **Reply Supremacy**: Replies are weighted significantly higher than other engagement types
2. **Early Engagement**: Initial momentum strongly influences reach
3. **Network Effects**: In-network vs out-network distinction matters
4. **Negative Signals**: "Not interested" and reports have severe impacts
5. **Content Quality**: Original content outperforms retweets

## Using This Repository

### For Content Creators
Compare the three guides to find consensus strategies and unique insights for improving your X presence.

### For Researchers
Examine how different AI systems interpret and explain the same complex codebase.

### For Developers
Understand X's ranking algorithm implementation and its implications for social media platforms.

## Ethical Considerations

These guides are intended for:
- Understanding platform mechanics
- Creating valuable, engaging content
- Building genuine communities
- Improving content strategy

They should NOT be used for:
- Manipulating the algorithm maliciously
- Spreading misinformation
- Harassment or abuse
- Spam or bot networks

## Contributing

If you'd like to add analysis from another AI assistant or update existing analyses:

1. Fork the repository
2. Add your `[assistant_name]_viral.md` file
3. Update this README with the new addition
4. Submit a pull request

## Disclaimer

- The algorithm weights and mechanisms are based on X's open-sourced code from 2023
- X may have updated their algorithm since the code was released
- These guides represent AI interpretations and may contain inaccuracies
- Focus on creating quality content rather than gaming the system

## License

This analysis project is provided for educational purposes. The underlying X algorithm code is subject to its original license terms.

## Acknowledgments

- X (Twitter) for open-sourcing their algorithm
- Anthropic, OpenAI, and Google for their AI assistants
- The open-source community for algorithm analysis tools

---

*Last Updated: 2025*