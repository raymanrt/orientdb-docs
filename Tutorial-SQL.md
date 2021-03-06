# SQL

Most NoSQL products have a custom query language. OrientDB focuses on standards when it comes to query languages. Instead of inventing "Yet Another Query Language", we started from the widely used and well understood SQL. We then extended it to support more complex graph concepts like Trees and Graphs. Why SQL? SQL ubiquitous in the database developer world, it is familiar. Also, it is more readable and concise than Map Reduce scripts.

### `SELECT`

To start, let's write a query that returns the same result as the previous `BROWSE CLUSTER OUser` and `BROWSE CLASS OUser`:

<pre>
orientdb> <code class="lang-sql userinput">SELECT FROM OUser</code>
</pre>

Starting from this simple query, we can notice 2 interesting things:

- This query has no projections. This stands for "the entire record" like using the star (*).
- OUser is a class. By default queries are executed against classes.

The target can also be:

- a **cluster**, by prefixing with "cluster:". Example: `SELECT FROM CLUSTER:OUser`
- one or more **Record IDs**, by using the [RecordID](Concepts.md#recordId) directly. For example: `SELECT FROM #10:3` or `SELECT FROM [#10:1, #10:3, #10:5]`
- an **index**, by prefixing with `INDEX:`. For example: `SELECT VALUE FROM INDEX:dictionary WHERE key = 'Jay'`

Similar to standard SQL, OrientDB supports WHERE conditions to filter the returning records by specifying one or more conditions. For example:

<pre>
orientdb> <code class="lang-sql userinput">SELECT FROM OUser WHERE name LIKE 'l%'</code>
</pre>

Returns all OUser records where the name starts with 'l'. For more information, look at all the supported operators and functions: [WHERE](SQL-Where.md).

OrientDB also supports the `ORDER BY` clause to order the result set by one or more fields. For example:

<pre>
orientdb> <code class="lang-sql userinput">SELECT FROM Employee WHERE city='Rome' ORDER BY surname ASC, name ASC</code>
</pre>

This will return all of the Employees who live in Rome, ordered by surname and name in ascending order. You can also use the `GROUP BY` clause to group results. For example:

<pre>
orientdb> <code class="lang-sql userinput">SELECT SUM(salary) FROM Employee WHERE age < 40 GROUP BY job</code>
</pre>

This returns the sum of the salaries of all the employees with age under 40 grouped by job type. To limit the result set you can use the `LIMIT` keyword. For example, to limit the result set to maximum of 20 items:

<pre>
orientdb> <code class="lang-sql userinput">SELECT FROM Employee WHERE gender='male' LIMIT 20</code>
</pre>

Thanks to the SKIP keyword you can easily manage pagination. Use SKIP to pass over records from the result set. For example, to divide the result set in 3 pages you could do something like:

<pre>
orientdb> <code class="lang-sql userinput">SELECT FROM Employee WHERE gender='male' LIMIT 20</code>
orientdb> <code class="lang-sql userinput">SELECT FROM Employee WHERE gender='male' SKIP 20 LIMIT 20</code>
orientdb> <code class="lang-sql userinput">SELECT FROM Employee WHERE gender='male' SKIP 40 LIMIT 20</code>
</pre>

Now that we have the basic skills to execute queries, let's discuss how to manage relationships.


### `INSERT`

OrientDB supports ANSI-92 syntax:

<pre>
orientdb> <code class="lang-sql userinput">INSERT INTO Employee(name, surname, gender)
          VALUES('Jay', 'Miner', 'M')</code>
</pre>

And the simplified:

<pre>
orientdb> <code class="lang-sql userinput">INSERT INTO Employee SET name='Jay', surname='Miner', gender='M'</code>
</pre>

Since OrientDB was created for the web, it can natively ingest JSON data:

<pre>
orientdb> <code class="lang-sql userinput">INSERT INTO Employee CONTENT</code> <code class="lang-json userinput">{name : 'Jay', surname : 'Miner',
          gender : 'M'}</code>
</pre>

### `UPDATE`

The ANSI-92 syntax is supported. Example:

<pre>
orientdb> <code class="lang-sql userinput">UPDATE Employee SET local=TRUE WHERE city='London'</code>
</pre>

Also using JSON with the "merge" keyword to merge the input JSON with current record:

<pre>
orientdb> <code class="lang-sql userinput">UPDATE Employee MERGE { local : TRUE } WHERE city='London'</code>
</pre>


### `DELETE`

This also respects the ANSI-92 compliant syntax:

<pre>
orientdb> <code class="lang-sql userinput">DELETE FROM Employee WHERE city <> 'London'
</pre>

