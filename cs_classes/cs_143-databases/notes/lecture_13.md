# Lecture 13 - Multivalued Dependencies and Transactions 5/20/19

## Multivalued Dependencies (MVDs)

* Functional dependencies are only one way to decompose a relation into multiple
    normalized relations.
* MVDs express a condition among tuples of a relation $R$ that represents
    many-to-many relationships within a key.
    * When this happens, certain attributes become independent of one another, so their
        values appear in all combinations
* MVDs are an assertion that two sets of attributes are independent of one another, but
    appear in the same relation
* MVDs are a generalization of functional dependencies
* The most common example of MVDs is with a Drinkers relation

    ```sql
    Drinkers(name, address, emails, liquorsLiked)
    ```
* Each drinker has one name and address, but may have several emails and liquors they liked
* The set of email addresses and liquors are independent. There is nothing special about
    one email address and one liquor
* If a drinker has 3 emails and 10 liquors they liked, then the drinker has 30 tuples in
    this relation, where each email is repeated 10 times
* **Note**: name $\rightarrow$ addr is the only redundancy with respect to functional
    dependencies
* A **multivalued dependency** $\alpha\twoheadrightarrow\beta$ ($\alpha$ multidetermins
    $\beta$) is a constraint between two sets of attributes and is an assertion that if
    two tuples of a relation agree on all attributes of $\alpha$, then their components
    in $\beta$ may be swapped amongst themselves and the result will be two tuples also
    in the relation
* This just says that we have all combinations of components in $\beta$ in the tuple (in
    this case all email addresses paired with liquors, by person name), and that these
    components are independent of each other
* **Formal Definition of MVDs**: Let $r(R)$ be a relation, $\alpha \subseteq R$
    $\beta \subseteq R$. The MVD $\alpha\twoheadrightarrow\beta$ holds on $R$ if in any
    legal instance $r(R)$ for all pairs of tuples $t_1, t_2 \in r$ such that
    $t_1[\alpha] = t_2[\alpha]$, $\exists t_3, t_4 \in r$ such that
    1. $t_1[\alpha] = t_2[\alpha] = t_3[\alpha] = t_4[\alpha]$
    2. $t_3[\alpha] = t_1[\alpha]$
    3. $t_3[R-\beta] = t_2[R-\beta]$
    4. $t_4[\alpha] = t_2[\alpha]$
    5. $t_4[R-\beta] = t_1[R-\beta]$

* In the drinkers example, we had that name$\twoheadrightarrow$email, which means for
    each drinker name, the set of email addresses appears in conjunction with the set of
    liquors liked

#### Properties of MVDs

1. An MVD $\alpha\twoheadrightarrow\beta$ is trivial if $\beta \subseteq \alpha$ or
    $\alpha \cup \beta = R$
2. **Complementation**: If $\alpha\twoheadrightarrow\beta$, then
    $\alpha\twoheadrightarrow R -\beta$, meaning $\beta$ is a set independent of
    everything else in $R$
3. **Transitivity**: $\alpha\twoheadrightarrow\beta$, $\beta\twoheadrightarrow\gamma$,
    $\Longrightarrow$ $\alpha\twoheadrightarrow\gamma$
4. **Promotion**: Every FD is an MVD, so $\alpha\rightarrow\beta$ $\Longrightarrow$
    $\alpha\twoheadrightarrow\beta$
    * This is true since if the tuples agree on $\alpha$, then swapping values of $\beta$
        between tuples does not change the semantic meaning of the tuples
5. **Augmentation**: also holds as does coalescence and replication as part of
   Armstrong's Axioms
    $\alpha\twoheadrightarrow\beta$
6. Note that **Splitting** ($\alpha\rightarrow\beta\gamma \Longrightarrow$
    $\alpha\rightarrow\beta, \alpha\rightarrow\gamma$) does not hold, unlike with
    functional dependencies

#### Fourth Normal Form (4NF) and Higher Order Forms

* There is a lot of redundancy introduced by MVDs in the `Drinkers` example, we can use
    these MVDs to decompose the relation
* The functional dependency Name $\rightarrow$ Address also induces redundancy
* The fourth normal mode is essentially BCNF applied to MVDs
* A relation $R$ is 4NF for all MVDs in $F$ and $F^+$ if:
    1. $\alpha\twoheadrightarrow\beta$ is trivial, or
    2. $\alpha$ is a superkey for $R$
    3. $R$ must also be BCNF given any functional dependencies
* If a relation is not 4NF given a set of FDs and MVDs, we can decompose $R$ so that it
    is 4NF. First use all the FDs and normalize relations into BCNF, then once those have
    been applied, then for all the relations, check their MVDs, if an MVD
    $\alpha\twoheadrightarrow\beta$ is nontrivial and $\alpha$ is not a superkey, then
    decompose $R$ into $R_1(\alpha, \beta)$ and $R_2(\alpha, \Omega)$, where $\Omega$ is
    all the attributes in $R$ aside from $\alpha$ and $\beta$
* Note that very few relations in 4NF are also not 5NF, which means that every
    combination of $\beta$ is in the table.
* Tables in 5NF cannot be decomposed further without becoming lossy.
* The Sixth Normal Form (6NF) just means that every join dependency in $R$ is trivial
* Relations should be normalized to reduce redundancy and protect integrity during
    inserts updates and deletes
* It is possible to overnormalize and end up with sub-relations that require joins
    (expensive)
* 3NF is the only normal form that guarantees lossless decomposition that preserves
    dependencies
* Joins are expensive for read-only workloads. Start with normalized tables, and
    denormalize if we need better performance.

## Transactions

* A transaction is a group of operations on data that form one logical unit.
* In SQL this is represented with the following:

    ```sql
    BEGIN TRANSACTION
    ... # Data Transactions
    END TRANSACTION
    ```
* Transactions are indivisible (atomic), either executed entirely or not at all
* During a transaction, the RDBMS will start executing data operations, if at any point
    of failure occurs:
    * Data Integrity Failure
    * Constraint Failure
    * Other Errors
    * Disk Failure
    * System Outage

    The transaction stops and undoes all the operations it just did with a rollback
* Even a single SQL query performs multiple data operations, but it is assumed to be
    atomic.
* This get complicated with concurrent users executing groups of SQL queries concurrently
* RDBMS must take special care to ensure transactions operate correctly
* A transaction must maintain database consistency, we do not want to lose data,
    add extraneous data, or have data with improper values
* A database must maintain **ACID** compliant transactions, which means that the transactions
    must
    1. **Atomicity**: The result of all operations in a transaction are represented in
       the database or none are
    2. **Consistency**: When transactions are executed in isolation, data remains consistent
    3. **Isolation**: When transactions executed concurrently, none of the transactions
       need to know about or depend on each other
    4. **Durability**: After the transaction completes successfully, the changes persist
       to the database, even if there are system failuresvalues
* A transaction that completes successfully is considered committed.
* After all steps have run successfully, but before the result has been written to the
    database, that transaction state is partially committed.
* A transaction is active while it is still processing data.
* If a transaction has failed, all changes are rolled back. The transaction is then in 
    the aborted state.
