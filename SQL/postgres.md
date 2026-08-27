| دستور                   | کاربرد                                 |
| ----------------------- | -------------------------------------- |
| `sudo -u postgres psql` | ورود به PostgreSQL با کاربر `postgres` |
| `CREATE DATABASE shop;` | ساخت دیتابیس جدید                      |
| `\l`                    | نمایش لیست دیتابیس‌ها                  |
| `\c shop`               | اتصال به دیتابیس `shop`                |
| `CREATE TABLE ...`      | ساخت جدول                              |
| `\dt`                   | نمایش جدول‌های دیتابیس فعلی            |
| `\d products`           | نمایش ساختار جدول `products`           |
| `SERIAL`                | ساخت عدد افزایشی خودکار                |
| `PRIMARY KEY`           | تعیین کلید اصلی و یکتا                 |
| `VARCHAR(200)`          | ذخیره متن با حداکثر ۲۰۰ کاراکتر        |
| `DECIMAL(10,2)`         | ذخیره عدد اعشاری دقیق                  |
| `INTEGER`               | ذخیره عدد صحیح                         |
| `BOOLEAN`               | ذخیره `TRUE/FALSE`                     |
| `TIMESTAMP`             | ذخیره تاریخ و زمان                     |
| `CREATE`                | شروع دستور ایجاد یک Object             |
| `TABLE`                 | مشخص می‌کند Object موردنظر جدول است    |

---
---
## DATA TYPES
---
### character
- CHAR(5): store to max number characters
- VARCHAR: any length of char
- VARCHAR(50): 50 length of char
- TEXT: any length of char
---
### serial
- SMALLSWRIAL:  1 to 32767
- SERIAL: 1 to 2147483647
- BIGSERIAL: 1 to ...
---
### integer 
- SMALLINT: -32768 to 32767
- INTEGER: -2147583648 to 2147583647
- BITINT: -... to ...
---
### floats
- DECIMAL: exact number, fixed precision
- NUMERIC: same as DECIMAL, exact number
- REAL: 1E-37 to 1E37 (6 decimal digits of precision)
- DOUBLE PRECISION: 1E-307 to 1E308 (15 decimal digits of precision)
- FLOAT(p): floating-point number, precision depends on `p`
- FLOAT(1-24): same as REAL
- FLOAT(25-53): same as DOUBLE PRECISION
---
- BOOLEAN
- TIME
- DATE
- TIMESTAMP
- INTERVAL: durations of time
- ---
- ---
---
## table creatione template:
```
CREATE TABLE customer(
first_name VARCHAR(30) NOT NULL,
last_name VARCHAR(30) NOT NULL,
email VARCHAR(60) NOT NULL,
company VARCHAR(60) NOT NULL,
street VARCHAR(50) NOT NULL,
city VARCHAR(50) NOT NULL,
state CHAR(2) NOT NULL,
zip SMALLINT NOT NULL,
phone VARCHAR(11) NOT NULL,
birth_date DATE NULL,
sex CHAR(1) NOT NULL,
date_entered TIMESTAMP NOT NULL,
id SERIAL PRIMARY KEY);
```
---
## data insert template:
```
INSERT INTO customer(
    first_name,
    last_name,
    email,
    company,
    street,
    city,
    state,
    zip,
    phone,
    birth_date,
    sex,
    date_entered
)
VALUES (
    'Christopher',
    'Jones',
    'christopherjones@bp.com',
    'BP',
    '347 Cedar St',
    'Lawrenceville',
    'GA',
    '30044',
    '348-848-8291',
    '1938-09-11',
    'M',
    current_timestamp
);
```
---
## pulling data
```
SELECT * FROM public.customer
ORDER BY id ASC
```
---
## create data type
```
CREATE TYPE sex_type as enum
('M', 'F');
```
---
## update table & add data type
```
ALTER TABLE customer
ALTER COLUMN sex TYPE sex_type USING sex::sex_type;
```
- **`ALTER TABLE customer`**: این دستور پایه‌ای برای تغییر ساختار یک جدول موجود است. در اینجا به دیتابیس اعلام می‌کنید که قصد دارید تغییراتی روی جدول `customer` اعمال کنید.
- **`ALTER COLUMN sex TYPE sex_type`**: این بخش به دیتابیس می‌گوید که دقیقاً کدام ستون (`sex`) باید تغییر کند و نوع داده‌ی جدید آن باید چه چیزی (`sex_type`) باشد.
- **`USING sex::sex_type`**: این بخش (که در PostgreSQL بسیار پرکاربرد است) نحوه تبدیل داده‌های قدیمی به نوع جدید را مشخص می‌کند.
     زمانی که دیتابیس نتواند به‌طور ضمنی (Implicitly) نوع قبلی را به نوع جدید تبدیل کند، از کلمه‌ی کلیدی `USING` برای تعیین یک عبارت تبدیل استفاده می‌شود.

    - علامت `::` سینتکس اختصاصی PostgreSQL برای عمل تبدیل نوع (Type Casting) است و `sex::sex_type` دقیقاً معادل دستور استاندارد `CAST(sex AS sex_type)` عمل می‌کند. این یعنی: «داده‌های فعلی موجود در ستون `sex` را بخوان و آن‌ها را به ساختار نوع داده‌ی `sex_type` تبدیل کن».
---
---
---
---

|مفهوم|توضیح|
|---|---|
|Database|مجموعه‌ای از جداول و داده‌ها|
|Table|ساختار اصلی ذخیره داده|
|Row|یک رکورد از داده|
|Column|یک ویژگی از داده|
|Schema|فضای نام برای گروه‌بندی آبجکت‌ها|
|Primary Key|شناسه یکتا برای هر رکورد|
|Foreign Key|ارتباط بین دو جدول|
|Constraint|قانون محدودکننده داده|
|Index|ساختار افزایش سرعت جستجو|
|View|جدول مجازی ساخته‌شده از Query|
|Materialized View|View ذخیره‌شده روی Disk|
|Sequence|تولیدکننده شماره ترتیبی|
|Transaction|مجموعه عملیات اتمیک|
|Normalization|کاهش تکرار داده|
|Denormalization|افزایش سرعت با تکرار کنترل‌شده|

---

# 2) اتصال و مدیریت PostgreSQL (psql)

```bash
psql -U username
```

اتصال به PostgreSQL

```bash
psql -h localhost -U user dbname
```

اتصال به دیتابیس مشخص

```sql
\l
```

لیست دیتابیس‌ها

```sql
\c database_name
```

تغییر دیتابیس

```sql
\dt
```

نمایش جدول‌ها

```sql
\d table_name
```

نمایش ساختار جدول

```sql
\du
```

نمایش کاربران

```sql
\q
```

خروج از psql

---

# 3) Database Commands

## CREATE DATABASE

```sql
CREATE DATABASE shop;
```

ساخت دیتابیس جدید

---

## DROP DATABASE

```sql
DROP DATABASE shop;
```

حذف دیتابیس

---

## ALTER DATABASE

```sql
ALTER DATABASE shop RENAME TO store;
```

تغییر تنظیمات دیتابیس

---

# 4) Table Commands

## CREATE TABLE

```sql
CREATE TABLE users(
 id SERIAL PRIMARY KEY,
 name VARCHAR(100)
);
```

ساخت جدول

---

## DROP TABLE

```sql
DROP TABLE users;
```

حذف جدول

---

## ALTER TABLE

```sql
ALTER TABLE users ADD COLUMN age INT;
```

تغییر ساختار جدول

---

## RENAME COLUMN

```sql
ALTER TABLE users RENAME COLUMN name TO username;
```

تغییر نام ستون

---

## DROP COLUMN

```sql
ALTER TABLE users DROP COLUMN age;
```

حذف ستون

---

# 5) Data Types مهم PostgreSQL

|Type|کاربرد|
|---|---|
|INTEGER|عدد صحیح|
|BIGINT|عدد بزرگ|
|SMALLINT|عدد کوچک|
|SERIAL|ID خودکار|
|BIGSERIAL|ID بزرگ|
|VARCHAR|متن محدود|
|TEXT|متن آزاد|
|BOOLEAN|true/false|
|DATE|تاریخ|
|TIMESTAMP|تاریخ و زمان|
|UUID|شناسه یکتا|
|JSON|داده JSON|
|JSONB|JSON با قابلیت Index|
|ARRAY|آرایه|

---

# 6) CRUD Commands

## INSERT

```sql
INSERT INTO users(name)
VALUES('Ali');
```

افزودن داده

---

## SELECT

```sql
SELECT * FROM users;
```

خواندن داده

---

## UPDATE

```sql
UPDATE users SET name='Reza' WHERE id=1;
```

ویرایش داده

---

## DELETE

```sql
DELETE FROM users WHERE id=1;
```

حذف داده

---

## TRUNCATE

```sql
TRUNCATE users;
```

پاک کردن سریع کل جدول

---

# 7) Filtering Query

## WHERE

```sql
SELECT * FROM users WHERE age > 18;
```

شرط روی داده

---

## AND

```sql
WHERE age>18 AND active=true
```

چند شرط همزمان

---

## OR

```sql
WHERE role='admin' OR role='staff'
```

یکی از شروط

---

## IN

```sql
WHERE id IN(1,2,3)
```

بررسی چند مقدار

---

## BETWEEN

```sql
WHERE age BETWEEN 18 AND 30
```

محدوده

---

## LIKE

```sql
WHERE name LIKE 'Ali%'
```

جستجوی الگو

---

## ILIKE

```sql
WHERE name ILIKE 'ali%'
```

LIKE بدون حساسیت حروف

---

## IS NULL

```sql
WHERE deleted_at IS NULL
```

بررسی مقدار NULL

---

# 8) Sorting و Pagination

## ORDER BY

```sql
ORDER BY created_at DESC
```

مرتب‌سازی

---

## LIMIT

```sql
LIMIT 10
```

محدود کردن خروجی

---

## OFFSET

```sql
OFFSET 20
```

پرش روی رکوردها

---

# 9) Aggregate Functions

## COUNT

```sql
SELECT COUNT(*) FROM users;
```

تعداد رکورد

---

## SUM

```sql
SUM(price)
```

جمع مقادیر

---

## AVG

```sql
AVG(score)
```

میانگین

---

## MAX

```sql
MAX(age)
```

بیشترین مقدار

---

## MIN

```sql
MIN(age)
```

کمترین مقدار

---

# 10) GROUP BY / HAVING

## GROUP BY

```sql
SELECT role,COUNT(*) FROM users GROUP BY role;
```

گروه‌بندی داده

---

## HAVING

```sql
HAVING COUNT(*)>10
```

شرط روی گروه‌ها

---

# 11) JOIN ها

## INNER JOIN

```sql
SELECT *
FROM users
INNER JOIN orders
ON users.id=orders.user_id;
```

فقط داده‌های مشترک

---

## LEFT JOIN

```sql
LEFT JOIN orders
```

همه سمت چپ + مشترک‌ها

---

## RIGHT JOIN

```sql
RIGHT JOIN users
```

همه سمت راست + مشترک‌ها

---

## FULL JOIN

```sql
FULL JOIN users
```

تمام داده‌های هر دو طرف

---

## CROSS JOIN

```sql
CROSS JOIN products
```

ضرب دکارتی

---

# 12) Subquery

```sql
SELECT *
FROM users
WHERE id IN(
 SELECT user_id FROM orders
);
```

Query داخل Query

---

# 13) CTE

```sql
WITH active_users AS(
 SELECT * FROM users WHERE active=true
)
SELECT * FROM active_users;
```

Query موقت خواناتر

---

# 14) Window Functions

## ROW_NUMBER

```sql
ROW_NUMBER() OVER()
```

شماره‌دهی رکوردها

---

## RANK

```sql
RANK() OVER()
```

رتبه‌بندی

---

## PARTITION BY

```sql
OVER(PARTITION BY role)
```

گروه‌بندی در Window

---

# 15) Constraints

## PRIMARY KEY

```sql
id SERIAL PRIMARY KEY
```

کلید اصلی

---

## FOREIGN KEY

```sql
user_id INT REFERENCES users(id)
```

ارتباط جدول

---

## UNIQUE

```sql
email TEXT UNIQUE
```

جلوگیری از تکرار

---

## NOT NULL

```sql
name TEXT NOT NULL
```

اجباری بودن مقدار

---

## CHECK

```sql
age INT CHECK(age>0)
```

قانون سفارشی

---

# 16) Index ها

## CREATE INDEX

```sql
CREATE INDEX idx_name ON users(name);
```

ساخت Index برای سرعت

---

## UNIQUE INDEX

```sql
CREATE UNIQUE INDEX idx_email ON users(email);
```

Index یکتا

---

## DROP INDEX

```sql
DROP INDEX idx_name;
```

حذف Index

---

## EXPLAIN

```sql
EXPLAIN SELECT * FROM users;
```

نمایش Query Plan

---

## EXPLAIN ANALYZE

```sql
EXPLAIN ANALYZE SELECT * FROM users;
```

بررسی واقعی Performance

---

# 17) Transactions

## BEGIN

```sql
BEGIN;
```

شروع تراکنش

---

## COMMIT

```sql
COMMIT;
```

ثبت تغییرات

---

## ROLLBACK

```sql
ROLLBACK;
```

برگشت تغییرات

---

# 18) User و Permission

## CREATE USER

```sql
CREATE USER sajad;
```

ساخت کاربر

---

## CREATE ROLE

```sql
CREATE ROLE developer;
```

ساخت Role

---

## GRANT

```sql
GRANT SELECT ON users TO developer;
```

دادن دسترسی

---

## REVOKE

```sql
REVOKE SELECT ON users FROM developer;
```

گرفتن دسترسی

---

# 19) View

## CREATE VIEW

```sql
CREATE VIEW active_users AS SELECT * FROM users;
```

ساخت View

---

## DROP VIEW

```sql
DROP VIEW active_users;
```

حذف View

---

# 20) JSON در PostgreSQL

## استخراج JSON

```sql
data->>'name'
```

گرفتن مقدار JSON

---

## JSONB Index

```sql
CREATE INDEX idx ON table USING GIN(data);
```

سرعت JSON Query

---

# 21) Date و Time

```sql
NOW()
```

زمان فعلی

```sql
CURRENT_DATE
```

تاریخ امروز

```sql
AGE(date)
```

اختلاف تاریخ

```sql
DATE_TRUNC()
```

گرد کردن زمان

---

# 22) String Functions

```sql
LOWER()
```

کوچک کردن حروف

```sql
UPPER()
```

بزرگ کردن حروف

```sql
CONCAT()
```

اتصال رشته‌ها

```sql
LENGTH()
```

طول متن

```sql
SUBSTRING()
```

بخشی از متن

---

# 23) PostgreSQL مخصوص Backend

## UUID

```sql
CREATE EXTENSION pgcrypto;
```

فعال کردن UUID

---

## Generate UUID

```sql
gen_random_uuid()
```

ساخت UUID تصادفی

---

## Soft Delete Pattern

```sql
deleted_at TIMESTAMP
```

حذف منطقی

---

## Pagination Pattern

```sql
LIMIT 20 OFFSET 40
```

صفحه‌بندی

---

## Upsert

```sql
INSERT ... ON CONFLICT DO UPDATE
```

Insert یا Update

---

# 24) Backup و Restore

Backup:

```bash
pg_dump dbname > backup.sql
```

گرفتن بکاپ

Restore:

```bash
psql dbname < backup.sql
```

بازگردانی

---

# 25) Performance مهم

|دستور|کاربرد|
|---|---|
|EXPLAIN|تحلیل Query|
|EXPLAIN ANALYZE|تحلیل واقعی|
|VACUUM|پاکسازی فضای مرده|
|ANALYZE|بروزرسانی Statistics|
|REINDEX|بازسازی Index|
|pg_stat_activity|مشاهده Query های فعال|
|pg_stat_database|آمار Database|
