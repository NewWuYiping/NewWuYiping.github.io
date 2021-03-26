---
layout: post
title: "mysql 操作记录"
date:   2017-12-22 13:00:59 +0800f
categories: [cate1, cate2]
description: some word here
keywords: keyword1, keyword2
---

### 大表加索引
```
-- 1. 准备
CREATE TABLE user_login_log_tmp LIKE user_login_log;
ALTER TABLE `user`.`user_login_log_tmp` ADD INDEX `idx_device`(`device`) USING BTREE;

-- 2. 切换
RENAME TABLE user_login_log TO user_login_log_1, user_login_log_tmp TO user_login_log;
ALTER TABLE `user`.`user_login_log_1` ADD INDEX `idx_device`(`device`) USING BTREE;

-- 3. 还原
RENAME TABLE user_login_log TO user_login_log_tmp, user_login_log_1 TO user_login_log;
INSERT INTO user_login_log(app_id, user_id, ip, device, account_type, app_key, lang, gmt_create, gmt_modify, sign_type, advert_aaid, is_register, campaign, ad_partner) SELECT app_id, user_id, ip, device, account_type, app_key, lang, gmt_create, gmt_modify, sign_type, advert_aaid, is_register, campaign, ad_partner FROM user_login_log_tmp;
```

### 查询 SlowSQL
```
SELECT *
FROM (SELECT query_time, rows_examined, db, CONVERT(sql_text USING utf8) as sql_a FROM mysql.slow_log) abc
WHERE sql_a like 'select%';
```