# ๐ฉโ๐ป ์จ๋ผ์ธ ๊ฐ์๋ฅผ ๋ฃ๊ณ  ํ์ต ๋ฐ ์ค์ตํ ๋ด์ฉ์ ์ ๋ฆฌํ์ต๋๋ค

โ

โ

# Yammer ์๊ฐ

- ์ฌ๋ด ์ปค๋ฎค๋์ผ์ด์, ํ์ ๋ฑ์ ํ  ์ ์๋ ๊ธฐ์์ฉ sns ์๋น์ค(Slack๊ณผ ์ ์ฌ)

<img src="./data/101.png" width="100%" align="left"></img>

โ

โ

# 1. Yammer ์ ์  ์ธ๊ฒ์ด์ง๋จผํธ ํ๋ฝ ์์ธ ๋ถ์

### [๋ฌธ์  ์ํฉ]

### 2014-08-04 ์ฃผ์ WAU๊ฐ 12.21% ๊ฐ์

(Active User: ๋ก๊ทธ์ธ ํ ์ ์ )

```sql
SELECT DATE_TRUNC('week', e.occurred_at) AS week,
       COUNT(DISTINCT e.user_id) AS weekly_activate_users
  FROM tutorial.yammer_events e
  WHERE e.event_type = 'engagement'
        AND e.event_name = 'login'
  GROUP BY 1
  ORDER BY 1;
```

<img src="./data/102.png" width="100%" align="left"></img>

โ

โ

### (1) ์ ๊ท๊ฐ์์๊ฐ ๊ฐ์ํ๋?

์ ๊ท๊ฐ์์๋ ๊ฐ์ ํ ๋ฐ๋์ ๋ก๊ทธ์ธ์ ํ๋ฏ๋ก WAU์ ์ํฅ์ ๋ผ์น  ์ ์์

(์ ๊ท๊ฐ์์: ์ต๊ทผ 3๊ฐ์ ์ด๋ด ๊ฐ์์)

### โ 8/4 ์ฃผ์ ์ ๊ท๊ฐ์์ ๊ฐ์. ์ดํ ์ด์  ์์ค์ผ๋ก ํ๋ณตํ๋ฉฐ ์ํญ ์ฆ๊ฐ

```sql
SELECT DATE_TRUNC('week', u.created_at) AS week,
       COUNT(u.user_id) AS all_new_users,
       COUNT(CASE WHEN activated_at IS NOT NULL THEN u.user_id ELSE NULL END) AS activated_new_users
  FROM tutorial.yammer_users u
  WHERE u.created_at BETWEEN '2014-06-01 00:00:00' AND '2014-08-31 23:59:59'
  GROUP BY 1
  ORDER BY 1;
```

<img src="./data/103.png" width="100%" align="left"></img>

โ

โ

### (2) ์ฝํธํธ ๋ถ์์ ํด๋ณด์

- ์ ์ ๋ฅผ ๊ฐ์์ฃผ๋ณ๋ก ๋๋์ด ๋ถ์

### โ 8/4 ์ฃผ์ 10+ week ์ ์ ์ ๋ก๊ทธ์ธ ํ์๊ฐ ๊ธ๊ฐํ๋ ๊ฒฝํฅ์ด ์์

```sql
SELECT DATE_TRUNC('week', z.login_date) AS "week",
       COUNT(DISTINCT CASE WHEN z.user_age >=70 THEN z.user_id ELSE NULL END) AS "10+ week",
       COUNT(DISTINCT CASE WHEN z.user_age >=63 AND z.user_age < 70 THEN z.user_id ELSE NULL END) AS "9 week",
       COUNT(DISTINCT CASE WHEN z.user_age >=56 AND z.user_age < 63 THEN z.user_id ELSE NULL END) AS "8 week",
       COUNT(DISTINCT CASE WHEN z.user_age >=49 AND z.user_age < 56 THEN z.user_id ELSE NULL END) AS "7 week",
       COUNT(DISTINCT CASE WHEN z.user_age >=42 AND z.user_age < 49 THEN z.user_id ELSE NULL END) AS "6 week",
       COUNT(DISTINCT CASE WHEN z.user_age >=35 AND z.user_age < 42 THEN z.user_id ELSE NULL END) AS "5 week",
       COUNT(DISTINCT CASE WHEN z.user_age >=28 AND z.user_age < 35 THEN z.user_id ELSE NULL END) AS "4 week",
       COUNT(DISTINCT CASE WHEN z.user_age >=21 AND z.user_age < 28 THEN z.user_id ELSE NULL END) AS "3 week",
       COUNT(DISTINCT CASE WHEN z.user_age >=14 AND z.user_age < 21 THEN z.user_id ELSE NULL END) AS "2 week",
       COUNT(DISTINCT CASE WHEN z.user_age >=7 AND z.user_age < 14 THEN z.user_id ELSE NULL END) AS "1 week",
       COUNT(DISTINCT CASE WHEN z.user_age < 7 THEN z.user_id ELSE NULL END) AS "Less than a week"
  FROM(
       SELECT u.user_id,
              EXTRACT(day FROM '2014-09-01' - u.activated_at) AS user_age,
              e.occurred_at AS login_date
         FROM tutorial.yammer_users u
         JOIN tutorial.yammer_events e
              ON u.user_id = e.user_id
         WHERE u.activated_at IS NOT NULL
               AND e.event_type = 'engagement'
               AND e.event_name = 'login'
      ) z
  GROUP BY 1
  ORDER BY 1;
```

<img src="./data/104.png" width="100%" align="left"></img>

โ

- ์ฌ์ฉ ๋๋ฐ์ด์ค๋ณ๋ก ๋๋์ด ๋ถ์

### โ 8/4 ์ฃผ์ ๋ชจ๋ฐ์ผ ๊ธฐ๊ธฐ ์ ์ (phone, tablet)์ ๋ก๊ทธ์ธ ํ์๊ฐ ๊ธ๊ฐ

```sql
SELECT DATE_TRUNC('week', occurred_at) AS week,
       COUNT(DISTINCT CASE WHEN e.device IN ('macbook pro','lenovo thinkpad','macbook air','dell inspiron notebook',
          'asus chromebook','dell inspiron desktop','acer aspire notebook','hp pavilion desktop','acer aspire desktop','mac mini')
          THEN e.user_id ELSE NULL END) AS computer,
       COUNT(DISTINCT CASE WHEN e.device IN ('iphone 5','samsung galaxy s4','nexus 5','iphone 5s','iphone 4s','nokia lumia 635',
       'htc one','samsung galaxy note','amazon fire phone') THEN e.user_id ELSE NULL END) AS phone,
        COUNT(DISTINCT CASE WHEN e.device IN ('ipad air','nexus 7','ipad mini','nexus 10','kindle fire','windows surface',
        'samsumg galaxy tablet') THEN e.user_id ELSE NULL END) AS tablet
  FROM tutorial.yammer_events e
 WHERE e.event_type = 'engagement'
       AND e.event_name = 'login'
  GROUP BY 1
  ORDER BY 1;
```

<img src="./data/105.png" width="100%" align="left"></img>

โ

โ

### (4) ์ด๋ฉ์ผ ๋ฐ์ดํฐ๋ฅผ ํ์ธํด๋ณด์

- ์ ์ ์ ๋ก๊ทธ์ธ์ ์ ๋ํ๋ reengagement email ๊ด๋ จ ๋ฐ์ดํฐ ํ์ธ

### โ 8/4 ์ฃผ์ ์ด๋ฉ์ผ ๋ด์ ๋ก๊ทธ์ธ ๋งํฌ๋ฅผ ํด๋ฆญํ๋ ํ์๊ฐ ๊ธ๊ฐ

```sql
SELECT DATE_TRUNC('week', occurred_at) AS week,
       COUNT(DISTINCT CASE WHEN action = 'sent_weekly_digest' THEN user_id ELSE NULL END) AS weekly_emails,
       COUNT(DISTINCT CASE WHEN action = 'sent_reengagement_email' THEN user_id ELSE NULL END) AS reengagement_email,
       COUNT(DISTINCT CASE WHEN action = 'email_open' THEN user_id ELSE NULL END) AS email_opens,
       COUNT(DISTINCT CASE WHEN action = 'email_clickthrough' THEN user_id ELSE NULL END) AS email_clickthroughs
  FROM tutorial.yammer_emails
  GROUP BY 1
  ORDER BY 1;
```

<img src="./data/106.png" width="100%" align="left"></img>

โ

- ์ด๋ฉ์ผ ์์  5๋ถ ๋ด๋ก ์ด๋ฉ์ผ์ ์คํํ๊ฑฐ๋ ๋งํฌ๋ฅผ ํด๋ฆญํ๋ ๋น์จ ํ์ธ

### โ 8/4 ์ฃผ์ digest email ๋ด์ ๋ก๊ทธ์ธ ๋งํฌ๋ฅผ ํด๋ฆญํ๋ ํ์๊ฐ ๊ธ๊ฐ

```sql
SELECT DATE_TRUNC('week', e1.occurred_at) AS week, -- ์ด๋ฉ์ผ ๋ณด๋ธ ์ฃผ
       COUNT(CASE WHEN e1.action = 'sent_weekly_digest' THEN e1.user_id ELSE NULL END) AS digest_email,
       COUNT(CASE WHEN e1.action = 'sent_weekly_digest' THEN e2.user_id ELSE NULL END) AS digest_email_open,
       COUNT(CASE WHEN e1.action = 'sent_weekly_digest' THEN e3.user_id ELSE NULL END) AS digest_email_click,
       COUNT(CASE WHEN e1.action = 'sent_reengagement_email' THEN e1.user_id ELSE NULL END) AS reengagement_email,
       COUNT(CASE WHEN e1.action = 'sent_reengagement_email' THEN e2.user_id ELSE NULL END) AS reengagement_email_open,
       COUNT(CASE WHEN e1.action = 'sent_reengagement_email' THEN e3.user_id ELSE NULL END) AS eengagement_email_click
  FROM tutorial.yammer_emails e1
  LEFT JOIN tutorial.yammer_emails e2
            ON e2.user_id = e1.user_id
               AND e2.action = 'email_open'
               AND e2.occurred_at BETWEEN e1.occurred_at AND ADDDATE(e1.occurred_at, INTERVAL 5 MINUTE)
  LEFT JOIN tutorial.yammer_emails e3
            ON e3.user_id = e1.user_id
               AND e3.action = 'email_clickthrough'
               AND e3.occurred_at BETWEEN e1.occurred_at AND ADDDATE(e1.occurred_at, INTERVAL 5 MINUTE)
  WHERE e1.occurred_at BETWEEN '2014-06-01' AND '2014-09-01'
        AND e1.action IN ('sent_weekly_digest', 'sent_reengagement_email')
  GROUP BY 1;
```

<img src="./data/107.png" width="100%" align="left"></img>

โ

# ๐ก ๋ถ์ ๊ฒฐ๊ณผ

### ๋ก๊ทธ์ธ ํ์ ๊ฐ์์ ๊ด๋ จ์ด ์๋ ๋ชจ๋ฐ์ผ ์ฑ & ์ด๋ฉ์ผ ๋ด ๋งํฌ์ ๊ด๋ จ๋ ๋ฌธ์  ํ์ ํ์

โ

โ

โ

# 2. Yammer publisher ๊ธฐ๋ฅ A/B ํ์คํธ ์ ํจ์ฑ ๋ถ์

### [๋ฌธ์  ์ํฉ] ์คํ ์ค๊ณ ๋ฐ ์งํ ํด์์ ๋ฌธ์ ๊ฐ ์์๋์ง ํ์ธ

6/1 ~ 6/30 ํ ๋ฌ๊ฐ publisher ๊ธฐ๋ฅ์ ๋ํ A/B Test ์งํ

๊ทธ๋ฃน๋ณ๋ก publisher์ old/new ๋ฒ์ ์ ๊ฐ๊ฐ ๋ธ์ถ

<img src="./data/108.png" width="100%" align="left"></img>
โ

์ดํ ๊ฒฐ๊ณผ test group์ ๋ฉ์ธ์ง ํฌ์คํ ํ์๊ฐ 1.5๋ฐฐ ๋์

```sql
SELECT cbu.experiment_group,
       COUNT(cbu.user_id) AS user,
       SUM(cbu.cnt_send_message) AS total,
       AVG(cbu.cnt_send_message) AS average
  FROM (
        SELECT u.user_id,
               ex.experiment_group,
               COUNT(e.user_id) AS cnt_send_message
          FROM tutorial.yammer_experiments ex
          INNER JOIN tutorial.yammer_users u
                     ON ex.user_id = u.user_id
          LEFT JOIN tutorial.yammer_events e
                    ON ex.user_id = e.user_id
                       AND e.occurred_at BETWEEN ex.occurred_at AND '2014-06-30 23:59:59'
                       AND e.event_name = 'send_message'
          WHERE experiment = 'publisher_update'
          GROUP BY 1, 2
          ) cbu -- cnt by user
  GROUP BY 1;
```

<img src="./data/109.png" width="100%" align="left"></img>

โ

โ

### (1) ๊ทธ๋ฃน๊ฐ ์ฐจ์ด๊ฐ ํต๊ณ์ ์ผ๋ก ์ ์๋ฏธํ์ง ํ์ธ

[๋๋ฆฝ 2ํ๋ณธ t๊ฒ์ (์์ธก๊ผฌ๋ฆฌ) ์คํ]

- T-stat = -7.6245

- p-value = 0.0000

### โ ๊ฐ ๊ทธ๋ฃน๊ฐ ํ๊ท ์ ์ฐจ์ด๊ฐ ํต๊ณ์ ์ผ๋ก ์ ์๋ฏธํจ

<img src="./data/110.png" width="100%" align="left"></img>

โ

โ

### (2) ๋ฉ์ธ์ง ํฌ์คํ ํ์๊ฐ ํ์คํธ์ ์ฑ๊ณต ์ฌ๋ถ๋ฅผ ์ ๋ฐ์ํ๋ ์งํ์ธ๊ฐ?

๋ค๋ฅธ ์งํ๋ ์ถ๊ฐ๋ก ํ์ธํด๋ณด์ - ๋ก๊ทธ์ธ ํ์ ๋น๊ต

### โ ๊ทธ๋ฃน๋ณ ํ๊ท  ๋ก๊ทธ์ธ ํ์์ ๋น๋(days) ๋ชจ๋ test group์ด ๋์

```sql
SELECT cbu.experiment_group,
       COUNT(cbu.user_id) AS user,
       SUM(cbu.cnt_login) AS total,
       AVG(cbu.cnt_login) AS average
  FROM (
        SELECT u.user_id,
               ex.experiment_group,
               COUNT(e.user_id) AS cnt_login
          FROM tutorial.yammer_experiments ex
          INNER JOIN tutorial.yammer_users u
                     ON ex.user_id = u.user_id
          LEFT JOIN tutorial.yammer_events e
                    ON ex.user_id = e.user_id
                       AND e.occurred_at BETWEEN ex.occurred_at AND '2014-06-30 23:59:59'
                       AND e.event_name = 'login'
          WHERE experiment = 'publisher_update'
          GROUP BY 1, 2
          ) cbu -- cnt by user
  GROUP BY 1;
```

<img src="./data/111.png" width="100%" align="left"></img>

โ

```sql
SELECT cbu.experiment_group,
       COUNT(cbu.user_id) AS user,
       SUM(cbu.cnt_login) AS total,
       AVG(cbu.cnt_login) AS average
  FROM(
      SELECT u.user_id,
             ex.experiment_group,
             COUNT(DISTINCT DATE_TRUNC('day', e.occurred_at)) AS cnt_login
        FROM tutorial.yammer_experiments ex
        INNER JOIN tutorial.yammer_users u
                   ON ex.user_id = u.user_id
        LEFT JOIN tutorial.yammer_events e
                  ON ex.user_id = e.user_id
                     AND e.occurred_at BETWEEN ex.occurred_at AND '2014-06-30 23:59:59'
                     AND e.event_name = 'login'
        WHERE experiment = 'publisher_update'
        GROUP BY 1, 2
        ) cbu -- cnt by user
  GROUP BY 1;
```

<img src="./data/112.png" width="100%" align="left"></img>

โ

โ

### (3) ์ํ๋ง์ด ๋๋คํ๊ฒ ์ ์ด๋ฃจ์ด์ก๋๊ฐ?

### โ 6์์ ์ ๊ท ๊ฐ์ํ ์ ์ ๊ฐ ๋ชจ๋ control group์ ๋ฐฐ์ ๋๋ ์ค๋ฅ

```sql
SELECT DATE_TRUNC('month', u.activated_at) AS month,
       COUNT(CASE WHEN ex.experiment_group = 'control_group' THEN u.user_id ELSE NULL END) AS control_group,
       COUNT(CASE WHEN ex.experiment_group = 'test_group' THEN u.user_id ELSE NULL END) AS test_group
  FROM tutorial.yammer_experiments ex
  INNER JOIN tutorial.yammer_users u
             ON ex.user_id = u.user_id
  GROUP BY 1
  ORDER BY 1;
```

<img src="./data/113.png" width="100%" align="left"></img>

โ

โ

### (4) 6์ ์ ๊ท๊ฐ์์๋ฅผ ์ ์ธํ๊ณ  ๋ค์ ๋ถ์ํด๋ณด์

### โ ๊ทธ๋๋ test group์ ๋ฉ์ธ์ง ํฌ์คํ ํ์๊ฐ 1.4๋ฐฐ ๋์ผ๋ฉฐ ํต๊ณ์ ์ผ๋ก๋ ์ ์๋ฏธํจ

```sql
SELECT cbu.experiment_group,
       COUNT(cbu.user_id) AS user,
       SUM(cbu.cnt_send_message) AS total,
       AVG(cbu.cnt_send_message) AS average
  FROM (
        SELECT u.user_id,
               ex.experiment_group,
               COUNT(e.user_id) AS cnt_send_message
          FROM tutorial.yammer_experiments ex
          INNER JOIN tutorial.yammer_users u
                     ON ex.user_id = u.user_id
                        AND u.activated_at <= '2014-05-31 23:59:59'
          LEFT JOIN tutorial.yammer_events e
                    ON ex.user_id = e.user_id
                       AND e.occurred_at BETWEEN ex.occurred_at AND '2014-06-30 23:59:59'
                       AND e.event_name = 'send_message'
          WHERE experiment = 'publisher_update'
          GROUP BY 1, 2
          ) cbu -- cnt by user
  GROUP BY 1;
```

<img src="./data/114.png" width="100%" align="left"></img>

โ

[๋๋ฆฝ 2ํ๋ณธ t๊ฒ์ (์์ธก๊ผฌ๋ฆฌ) ์คํ]

- T-stat = -5.5266

- P-value = 0.0000

<img src="./data/115.png" width="100%" align="left"></img>

โ

โ

# ๐ก ๋ถ์ ๊ฒฐ๊ณผ

### ์ ๊ท๊ฐ์์๊ฐ ๋ชจ๋ control group์ ๋ฐฐ์ ๋๋ ์ํ๋ง ์ค๋ฅ๊ฐ ์์์

### ์ ๊ท๊ฐ์์๋ฅผ ์ ์ธํ๊ณ  ์คํ ์ฌ์งํ โ test group์ ๋ฉ์์ง ํฌ์คํ ํ์๊ฐ ๋ ๋์
