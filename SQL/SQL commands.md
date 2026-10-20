## database commands

- SHOW databases;
- CREATE DATABASES <name>;
- DROP DATABASE <name>;
- USE <database name>;

## table commands

- SHOW TABLES;
- DESCRIBE <table name>;
it show table fields & details
- CREATE TABLE <table name>();
    - (id int NOT NULL)
- ALTER TABLE <name>
- CHECK (condition);
it let us to set condition
- DROP TABLE <name>;
- TRUNCATE TABLE <name>;

## data in table commands
- INSERT INTO <table> (column1, column2, ...) VALUES (value1, value2, ...);
- INSERT INTO <table> VALUES (value1, value2, ...);
- DELETE FROM <table-name> WHERE <condition>;
- UPDATE <table-name> SET column1=value1, colume2=value2, ..., WHERE <condition>;
- SELECT columns FROM <table-name> WHERE <condition>;