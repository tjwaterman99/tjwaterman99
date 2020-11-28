---
date: 2020-10-15
---

# Dimension tables and Fact tables

A common question that analysts have when they start working with a data warehouse is: what's the difference between a fact table and a dimension table?

Those labels often appear as abbreviations prefixed on a table's name, like `dim_users` or `fct_clicks`, or sometimes just `d_users` or `f_clicks`.

So if you ever want to create your own table in the warehouse, you'll need to decide which label to use.

Unfortunately, Google doesn't provide much help with that decision. Here's what the first results gives:

> **A cryptic definition:**
> The fact table contains business facts (or measures), and foreign keys which refer to candidate keys (normally primary keys) in the dimension tables. Contrary to fact tables, dimension tables contain descriptive attributes (or fields) that are typically textual fields (or discrete numbers that behave like text).

To be honest, I don't really understand that definition or how to apply it.

So I thought it would be helpful to provide an alternative explanation about fact and dimension tables which is more practical, and which always gives you the right answer.

The short answer is: a fact table contains records which will never change. Sometimes the data in a fact table "immutable" or "static."

That means that a fact table will never have someone run an `UPDATE` query on any of its records. 

So if you look at a specific record in a fact table, you are gauranteed to get back the same result every time, no matter whether you run the query today or a year from now.

```
# Always returns the same result, because 
# f_clicks is a fact table
select * from f_clicks where id=1
```

Records can be inserted into fact tables, but once a record has been inserted, it can never be updated. Records can also never be deleted from fact tables.

A dimension table, however, offers no gaurantee that the data it contains will never change. For example, you might be interested in the time that a user from the `dim_users` table last logged in.

```sql
select last_login_at from dim_users where id=1
'2020-09-15 12:34:56.987'
```

But maybe that user will log in again at a later date. Running that same query would then return a different result - the data for that record has changed.

```sql
select last_login_at from dim_users where id=1
'2020-09-24 09:08:07.543'
```

Sometimes people call the data in a dimension table "mutable" or "dynamic," in contrast to the "immutable" or "static" data in a fact table.

So to decide if your table is a dimension table or a fact table, you need to answer the question: will the data in my table ever change? 

If you're confident the data will never change, then it's a fact table. Common fact tables include historical records of events, like when a page was viewed, or when an order was placed.

But if the data in your table will change, then it's a dim table. Common dim tables include information about a user's account, or the contents of an article on their blog.
