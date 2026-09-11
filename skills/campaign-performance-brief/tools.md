# Kampalo MCP tools

Server id: `kampalo`. Source: `backend/marketing/agents/suites.py` and `tool_backend.py`.

Common args on most tools: `user_id`, `user_email`, `client_id`, `days`, `date_from`, `date_to`, `range_preset`, `limit`.

## Overview

| Tool | Purpose |
| --- | --- |
| `overview_get_account` | Connected Google/Meta/SEO resource counts |
| `meta_insights_get_overview` | Account overview for insights context |
| `meta_insights_compare_platforms` | Google vs Meta ads comparison |

## Google Ads

| Tool | Purpose | Extra args |
| --- | --- | --- |
| `google_ads_get_summary` | Rolled-up totals | `activity_only` |
| `google_ads_get_campaigns` | Per-campaign metrics | `campaign_name`, `sort_by` (`spend` \| `roas` \| `conversions` \| `name`), `activity_only` |
| `google_ads_get_trends` | Spend/clicks/conversion trends | |

## Meta Ads

| Tool | Purpose | Extra args |
| --- | --- | --- |
| `meta_ads_get_summary` | Rolled-up totals | `activity_only` |
| `meta_ads_get_campaigns` | Per-campaign metrics | `campaign_name`, `sort_by`, `activity_only` |

## Search / SEO

| Tool | Purpose | Extra args |
| --- | --- | --- |
| `search_get_metrics` | Search Console site metrics | `site_url` |
| `search_get_top_queries` | Top queries | |
| `search_get_lighthouse` | Lighthouse scores and CWV | `url` |
| `search_get_page_metadata` | On-page metadata issues | |
| `search_get_paid_organic` | Google Ads terms overlapping Search Console | |

## GA4

| Tool | Purpose | Extra args |
| --- | --- | --- |
| `analytics_get_ga4_metrics` | Traffic and conversions | `property_name` |
| `analytics_get_ai_referrals` | ChatGPT / Perplexity / Gemini referral traffic | `property_name` |
| `analytics_get_organic_roi` | GSC landing pages joined with GA4 | `property_name` |
| `analytics_get_checkout_funnel` | Add-to-cart / checkout / purchase | `property_name` |

## Meta organic

Call `meta_organic_get_overview` before page/IG tools if `page_id` / `ig_user_id` are unknown.

| Tool | Purpose | Extra args |
| --- | --- | --- |
| `meta_organic_get_overview` | Pages and linked Instagram accounts | |
| `meta_organic_get_page_insights` | Page reach/views/engagement/follows | `page_id` |
| `meta_organic_get_page_posts` | Recent Page posts | `page_id`, `limit` |
| `meta_organic_get_ig_insights` | IG account insights | `ig_user_id` |
| `meta_organic_get_ig_media` | Recent IG media | `ig_user_id`, `limit` |
