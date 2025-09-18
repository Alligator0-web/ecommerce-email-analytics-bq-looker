# ecommerce-email-analytics-bq-looker
BigQuery SQL (CTE + UNION) dataset for e-commerce account &amp; email activity (accounts created, sent/open/visit), function ranks. 



CREATE OR REPLACE VIEW Students.Khv_module_task AS
WITH


msg_cte AS (
 SELECT
   DATE_ADD(s.date, INTERVAL es.sent_date DAY) AS event_date,
   sp.country                                   AS country,
   acc.send_interval                            AS send_interval,
   acc.is_verified                              AS is_verified,
   acc.is_unsubscribed                          AS is_unsubscribed,
   0                                            AS account_cnt,
   COUNT(DISTINCT es.id_message)                AS sent_msg,
   COUNT(DISTINCT eo.id_message)                AS open_msg,
   COUNT(DISTINCT ev.id_message)                AS visit_msg
 FROM `data-analytics-mate.DA.email_sent`       es
 LEFT JOIN `data-analytics-mate.DA.email_visit` ev USING(id_message)
 LEFT JOIN `data-analytics-mate.DA.email_open`  eo USING(id_message)
 LEFT JOIN `data-analytics-mate.DA.account_session` acs
   ON es.id_account = acs.account_id
 JOIN `data-analytics-mate.DA.account` acc
   ON es.id_account = acc.id
 JOIN `data-analytics-mate.DA.session` s
   ON acs.ga_session_id = s.ga_session_id
 JOIN `data-analytics-mate.DA.session_params` sp
   ON acs.ga_session_id = sp.ga_session_id
 GROUP BY 1, 2, 3, 4, 5
),




acct_cte AS (
 SELECT
   s.date                                       AS event_date,
   sp.country                                   AS country,
   acc.send_interval                            AS send_interval,
   acc.is_verified                              AS is_verified,
   acc.is_unsubscribed                          AS is_unsubscribed,
   COUNT(DISTINCT acs.account_id)               AS account_cnt,
   0                                            AS sent_msg,
   0                                            AS open_msg,
   0                                            AS visit_msg
 FROM `data-analytics-mate.DA.account_session`  acs
 JOIN `data-analytics-mate.DA.account` acc
   ON acs.account_id = acc.id
 JOIN `data-analytics-mate.DA.session` s
   ON acs.ga_session_id = s.ga_session_id
 JOIN `data-analytics-mate.DA.session_params` sp
   ON acs.ga_session_id = sp.ga_session_id
 GROUP BY 1, 2, 3, 4, 5
),




union_cte AS (
 SELECT * FROM msg_cte
 UNION ALL
 SELECT * FROM acct_cte
),




agg_cte AS (
 SELECT
   event_date,
   country,
   send_interval,
   is_verified,
   is_unsubscribed,
   SUM(account_cnt) AS account_cnt,
   SUM(sent_msg)    AS sent_msg,
   SUM(open_msg)    AS open_msg,
   SUM(visit_msg)   AS visit_msg
 FROM union_cte
 GROUP BY event_date, country, send_interval, is_verified, is_unsubscribed
),




country_cte AS (
 SELECT
   a.*,
   SUM(account_cnt) OVER (PARTITION BY country) AS total_country_account_cnt,
   SUM(sent_msg)    OVER (PARTITION BY country) AS total_country_sent_cnt
 FROM agg_cte a
),




rank_cte AS (
 SELECT
   c.*,
   DENSE_RANK() OVER (ORDER BY total_country_account_cnt DESC) AS rank_total_country_account_cnt,
   DENSE_RANK() OVER (ORDER BY total_country_sent_cnt DESC)     AS rank_total_country_sent_cnt
 FROM country_cte c
)




SELECT *
FROM rank_cte
WHERE rank_total_country_account_cnt <= 10
  OR rank_total_country_sent_cnt <= 10;
