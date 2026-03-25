# Reddit Post Virality Dataset

A dataset of 1,656 Reddit posts collected via the Reddit API (PRAW), designed for predicting post score (virality) using text features, timing, author metadata, and subreddit statistics.

---

## Overview

| Property | Value |
|---|---|
| Total posts | 1,656 |
| Subreddits | 12 |
| Features | 39 |
| Target variable | `log_score` (log1p of upvote score) |
| Task type | Regression / Classification |
| Collection method | Reddit API via PRAW |
| Collection date | March 2026 |

---

## Subreddits Included

| Subreddit | Category | Posts |
|---|---|---|
| r/AskReddit | Q&A, text | 12 |
| r/worldnews | News, links | 150 |
| r/todayilearned | TIL format | 150 |
| r/explainlikeimfive | ELI5 format | 150 |
| r/dataisbeautiful | Data visualization | 150 |
| r/personalfinance | Advice/discussion | 144 |
| r/tifu | Story format | 150 |
| r/science | Science links | 150 |
| r/Showerthoughts | Short creative titles | 150 |
| r/LifeProTips | Tips format | 150 |
| r/learnpython | Technical/niche | 150 |
| r/running | Hobby/niche | 150 |

> **Note:** r/AskReddit yielded only 12 posts because its "hot" feed is dominated by very recent posts, most of which were filtered out by the 24-hour minimum age requirement.

---

## Collection Methodology

Posts were collected from the **"hot" feed** of each subreddit using the PRAW Python library. The following filters were applied:

- Posts must be **at least 24 hours old** to ensure scores had stabilized before collection
- **Stickied moderator posts** were excluded
- Up to **150 posts per subreddit** were collected

Because data was sampled from the "hot" feed (which surfaces already-popular content), the dataset has an inherent **collection bias toward high-virality posts**. This should be accounted for in any analysis.

---

## Features

### Identifiers
| Column | Type | Description |
|---|---|---|
| `post_id` | string | Reddit post ID |
| `title` | string | Post title text |
| `subreddit` | string | Subreddit name |
| `post_type` | string | `self` (text post) or `link` |

### Target Variables
| Column | Type | Description |
|---|---|---|
| `score` | int | Raw upvote score |
| `log_score` | float | log1p(score) — primary regression target |
| `virality_category` | int | 0=low (<50), 1=medium (50–500), 2=high (≥500) |

### Engagement Features
| Column | Type | Description |
|---|---|---|
| `num_comments` | int | Total number of comments |
| `upvote_ratio` | float | Fraction of upvotes (0–1) |
| `is_original_content` | int | 1 if marked OC |
| `over_18` | int | 1 if NSFW |
| `has_flair` | int | 1 if post has a flair tag |

### Text Features
| Column | Type | Description |
|---|---|---|
| `title_length` | int | Character count of title |
| `title_word_count` | int | Word count of title |
| `body_length` | int | Character count of post body |
| `body_word_count` | int | Word count of post body |
| `has_body` | int | 1 if post has a text body |
| `num_question_marks` | int | Count of `?` in title + body |
| `num_exclamations` | int | Count of `!` in title + body |
| `num_urls` | int | Count of URLs in body |
| `title_uppercase_ratio` | float | Fraction of uppercase characters in title |
| `is_question_title` | int | 1 if title ends with `?` |

### Timing Features
| Column | Type | Description |
|---|---|---|
| `post_hour_utc` | int | Hour of posting (0–23, UTC) |
| `post_day_of_week` | int | Day of week (0=Monday) |
| `post_age_hours` | float | Hours between posting and collection |
| `created_utc` | float | Unix timestamp of post creation |

### Subreddit Features
| Column | Type | Description |
|---|---|---|
| `subreddit_subscribers` | int | Subscriber count at collection time |
| `subreddit_created_utc` | float | Unix timestamp of subreddit creation |
| `subreddit_age_days` | float | Age of subreddit in days |
| `subreddit_size_log` | float | log1p(subreddit_subscribers) |

### Author Features
| Column | Type | Description |
|---|---|---|
| `author_comment_karma` | int | Author's comment karma |
| `author_link_karma` | int | Author's link karma |
| `author_account_age_days` | float | Age of author account in days |
| `author_is_verified` | int | 1 if email-verified account |
| `author_is_gold` | int | 1 if Reddit Premium member |
| `author_karma_log` | float | log1p(comment_karma + link_karma) |

### Derived Features
| Column | Type | Description |
|---|---|---|
| `comments_per_hour` | float | num_comments / (post_age_hours + 1) |
| `score_per_hour` | float | score / (post_age_hours + 1) |
| `karma_to_age_ratio` | float | total_karma / (account_age_days + 1) |
| `title_body_ratio` | float | title_length / (body_length + 1) |

---

## Virality Category Distribution

| Category | Label | Score Range | Proportion |
|---|---|---|---|
| 0 | Low | < 50 | 41.9% |
| 1 | Medium | 50–500 | 23.6% |
| 2 | High | ≥ 500 | 34.5% |

The skew toward low and high virality (and underrepresentation of medium) reflects the hot-feed collection bias.
