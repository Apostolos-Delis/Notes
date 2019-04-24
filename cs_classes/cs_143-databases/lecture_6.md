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
    whom. If a user $U_i$ gave permission to $U_j$, then

