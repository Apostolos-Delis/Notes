## Lecture 6 - Authorization and Advanced SQL - 4/22/19

## Authorization

1. A database consists of one or more users, where there is always one root user who is
   the administrator. The other users represent other processes that access your data
2. Database Management Systems have different permissions that users can have access for
3. Some of the most basic privileges are
    * Reading things
    * Creating thing
    * Inserting data
    * Changing things
    * Deleting things
4. Authorization is based on a Postgres user name (role). In Unix for example, a common
   group is called staff and everyone in staff has the same base permissions
5. A user’so privileges include all privileges granted to them and all privileges
    granted to his/her/the process roles
6. A template for gaining privileges:

    ```sql
    GRANT
        list_of_privileges
    ON
        database_table_schema_view name
    TO
        user_or_role_list;
        -- comma separated
    ```
7. An example of giving a user the ability to see data in the scooter_status views is:

    ```sql
    GRANT SELECT ON bird.ucla_scooter TO appuser;
    ```
8. Can also revoke privileges from users:

    ```sql
    REVOKE ALL PRIVILEGES ON bart.entry_exit FROM station;
    ```
9. Can also decide to **Pass the torch** where the user is assigned the privilege to
   grant privileges to others:

   ```sql
   GRANT ALL PRIVILEGES ON *.*
        TO user_name WITH GRANT OPTION;
   ```
10. If you want to undo the previous transaction, can use the `REVOKE GRANT OPTION FOR`
    syntax

    ```sql
    REVOKE GRANT OPTION FOR <privilege> ON bart
        FROM user_name;
    ```
11. Note that the authorization graph keeps track of which users gave permissions to
    whom. If a user $U_i$ gave permission to $U_j$, then, if $U_i$ has their privileges
    revoked, then so will $U_j$, but if $U_j$ has recieved permissions from $U_k$, then
    it will keep its permissions
13. There are two actions we can take to revoke privileges from $U_i$:
    1. `CASCADE` revokes the privilege from $U_i$ and all direct and indirect descendants,
    2. `RESTRICT` throws an error if any direct or indirect descendants have the
        authorization. So you have to recursively go through the leafs of the graph and
        revoke privileges first.

## Applications

1. Connecting and using PostgresSQL consists of the following steps:
    * Connecting to the database using a driver
    * Constructing a query or prepared statement
    * Executing the query with a cursor
    * Iterating over the resultset using a cursor
    * Closing the connection
2. When formatting the SQL queries, it is always important to use string formatting in
   order to avoid SQL injection. So for python, use `s.format()` or `f"str"`
3. Notice that for example, we use the following piece of code:

    ```python
    query = """SELECT id, student_last_name,
                student_first_name,
                student_middle_name, grade
                WHERE id=""" + request_args['student_id']
    ```
    What happens if the user enters something like: `1; DROP DATABASE students;`?
4. The query for this then becomes:

    ```sql
    SELECT id,
        student_last_name,
        student_first_name, student_middle_name,
        grade WHERE id=1; DROP DATABASE students;
    ```
    This is known as SQL injection.
5. SQL injection can also be used to steal private data.

    ```python
    query = """SELECT
        uid,
        bol_username,
        bol_password
    FROM ucla_shibboleth
    WHERE bol_username='{}' AND ...""".format(user_input)
    ```
    Here the developer assumes that the user enters a valid username
6. But what if the user inputs `joebruin' OR 1=1; --`, leading to the code to become:

    ```sql
    SELECT
        uid,
        bol_username,
        bol_password
    FROM ucla_shibboleth
    WHERE bol_username='joebruin' OR 1=1; --' AND ...
    ```

#### SQL Injection and Prepared Statements

1. Instead, of using simple strings which are vulnerable to SQL injection, we should
    construct prepared statements which have two advantages:
    * They are faster when performing multiple queries of the same form.
    * Prevent SQL injection
2. Prepared statements are a way of creating query templates that can be used over
    and over again by simply creating a template, and then pop in the necessary values.
3. Here is an example query template that now lets me just pass in the proper data via
   api which is more efficient to code with:

   ```sql
   INSERT INTO table_name
        (Origin,Destination,DateTime,Throughput)
        VALUES (?,?,?,?)
            ON DUPLICATE KEY
            UPDATE Throughput = Throughput + 1;
   ```

4. Prepared statements prevent SQL injection by escaping user input, and treating it as a
   constant, so in the case of an attack the system would see:

   ```sql
   SELECT id,
        student_last_name,
        student_first_name,
        student_middle_name,
        grade
        WHERE id="1; DROP DATABASE students";
   ```

#### Putting it Together in Python

1. After connecting to the database, we can execute a query on a cursor. Then, the cursor
   provides traversal over the result
2. Note that it is important to close the connection when you are done since databases
   have a maximum number of concurrent users.
3. Here is an example of using the cursor in python:

    ```python
    import psycopg2

    connection = psycopg2.connect(user="some_user",
        password="the_p@55w0rd",
        host="localhost",
        port="5432",
        database="my_database")
    cursor = connection.cursor()

    major = "MATH" # or some user input
    db_query = """

    SELECT uid, last, first FROM students WHERE major={};
    """.format(major)

    cursor.execute(query)

    # Can also use fetchone or fetchmany and iterate
    row = cursor.fetchall()
    for result in row:
        uid, last_name, first_name = result
        print("Student ID: {}, Name: {} {}".format(uid, first_name, last_name))
    conn.close()
    ```

#### Caching

1. Caching involves storing the results of past lookups (or important
    lookups) in a system that allows very fast retrieval.
2. Instead of querying a database server over and over each time we
    load the Bird app, the database server can cache scooter locations
    in a very fast key-value store or even on the phone's cache
3. It is however important to also update the cache, perhaps a good routine is to update
   every 30 min or so
4. PostgreSQL and other RDBMS implement caching as well, if you run two consecutive
   identical queries, you will notice that the second one finishes much faster than the
   first

#### Logging

1. When developing long-lasting applications, it’s important to log as much as we can:
    * Login and logout, and authentication issues.
    * Impressions (views of content or pixels), interactions (clicks) and "successes"
      (conversions).
    * Visit path through the app or site.
    * Time spent on pages or screens.
    * Purchases
    * JavaScript and AJAX actions.
2. This information is often called metadata, but many times it can be just as important
   as actual payload data, since you can use it to view the state of the application and
   identify problems or bottlenecks
3. When logging, make sure to minimize private information in them, since if you get
   hacked you don't want that information leaked
4. Other things to do while logging to maintain privacy is:
    * Minimize the number of copies of private information in logs
    * Use a join key everywhere else, such as an integer or UUID
    * Hash super private data or just leave them out if they aren’t needed

## Review of SQL Functions

1. There are three ways to add new functions to SQL:
    1. Write a function with(out) arguments, with(out) a return value, as a SQL statement.
        * **Pro**: Easy to understand, and supports overloading.
        * **Con**: Slower than the other options, limited by declarative language
    2. Write a function in a procedural language like PL or pgSQL and compile it into a
       library. (dynamic loading)
        * **Pro**: Can be faster by allowing coder to specify how an algorithm is executed.
        * **Con**: Need to install (and perhaps distribute) object files. If the UDF
               interface in PostgreSQL changes, must be modified.
    3. Write an internal function that must be compiled into Postgres server. (static loading)
        * Pro: Fast!
        * Con: Have to modify Postgres server code.
2. Here is an example using the extended_roster table we used previously:

    ```sql
    CREATE TABLE extended_roster (
        uid             varchar(9) PRIMARY KEY,
        last_name       varchar(50) NOT NULL,
        first_name      varchar(50) NOT NULL,
        middle_name     varchar(50),
        class_level     char(3),
        major           varchar(255) NOT NULL,
        career          char(1) NOT NULL,
        midterm_score   smallint
    );
    ```
3. Now suppose we want to compute full names a lot using `CONCAT`, can put it in a
   function:

   ```sql
   CREATE FUNCTION name(varchar, varchar, varchar) RETURNS varchar AS '
        SELECT CONCAT($1,'', '', $2, COALESCE('', '' || $3, ''''));
   ' LANGUAGE SQL;

   -- To use
   SELECT name(last_name, first_name, middle_name) FROM extended_roster;
   ```
4. Can also do a very similar thing for class levels:

    ```sql
    CREATE FUNCTION classlevel(char) RETURNS varchar AS $$
        SELECT CASE ($1)
            WHEN 'USR' THEN 'Senior'
            WHEN 'UJR' THEN 'Junior'
            WHEN 'USO' THEN 'Sophomore'
            WHEN 'UFR' THEN 'Freshman'
            WHEN 'GD1' THEN 'PhD Student'
            WHEN 'GD2' THEN 'PhD Candidate'
            WHEN 'GMT' THEN 'Masters Student'
            WHEN 'CHA' THEN 'Chancellor Bossman'
            ELSE 'Unknown'
        END;
    $$ LANGUAGE SQL;
    ```

## Advanced SQL

#### Recursive Queries

1. Recursive queries allow us to extract recursive relationships, like all subordinates
   of a manager
2. The `WITH RECURSIVE` construct defines a recursive query. A
   recursive query has three parts:
    * The base set of results - the root of the search tree
    * Recursive term: one or more CTEs (Common Table Expressions) with a non-recursive
        term with `UNION` or `UNION ALL`
    * Termination check: when no more rows are returned.
3. An example is of the FOAF that was used a while back, find all subordinates of a
   manager with ID = 2:

    ```sql
    CREATE TABLE employees (
        employee_id serial PRIMARY KEY,
        full_name varchar NOT NULL,
    manager_id smallint
    );
   ```
4. Can then run a query using:

    ```sql
    WITH RECURSIVE subordinates AS (
        SELECT
            employee_id,
            manager_id,
            full_name
        FROM employees
        WHERE employee_id = 2
        UNION
        SELECT
            e.employee_id,
            e.manager_id,
            e.full_name
        FROM employees e
        INNER JOIN subordinates s ON s.employee_id = e.manager_id
    )
    SELECT employee_id, full_name FROM subordinates;
   ```
#### Window Functions

1. Window functions are similar to aggregation functions – but instead of
   collapsing a group, they take the order of the rows in the group into account.
2. These functions are used for:
    * Computing aggregates over rows in a group based on order;
    * Compute changes between rows in a group
3. For example, look at the chargebacks example from RIOT, previously a self-inner
   non-equijoin was used:
    
    ```sql
    SELECT
        l.trans_id,
        l.customer_id,
        l.datetime,
    SUM(r.result) AS prior_chargebacks
    FROM transactions l
    JOIN transactions r
    ON l.customer_id = r.customer_id AND
        r.datetime <= l.datetime
    GROUP BY l.customer_id, l.datetime, l.trans_id
    ORDER BY l.trans_id;
    ```
4. This could instead be done by a window function:
    We could instead use a window function:
    
    ```sql
    SELECT
        trans_id,
        customer_id,
        datetime,
        SUM(result) OVER (
            PARTITION BY customer_id
            ORDER BY datetime
        ) AS chargebacks
    FROM transactions
    ORDER BY trans_id;
    ```
