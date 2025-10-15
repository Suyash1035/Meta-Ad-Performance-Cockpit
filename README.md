# Meta-Ad-Performance-Cockpit

Key KPIs and insights (216K impressions, 25.4K clicks, CTR 11.76%, 1.3K purchases, conversion & purchase rates, best ad types: Video & Stories, audience: females 18–30, timing: afternoons/evenings). Action: conversion optimization

Data model & data dictionary (concise) Star schema

Fact: ad_events — every interaction (Impression, Click, Share, Comment, Purchase). Use to derive Impressions, Clicks, CTR, Conversion Rate. Dimensions: ads (ad metadata, ad_type, platform), campaigns (budget, start/end dates), users (demographics: gender, age, country).

Key table fields (from Domain Knowledge Document): ad_events: event_id, ad_id, user_id, timestamp, event_type, day_of_week, time_of_day. ads: ad_id, campaign_id, ad_platform, ad_type, target_gender, target_age_group, target_interests. campaigns: campaign_id, name, start_date, end_date, total_budget. users: user_id, user_gender, user_age, age_group, country, location, interests.

Visualization & dashboard *Funnel chart: Impressions → Clicks → Conversions (with absolute numbers and % drop-off). (Explains the main problem: high awareness, low purchases). *Time series: Weekly & hourly engagement lines; annotate campaign launch days (19–21, 25–27 spikes). *Ad type comparison: Bar chart for CTR, conversion rate, engagement rate by ad_type (Video, Stories, Image, Carousel). *Audience donut / stacked bar: Gender, age-group breakdown (call out Females 18–30). *Geo map: Top countries (US, India, Brazil, Germany, UK) with volume vs estimated value.

Issues, tasks & roadmap (open as GitHub Issues)

*data: add sample CSVs and data schema

*etl: add script to load data into SQLite / Postgres

*sql: implement KPI & segmentation queries (complete and validate)

*dashboard: Power BI file / exported visuals

git add C:\Users\suyas\OneDrive\Pictures\Screenshots\dashboard.png git commit -m "Add Meta dashboard screenshot" git push origin main

*analysis: run cohort / retention or LTV analysis for purchasers

*optimization: plan AB tests for landing pages and retargeting flows

*docs: write methodology & assumptions
## 🛠 Skills
Power BI, Excel, SQL


## Screenshots

https://github.com/Suyash1035/Meta-Ad-Performance-Cockpit/blob/main/dashboard.png


## Deployment

To deploy this project run
*Useful SQL snippets

-- 01_kpis.sql
-- total impressions, clicks, purchases
SELECT 
  SUM(CASE WHEN event_type = 'Impression' THEN 1 ELSE 0 END) AS impressions,
  SUM(CASE WHEN event_type = 'Click' THEN 1 ELSE 0 END) AS clicks,
  SUM(CASE WHEN event_type = 'Purchase' THEN 1 ELSE 0 END) AS purchases,
  ROUND(100.0 * SUM(CASE WHEN event_type = 'Click' THEN 1 ELSE 0 END) / NULLIF(SUM(CASE WHEN event_type = 'Impression' THEN 1 ELSE 0 END),0), 2) AS ctr_pct,
  ROUND(100.0 * SUM(CASE WHEN event_type = 'Purchase' THEN 1 ELSE 0 END) / NULLIF(SUM(CASE WHEN event_type = 'Click' THEN 1 ELSE 0 END),0), 2) AS conv_rate_click_to_purchase_pct,
  ROUND(100.0 * SUM(CASE WHEN event_type = 'Purchase' THEN 1 ELSE 0 END) / NULLIF(SUM(CASE WHEN event_type = 'Impression' THEN 1 ELSE 0 END),0), 2) AS purchase_rate_pct
FROM ad_events;

*Breakout by ad type 

-- 02_by_adtype.sql
SELECT a.ad_type,
  SUM(CASE WHEN e.event_type='Impression' THEN 1 ELSE 0 END) AS impressions,
  SUM(CASE WHEN e.event_type='Click' THEN 1 ELSE 0 END) AS clicks,
  SUM(CASE WHEN e.event_type='Purchase' THEN 1 ELSE 0 END) AS purchases,
  ROUND(100.0 * SUM(CASE WHEN e.event_type='Click' THEN 1 ELSE 0 END) / NULLIF(SUM(CASE WHEN e.event_type='Impression' THEN 1 ELSE 0 END),0), 2) AS ctr_pct,
  ROUND(100.0 * SUM(CASE WHEN e.event_type='Purchase' THEN 1 ELSE 0 END) / NULLIF(SUM(CASE WHEN e.event_type='Click' THEN 1 ELSE 0 END),0), 2) AS conv_rate_click_to_purchase_pct
FROM ad_events e
JOIN ads a ON e.ad_id = a.ad_id
GROUP BY a.ad_type
ORDER BY impressions DESC;

*Time-of-day & hourly peaks:

-- 03_time_series.sql
SELECT date(timestamp) AS day,
       EXTRACT(hour FROM timestamp) AS hour,
       SUM(CASE WHEN event_type='Impression' THEN 1 ELSE 0 END) AS impressions,
       SUM(CASE WHEN event_type='Click' THEN 1 ELSE 0 END) AS clicks
FROM ad_events
GROUP BY 1,2
ORDER BY 1,2;

*Demographics:

-- 04_demographics.sql
SELECT u.user_gender,
       u.age_group,
       SUM(CASE WHEN e.event_type='Click' THEN 1 ELSE 0 END) AS clicks,
       SUM(CASE WHEN e.event_type='Purchase' THEN 1 ELSE 0 END) AS purchases,
       ROUND(100.0 * SUM(CASE WHEN e.event_type='Click' THEN 1 ELSE 0 END) / NULLIF(SUM(CASE WHEN e.event_type='Impression' THEN 1 ELSE 0 END),0), 2) AS ctr_pct
FROM ad_events e
JOIN users u ON e.user_id = u.user_id
GROUP BY u.user_gender, u.age_group
ORDER BY clicks DESC;
