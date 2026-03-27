# Reddit Post Virality Dataset

## Dataset Overview
This dataset contains analytics data for 1,656 Reddit posts collected from the Reddit API, including engagement metrics, text features, author statistics, subreddit characteristics, and virality indicators.

### Dataset Shape
- Records: 1,656 rows (posts)
- Features: 39 columns
- Format: CSV

## Data Description

### Features
| Column Name | Data Type | Description |
|-------------|-----------|-------------|
| post_id | string | Unique Reddit identifier for the post |
| title | string | Post title text |
| subreddit | string | Subreddit the post was submitted to |
| post_type | string | Type of post: `self` (text) or `link` |
| score | integer | Raw upvote score at time of collection |
| log_score | float | log1p(score) — primary regression target |
| num_comments | integer | Total number of comments on the post |
| upvote_ratio | float | Fraction of upvotes vs total votes (0–1) |
| is_original_content | integer | Binary indicator if post is marked as OC (0/1) |
| over_18 | integer | Binary indicator if post is marked NSFW (0/1) |
| has_flair | integer | Binary indicator if post has a flair tag (0/1) |
| title_length | integer | Character count of the post title |
| title_word_count | integer | Word count of the post title |
| body_length | integer | Character count of the post body |
| body_word_count | integer | Word count of the post body |
| has_body | integer | Binary indicator if post has a text body (0/1) |
| num_question_marks | integer | Count of `?` characters in title and body |
| num_exclamations | integer | Count of `!` characters in title and body |
| num_urls | integer | Count of URLs in the post body |
| title_uppercase_ratio | float | Fraction of uppercase characters in the title |
| is_question_title | integer | Binary indicator if title ends with `?` (0/1) |
| post_hour_utc | integer | Hour of posting in UTC (0–23) |
| post_day_of_week | integer | Day of week at posting time (0=Monday, 6=Sunday) |
| post_age_hours | float | Hours elapsed between posting and collection |
| created_utc | float | Unix timestamp of post creation |
| subreddit_subscribers | integer | Subscriber count of the subreddit at collection time |
| subreddit_created_utc | float | Unix timestamp of subreddit creation |
| subreddit_age_days | float | Age of the subreddit in days |
| author_comment_karma | integer | Author's total comment karma |
| author_link_karma | integer | Author's total link karma |
| author_account_age_days | float | Age of the author's account in days |
| author_is_verified | integer | Binary indicator if author's email is verified (0/1) |
| author_is_gold | integer | Binary indicator if author has Reddit Premium (0/1) |
| comments_per_hour | float | Comment rate normalized by post age (num_comments / (post_age_hours + 1)) |
| score_per_hour | float | Score rate normalized by post age (score / (post_age_hours + 1)) |
| karma_to_age_ratio | float | Author's total karma divided by account age in days |
| subreddit_size_log | float | log1p(subreddit_subscribers) |
| author_karma_log | float | log1p(comment_karma + link_karma) |
| virality_category | integer | Virality category (0=low <50, 1=medium 50–500, 2=high ≥500) |

## Data Collection Methodology

### Source
Data was collected from the Reddit API using the PRAW (Python Reddit API Wrapper) library. The collection process involved:

1. **Subreddit Selection**: Posts were collected from twelve subreddits spanning diverse content categories:
   - AskReddit
   - worldnews
   - todayilearned
   - explainlikeimfive
   - dataisbeautiful
   - personalfinance
   - tifu
   - science
   - Showerthoughts
   - LifeProTips
   - learnpython
   - running

2. **Post Filtering**: Posts were filtered to include only those at least 24 hours old, ensuring scores had sufficient time to stabilize. Stickied moderator posts were excluded.

3. **Data Enrichment**: For each post, the following data was collected:
   - Post metadata (title, body, score, upvote ratio, comment count)
   - Subreddit-level statistics (subscriber count, subreddit age)
   - Author-level statistics (karma, account age, verification status)
   - Timing information (hour, day of week, post age at collection)

4. **Derived Metrics**: Additional features were calculated including:
   - Comment and score rates normalized by post age
   - Log-transformed follower and karma counts to reduce skew
   - Author karma-to-age ratio as a proxy for account activity
   - TF-IDF features extracted from post titles (50 components)

5. **Virality Classification**: Posts were assigned to virality categories based on score thresholds: low (<50), medium (50–500), and high (≥500).

## Potential Use Cases
1. Post virality prediction and content strategy analysis
2. Subreddit-specific performance benchmarking
3. Optimal posting time identification
4. Author reputation impact on post success
5. Text feature analysis for title optimization

## Data Preparation
The dataset underwent the following preparation steps:
1. Collection of raw metrics from the Reddit API
2. Calculation of derived metrics (engagement rates, log transformations, ratios)
3. Handling of missing values with appropriate defaults (deleted/suspended accounts set to zero)
4. Feature engineering based on engagement and platform knowledge
5. Virality categorization using score thresholds

## Limitations
- Collection bias: sampling from the "hot" feed overrepresents already-popular posts
- Upvote ratio and comments per hour are collected at the same time as the score, creating potential leakage if used for prospective prediction
- Growth dynamics are not captured — the dataset is a single snapshot per post
- AskReddit is underrepresented (12 posts) due to the age filter removing most hot posts
- Author karma values may be negative for shadowbanned or penalized accounts
