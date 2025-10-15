# Meta-Ad-Performance-Cockpit
Key KPIs and insights (216K impressions, 25.4K clicks, CTR 11.76%, 1.3K purchases, conversion &amp; purchase rates, best ad types: Video &amp; Stories, audience: females 18–30, timing: afternoons/evenings). Action: conversion optimization 

Data model & data dictionary (concise)
Star schema

Fact: ad_events — every interaction (Impression, Click, Share, Comment, Purchase). Use to derive Impressions, Clicks, CTR, Conversion Rate. 
Dimensions: ads (ad metadata, ad_type, platform), campaigns (budget, start/end dates), users (demographics: gender, age, country). 

Key table fields (from Domain Knowledge Document): ad_events: event_id, ad_id, user_id, timestamp, event_type, day_of_week, time_of_day. 
ads: ad_id, campaign_id, ad_platform, ad_type, target_gender, target_age_group, target_interests. 
campaigns: campaign_id, name, start_date, end_date, total_budget. 
users: user_id, user_gender, user_age, age_group, country, location, interests.

Visualization & dashboard
*Funnel chart: Impressions → Clicks → Conversions (with absolute numbers and % drop-off). (Explains the main problem: high awareness, low purchases).
*Time series: Weekly & hourly engagement lines; annotate campaign launch days (19–21, 25–27 spikes). 
*Ad type comparison: Bar chart for CTR, conversion rate, engagement rate by ad_type (Video, Stories, Image, Carousel). 
*Audience donut / stacked bar: Gender, age-group breakdown (call out Females 18–30). 
*Geo map: Top countries (US, India, Brazil, Germany, UK) with volume vs estimated value.

Issues, tasks & roadmap (open as GitHub Issues)

*data: add sample CSVs and data schema

*etl: add script to load data into SQLite / Postgres

*sql: implement KPI & segmentation queries (complete and validate)

*dashboard: Power BI file / exported visuals

git add C:\Users\suyas\OneDrive\Pictures\Screenshots\dashboard.png
git commit -m "Add Meta dashboard screenshot"
git push origin main


*analysis: run cohort / retention or LTV analysis for purchasers

*optimization: plan AB tests for landing pages and retargeting flows

*docs: write methodology & assumptions
