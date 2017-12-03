---
layout: post
title: Clarify SQL Exists
---

Learning SQL for Oracle for a week till now. 

The "Exists" is quite confusing, so I spent some time on it. Here're the conclusions. 

##### 1). Outside VS Inside

>  "You may resist using WHERE EXISTS because it has the awkward syntax of returning a value, which you always ignore." 

So it truly is awkward! I'm not the only person who is confused.

> "When you write a query using the IN clause, you're telling the rule-based optimizer that you want the inner query to drive the outer query (think: IN = inside to outside). 
>
> When you write EXISTS in a where clause, you're telling the optimizer that you want the outer query to be run first, using each value to fetch a value from the inner query (think: EXISTS = outside to inside)."  

Honestly I still don't quite understand the meaning of this explanation, but I'll just leave it open for now. 

##### 2). Rule of thumb

> "The general rule of thumb is that if the subquery contains a large volume of data, the EXISTS operator provides a better performance. 
>
> However, the query that uses the IN operator will perform faster if the result set returned from the subquery is very small." 

*Rule of thumb* definition, a general or approximate principle, procedure, or rule based on experience or practice, as opposed to a specific, scientific calculation or estimate. 

##### 3). RBO VS CBO

> "Basically, the RBO used a set of rules to determine how to execute a query. The biggest problem with the RBO was that it did not take the data distribution into account. So the [Cost-Based Optimizer (CBO)](http://searchoracle.techtarget.com/tip/Managing-the-cost-based-optimizer-in-E-Business-Suite) was born. The CBO uses statistics about the table, its indexes and the data distribution to make better informed decisions."

##### 4). Code demo

```sql
SELECT * FROM EMP e WHERE EXISTS (SELECT NULL FROM EMP WHERE mgr = 7839 ); --"exists" return true, so all the 14 rows retrieved.
SELECT * FROM emp e WHERE EXISTS (SELECT 0 FROM EMP M JOIN EMP E ON M.mgr = E.empno); --self join; retreive all 14 rows
SELECT * FROM emp e WHERE EXISTS (SELECT 0 FROM emp WHERE mgr != e.empno); --retreive all 14 rows
SELECT * FROM emp e WHERE EXISTS (SELECT 0 FROM emp WHERE mgr = e.empno); --Find out the employees who belongs to management; a related subquery
SELECT * FROM emp WHERE EMPNO IN (SELECT MGR FROM EMP); --same result as the above, but obviously this is much easier. so use this one on priority. 
```



Reference websites: 

https://www.techrepublic.com/article/oracle-tip-understand-the-difference-between-in-and-exists-in-subqueries/

http://searchoracle.techtarget.com/answer/Rule-based-vs-cost-based-optimization

https://stackoverflow.com/questions/8875355/how-does-exists-work-in-oracle-and-how-does-it-differ-from-in

https://stackoverflow.com/questions/24929/difference-between-exists-and-in-in-sql