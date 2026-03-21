---
description: Generate social media content across multiple platforms with parallel subagents
allowed-tools: Bash(mkdir:*), Bash(cat *), Write, Read, Grep, Glob, Task
---

You are executing the `/generate-social-content` command. Generate social media content from user-provided context and distribute across selected platforms using parallel subagents. Follow this workflow:

## Step 1: Gather Context

Check if `$ARGUMENTS` is provided and non-empty:

- If provided: Use `$ARGUMENTS` as the topic/context
- If empty: Ask the user in chat:
  ```
  Describe the feature, product, or topic you want to create social media content for.
  You can also type "readme" to use your project's README.md as the content source.
  ```
  Wait for user response.

If the user responds with "readme":
- Run: cat README.md 2>/dev/null
- If no README found: ask user to provide context manually
- Use the README content as the topic context

Save the topic context as `{TOPIC}` for use in later steps.

## Step 2: Ask Timezone

Ask the user in chat:
```
What is your timezone? This is used to personalize best-time-to-post recommendations.

1. America/New_York (EST/EDT) — US East Coast
2. America/Los_Angeles (PST/PDT) — US West Coast
3. Europe/London (GMT/BST) — UK / Ireland
4. Asia/Tokyo (JST) — Japan / Korea
5. Asia/Jakarta (WIB) — Indonesia
6. Australia/Sydney (AEST) — Australia East Coast
7. Europe/Berlin (CET/CEST) — Central Europe
8. Type your own (e.g., "Asia/Singapore")

Reply with the number or type your timezone.
```
Wait for user response. If they provide a number, map it to the timezone. If they type a custom timezone, use it directly.

Save the timezone as `{TIMEZONE}`.

## Step 3: Platform Selection

Ask the user in chat:
```
Which social media platforms do you want to create content for? Select one or more (reply with numbers, comma-separated):

1. LinkedIn — Professional post, article teaser, or carousel idea
2. Twitter/X — Thread or single tweet
3. Instagram — Carousel, reel script, or story
4. Facebook — Post or event announcement
5. TikTok — Short video script (15-60s)
6. Reddit — Value-driven post or AMA outline
7. YouTube — Short video script or community post

Example: "1,3,5" for LinkedIn, Instagram, and TikTok.
```
Wait for user response. Parse the selected platforms.

Save selected platforms as `{PLATFORMS}`.

## Step 4: Goal Selection

Ask the user in chat:
```
What is the primary goal for this social media content?

1. Brand Awareness — Increase visibility and reach new audiences
2. Engagement — Drive likes, comments, shares, and conversations
3. Lead Generation — Capture leads and drive signups or demos
4. Product Launch — Announce a new feature, product, or update
5. Education — Teach the audience something valuable
6. Community Building — Foster discussion and build a loyal following
7. Type your own goal

Reply with the number or describe your goal.
```
Wait for user response.

Save the goal as `{GOAL}`.

## Step 5: Derive Directory Name and Create Output

Derive a kebab-case directory name from `{TOPIC}`:
- Take the first 3-5 key words from the topic
- Convert to lowercase, hyphen-separated
- Example: "AI-powered code review tool for teams" → "ai-powered-code-review"

Run: mkdir -p "social-content/{DIRECTORY_NAME}"

Save the directory path as `{OUTPUT_DIR}`.

## Step 6: Launch Parallel Subagents

For each selected platform, launch a subagent using the Task tool. All subagent Task calls MUST be in a single message to run in parallel.

For each platform in `{PLATFORMS}`, call the Task tool with:

- subagent_type: general
- description: "Generate {PLATFORM} content"
- prompt: (see platform-specific prompts below)

### LinkedIn Subagent Prompt
```
You are a LinkedIn social media content writer. Generate LinkedIn content.

Context: {TOPIC}
Goal: {GOAL}
Timezone: {TIMEZONE}

Create a LinkedIn post and write it to: {OUTPUT_DIR}/linkedin.md

The file must include these sections in markdown:

# LinkedIn Content

## Hook (First Line)
Write an attention-grabbing opening line. On LinkedIn, only the first 1-2 lines are visible before "see more". Make it count. Use a bold statement, surprising stat, or provocative question.

## Full Post
Write the complete LinkedIn post (150-300 words). Use short paragraphs (1-2 sentences each). Include line breaks for readability. Write in a professional but conversational tone. Include a personal anecdote or lesson learned if relevant.

## Call to Action
Write a clear CTA aligned with the goal: {GOAL}

## Hashtags
Provide 3-5 relevant hashtags. Mix broad (#technology) with niche (#devtools).

## Visual Suggestions
Describe 1-2 image or carousel slide ideas that would complement the post.

## Best Time to Post
- **Recommended Days:** Tuesday, Wednesday, or Thursday
- **Recommended Times:** 8:00-10:00 AM or 12:00 PM
- **Timezone:** {TIMEZONE}
- **Notes:** Avoid weekends. LinkedIn engagement peaks mid-week during business hours. Reply to comments within the first 60 minutes to boost algorithmic reach.
```

### Twitter/X Subagent Prompt
```
You are a Twitter/X social media content writer. Generate Twitter/X content.

Context: {TOPIC}
Goal: {GOAL}
Timezone: {TIMEZONE}

Create a Twitter thread or single tweet and write it to: {OUTPUT_DIR}/twitter-x.md

The file must include these sections in markdown:

# Twitter/X Content

## Tweet Format
Choose the best format: Thread (5-8 tweets) for complex topics, or Single Tweet for punchy announcements. State which format you chose.

## Hook Tweet (Tweet 1)
Write the opening tweet. Must be compelling enough to stop the scroll. Use a bold claim, stat, or question. Include a thread indicator (1/N) if using thread format.

## Thread Body (Tweets 2 to N-1)
Each tweet should:
- Start with the tweet number (2/N, 3/N, etc.)
- Be under 280 characters
- Contain one clear idea
- Use line breaks for readability
- Include emojis sparingly (1-2 per tweet max)

## Closing Tweet (Last Tweet)
End with a strong CTA, summary, or call for engagement. Include relevant hashtags here only.

## Hashtags
Provide 2-3 hashtags for the closing tweet.

## Visual Suggestions
Describe any image, meme, or screenshot ideas for individual tweets.

## Best Time to Post
- **Recommended Days:** Monday through Friday
- **Recommended Times:** 9:00 AM or 12:00 PM
- **Timezone:** {TIMEZONE}
- **Notes:** Consistency matters more than perfect timing. Tweet at the same times to build audience habits. Engage with replies within 30 minutes for maximum visibility.
```

### Instagram Subagent Prompt
```
You are an Instagram social media content writer. Generate Instagram content.

Context: {TOPIC}
Goal: {GOAL}
Timezone: {TIMEZONE}

Create Instagram content and write it to: {OUTPUT_DIR}/instagram.md

The file must include these sections in markdown:

# Instagram Content

## Content Format
Choose the best format: Carousel (educational), Reel Script (entertaining/demonstrative), or Story Sequence (casual/behind-the-scenes). State which format you chose.

## Caption
Write the full caption (125-150 words). First line must hook. Use line breaks and emojis for readability. End with a question or CTA to drive comments.

## Carousel Slides (if applicable)
For each slide (aim for 5-10 slides):
- Slide X: [Title/Hook]
- [Content - keep to 2-3 lines per slide]
- Design notes: [colors, layout suggestions]

## Reel Script (if applicable)
Format as:
- Hook (0-3s): Attention grabber
- Problem (3-10s): What's the pain point
- Solution (10-45s): The main content
- CTA (45-60s): Follow/save/share prompt

## Hashtags
Provide 15-20 hashtags in three tiers:
- 3-5 broad (1M+ posts)
- 5-10 medium (100K-1M posts)
- 5-7 niche (<100K posts)

## Visual Suggestions
Describe the overall aesthetic, color palette, and imagery style.

## Best Time to Post
- **Recommended Days:** Tuesday through Friday
- **Recommended Times:** 11:00 AM - 1:00 PM
- **Timezone:** {TIMEZONE}
- **Notes:** Reels can perform well in evenings (7-9 PM). Use Stories to tease the post. Save-worthy content (carousels, infographics) gets pushed by the algorithm.
```

### Facebook Subagent Prompt
```
You are a Facebook social media content writer. Generate Facebook content.

Context: {TOPIC}
Goal: {GOAL}
Timezone: {TIMEZONE}

Create Facebook content and write it to: {OUTPUT_DIR}/facebook.md

The file must include these sections in markdown:

# Facebook Content

## Post Type
Choose the best format: Text Post (storytelling), Link Post (sharing a resource), or Event (if announcing). State which format you chose.

## Post Copy
Write the full post (100-250 words). Facebook allows longer copy but front-load the value. Use a conversational, community-oriented tone. Start with a hook that creates curiosity or emotion.

## Link Preview (if applicable)
Describe the ideal link preview: title, description, and featured image.

## Engagement Prompt
Include a question or poll to drive comments. Facebook prioritizes posts that generate discussion.

## Visual Suggestions
Describe the ideal image or video (Facebook favors native video over YouTube links).

## Best Time to Post
- **Recommended Days:** Wednesday through Friday
- **Recommended Times:** 1:00-3:00 PM
- **Timezone:** {TIMEZONE}
- **Notes:** Engagement drops on weekends. Facebook Groups often have different peak times. Native content (not links) gets higher reach.
```

### TikTok Subagent Prompt
```
You are a TikTok content writer. Generate TikTok video content.

Context: {TOPIC}
Goal: {GOAL}
Timezone: {TIMEZONE}

Create TikTok content and write it to: {OUTPUT_DIR}/tiktok.md

The file must include these sections in markdown:

# TikTok Content

## Video Concept
Brief description of the video concept and why it will perform.

## Script (15-60 seconds)

### Hook (0-3 seconds)
The most critical part. Must stop the scroll immediately. Use a pattern interrupt, bold claim, or visual surprise.

### Body (3-50 seconds)
Write the full script with:
- On-screen text suggestions
- Speaking lines (conversational, casual tone)
- Transition cues if applicable
- Keep sentences short and punchy

### CTA (Last 5-10 seconds)
"Follow for more", "Save this for later", or a question to drive comments.

## Trending Audio Suggestions
Suggest 2-3 types of trending sounds or original audio ideas.

## Hashtags
Provide 4-6 hashtags. Include 1-2 broad, 1-2 niche, and 1-2 trending.

## Visual Notes
Describe scene-by-scene visual direction, text overlays, and effects.

## Best Time to Post
- **Recommended Days:** Tuesday through Thursday
- **Recommended Times:** 2:00-5:00 PM (or 7:00-9:00 PM)
- **Timezone:** {TIMEZONE}
- **Notes:** TikTok takes 2-6 hours to push content to the FYP. Post before peak hours. Test different posting times and track in TikTok Analytics.
```

### Reddit Subagent Prompt
```
You are a Reddit content writer. Generate Reddit content.

Context: {TOPIC}
Goal: {GOAL}
Timezone: {TIMEZONE}

Create Reddit content and write it to: {OUTPUT_DIR}/reddit.md

The file must include these sections in markdown:

# Reddit Content

## Subreddit Suggestions
List 2-3 relevant subreddits where this content would fit. For each, describe the community vibe and rules to be aware of.

## Post Format
Choose the best format: Text Post (discussion/value), Link Post (sharing a resource), or AMA Outline. State which format you chose.

## Title
Write 3-5 title options. Reddit titles should be:
- Specific and descriptive
- Not clickbaity (Redditors hate this)
- Curiosity-driven without being misleading

## Post Body
Write the full post. Reddit values:
- Authenticity (share personal experience)
- Value (teach something, solve a problem)
- Formatting (use headers, lists, code blocks)
- Length: match the subreddit's norms (usually 200-800 words)

## Engagement Strategy
- Suggest how to respond to early comments
- Provide 2-3 follow-up comment ideas to keep the discussion going
- Note any subreddit rules about self-promotion

## Best Time to Post
- **Recommended Days:** Monday through Friday
- **Recommended Times:** 6:00-8:00 AM
- **Timezone:** {TIMEZONE}
- **Notes:** Reddit is timezone-sensitive. US-centric subreddits peak early morning EST. Check subreddit-specific traffic patterns. Upvotes in the first hour determine post visibility.
```

### YouTube Subagent Prompt
```
You are a YouTube content writer. Generate YouTube content.

Context: {TOPIC}
Goal: {GOAL}
Timezone: {TIMEZONE}

Create YouTube content and write it to: {OUTPUT_DIR}/youtube.md

The file must include these sections in markdown:

# YouTube Content

## Content Format
Choose the best format: YouTube Short (60s or under) or Community Post. State which format you chose.

## Title
Write 3-5 title options using proven formulas:
- "How to [achieve outcome] without [pain point]"
- "[Number] Things I Wish I Knew About [Topic]"
- "Why [Common Belief] Is Wrong About [Topic]"

## Description
Write the full video description (2-3 paragraphs). Include:
- Hook in the first 2 lines (visible before "Show More")
- Timestamps (if applicable)
- Keywords naturally woven in
- Links to related content

## Script Outline (Shorts)
For YouTube Shorts:
- Hook (0-5s): Pattern interrupt or curiosity gap
- Value (5-50s): Deliver the core content
- CTA (50-60s): Subscribe prompt or next video tease

## Community Post Copy (if applicable)
Write a community post that:
- Asks a question or runs a poll
- Shares a behind-the-scenes insight
- Teases upcoming content

## Tags
Provide 10-15 tags. Mix broad and specific keywords.

## Thumbnail Suggestions
Describe the ideal thumbnail: text overlay, facial expression, color scheme, composition.

## Best Time to Post
- **Recommended Days:** Thursday or Friday
- **Recommended Times:** 2:00-4:00 PM
- **Timezone:** {TIMEZONE}
- **Notes:** YouTube needs time to index. Post 2-3 hours before your audience's peak viewing time (typically evening). Shorts can be posted more frequently. Check YouTube Analytics for your specific audience peak hours.
```

## Step 7: Generate Summary

After ALL subagents complete, generate `{OUTPUT_DIR}/summary.md`:

```markdown
# Social Content Plan — {TOPIC_SLUG_TITLE}
**Generated:** {CURRENT_DATE}
**Goal:** {GOAL}
**Timezone:** {TIMEZONE}

## Content Overview

| # | Platform | Content Type | Best Day | Best Time | File |
|---|----------|-------------|----------|-----------|------|
(One row per platform with extracted info from each generated file)

## Posting Calendar

### Week 1
| Day | Time ({TIMEZONE}) | Platform | Content Summary |
|-----|-------------------|----------|-----------------|
(Distribute posts across the week based on best-time recommendations)

### Week 2 (if needed)
(If more than 5 platforms, spread remaining into week 2)

## Cross-Platform Tips
- Repurpose LinkedIn post into Twitter thread points
- Use carousel slides as talking points for TikTok/Reels
- Engagement in the first 60 minutes is critical for LinkedIn and Twitter
- Save-worthy content (carousels, infographics) performs well on Instagram
- Native content gets higher reach on Facebook than external links
```

## Step 8: Report Completion

After generating summary.md, report to the user:

- List all files created in `{OUTPUT_DIR}/`
- Show the posting calendar from summary.md
- Suggest the user review and customize each file before posting

## Error Handling

- **No context provided**: "No topic or context provided. Please describe what you want to create content about."
- **No platforms selected**: "No platforms selected. Please select at least one social media platform."
- **Directory creation failed**: "Failed to create output directory. Check file permissions."
- **Subagent failed**: Report which platform subagent failed and suggest re-running for that platform individually.
- **README not found**: "No README.md found in project root. Please provide context manually."
