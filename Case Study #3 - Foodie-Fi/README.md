# Case Study #3 - Foodie-Fi 🥑▶️ 
<img src="https://8weeksqlchallenge.com/images/case-study-designs/3.png" width="500" alt="Foodie-Fi Logo">

## Introduction
Subscription based businesses are super popular and Danny realised that there was a large gap in the market - he wanted to create a new streaming service that only had food related content - something like Netflix but with only cooking shows!

Danny finds a few smart friends to launch his new startup Foodie-Fi in 2020 and started selling monthly and annual subscriptions, giving their customers unlimited on-demand access to exclusive food videos from around the world!

Danny created Foodie-Fi with a data driven mindset and wanted to ensure all future investment decisions and new features were decided using data. This case study focuses on using subscription style digital data to answer important business questions.

## Entity Relationship Diagram
<img src="/ERD/ERD-foodie.png" width="500" alt="Foodie Fi ERD">

## Business Rules
- Customers can choose which plans to join Foodie-Fi when they first sign up.
  
- Plans:
  - Trial: 7 day free trial with automatic conversion to pro monthly unless cancelled
  - Basic: Customers have limited access, can only stream their videos, and is only available monthly at $9.90.  
  - Pro: Customers have no watch time limits and are able to download videos for offline viewing. Pro plans start at $19.90 a month or $199 for an annual subscription.

- Customers can sign up to an initial 7 day free trial will automatically continue with the pro monthly subscription plan unless they cancel, downgrade to basic or upgrade to an annual pro plan at any point during the trial.

- Subscription change rules:
  - Upgrades: When customers upgrade their account from a basic plan to a pro or annual pro plan - the higher plan will take effect straightaway.
  - Downgrades/Cancellations: If customers downgrade from a pro plan or cancel their subscription - the higher plan will remain in place until the period is over.
  - Churn: When customers churn - they will keep their access until the end of their current billing period.
 
## Case Study Questions
- Customer Journey
- Data Analysis Questions
- Challenge Payment Question
- Outside The Box Questions

## Acknowledgments
- [8 Weeks SQL Challenge](https://8weeksqlchallenge.com/)
