# Global E-commerce Account & Email Engagement Analysis  

## Project Overview  
This project analyzes global account creation and email engagement activity in an e-commerce environment. Using **Google BigQuery** for data processing and **Looker Studio** for visualization, the project identifies the **top 10 countries** by account creation and email activity, providing insights into user engagement trends across different markets.  

The analysis covers:  
- Accounts created  
- Emails sent, opened, and clicked (visits)  
- Engagement by account properties (send interval, verification, unsubscribed status)  
- Country-level totals and rankings  

---

## Table of Contents  
- [Setup](#setup)  
- [Data Structure](#data-structure)  
- [Key Features](#key-features)  
- [Main Findings](#main-findings)  
- [Visualizations](#visualizations)  
 

---

## Setup  

### Prerequisites  
- Google BigQuery access  
- Looker Studio  

### Running the Query  
1. Open BigQuery console.  
2. Run the SQL script from [`final_task.sql`](final_task.sql).  
3. Connect the resulting dataset to Looker Studio.  
4. Use the provided dashboard template to replicate the visualizations.  

---

## Data Structure  
The analysis uses e-commerce datasets from BigQuery (`DA` schema), including:  
- `account`, `account_session`, `session`, `session_params`  
- `email_sent`, `email_open`, `email_visit`  

**Key Fields:**  
- `date` – account creation or email send date  
- `country` – extracted from session parameters  
- `send_interval`, `is_verified`, `is_unsubscribed` – account properties  
- `account_cnt` – number of accounts created  
- `sent_msg`, `open_msg`, `visit_msg` – email activity metrics  

---

## Key Features  
- **CTE + UNION query** to merge account and email datasets  
- **Country-level aggregation** with window functions for ranking  
- **Top 10 countries filter** for focused analysis  
- **Dynamic segmentation** by account properties  
- **Looker Studio dashboard** for interactive visual exploration  

---

## Main Findings  
- Certain countries dominate both account creation and email sending volume.  
- Engagement (opens, visits) varies significantly even within high-volume countries.  
- Verified accounts tend to show higher activity.  
- Unsubscribed users reduce engagement levels across markets.  

---

## Visualizations  
- **Bar charts**: top countries by accounts created and emails sent  
- **Line chart**: daily dynamics of emails sent  
- **Ranking tables**: country engagement rank by accounts and email activity  

👉 *Example screenshot of Looker Studio dashboard:*  
![Dashboard Screenshot](images/[Dashboard Screenshot](Screenshot%202025-09-18%20at%206.23.49%20PM.png)

---


