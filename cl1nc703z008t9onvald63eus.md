## Asterisk 11.6 and PostgreSQL 13: column d.adsrc does not exist

# The problem

Do you work with `asterisk 11.6` and `PostgreSQL 9`?  
Have you decided to upgrade PostgreSQL to version `13` and now Asterisk gives you **bad errors** at startup and most important **no longer saves** any lines in the **CDRs**?  

```bash
ERROR[12558] res_config_pgsql.c: PostgreSQL RealTime: Query Failed: SELECT a.attname, t.typname, a.attlen, a.attnotnull, d.adsrc, a.atttypmod FROM (((pg_catalog.pg_class c INNER JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace AND c.relname = 'sipfriends' AND n.nspname = current_schema()) INNER JOIN pg_catalog.pg_attribute a ON (NOT a.attisdropped) AND a.attnum > 0 AND a.attrelid = c.oid) INNER JOIN pg_catalog.pg_type t ON t.oid = a.atttypid) LEFT OUTER JOIN pg_attrdef d ON a.atthasdef AND d.adrelid = a.attrelid AND d.adnum = a.attnum ORDER BY n.nspname, c.relname, attnum
ERROR[12558] res_config_pgsql.c: PostgreSQL RealTime: Query Failed because: ERRORE:  the d.adsrc column does not exist
ERROR[12558] res_config_pgsql.c: Failed to query database columns for table sipfriends
ERROR[12558] cdr_pgsql.c: Failed to query database columns: ERRORE:  the d.adsrc column does not exist
```

I found myself in this situation and I want to tell you how I got out of it.

---

# The analysis

Googling I found the problem: some asterisk files (`cdr/cdr_pgsql.c`, `cel/cel_pgsql.c` and `res/res_config_pgsql.c`) query various system tables (such as `pg_class`, `pg_type`, `pg_attribute`) including in the select also the column `d.adsrc` (`d` is `pg_attrdef` table) but **PostgreSQL 12 finally removed column adsrc from table pg_catalog.pg_attrdef (column default values), which has been deprecated since version 8.0**.

The problem is well described:

- in the post on this mailing list: [cdr_pgsql: accesses obsolete (and finally removed) column](http://lists.digium.com/pipermail/asterisk-bugs/2019-October/196852.html)
- in the reference [asterisk Jira task](https://issues.asterisk.org/jira/browse/ASTERISK-28571)

As described in the task on gerrit: *fortunately, the same information which once was stored in adsrc is accessable via column adbin and function pg_catalog.pg_get_expr, which is the recommended way since PostgreSQL 8.0*. 
So here is the patch: [https://gerrit.asterisk.org/c/asterisk/+/13027/](https://gerrit.asterisk.org/c/asterisk/+/13027/)

---

# The solution

So for me the possible ways were mainly 3:

1. upgrade Asterisk to the latest version, hoping (or rather, reading all the changelogs) that the problem would not occur there;
1. downgrade PostgreSQL to a version earlier than 11;
1. recompile Asterisk 11.6 with the patch indicated in the asterisk task.

Of course, the best thing is to always have up-to-date systems, maybe not at the latest version but at the latest stable version; so thinking of keeping Asterisk 11.6 or downgrading PostgreSQL are not the best choices.  
At the same time, it is true that jumping from Asterisk 11.6 to Asterisk 18 is not easy and before putting the system into production with Asterisk 18 I would have had to do many tests; instead I had little time and I needed to restore the full functionality of the voice system.  
I decided to opt for the third choice *recompile asterisk 11.6 with the patch indicated in the asterisk task* and here I will describe the steps I have successfully followed. 


The steps that I followed are easy and with more or less 0 downtime for the customers

```bash
# I git clone the asterisk source code in tmp folder 
# and checkout the 11.6 version
cd /tmp
git clone https://github.com/asterisk/asterisk.git
cd asterisk
git checkout certified/11.6
# I install the needed packages
# You can also use the install_prereq (take a look at Linkography section)
apt-get install ncurses-dev libxml2-dev sqlite3 libsqlite3-dev libpq-dev
# I configure the installation
./configure
make menuselect # remember to select all the module and functions you need
# I ran the patch by editing these files
vim cdr/cdr_pgsql.c # https://gerrit.asterisk.org/c/asterisk/+/13027/2/cdr/cdr_pgsql.c
vim cel/cel_pgsql.c # https://gerrit.asterisk.org/c/asterisk/+/13027/2/cel/cel_pgsql.c
vim res/res_config_pgsql.c # https://gerrit.asterisk.org/c/asterisk/+/13027/2/res/res_config_pgsql.c
# I lunch the make
make
# I remove the actual asterisk installation
apt-get remove asterisk asterisk asterisk-config asterisk-core-sounds-en asterisk-core-sounds-en-gsm asterisk-moh-opsound-gsm asterisk-modules
# and in the end I install the compiled and patched version
make install
```

If your system consists of several asterisk nodes, one clone of the other (i.e. with the same OS), you do not need to compile asterisk on each node to update any additional asterisk nodes, but you can follow these steps

1. go to the asterisk node where you recompiled and create an archive of the asterisk folder

    ```bash
    cd /tmp
    tar -cvzf asterisk11-6-patched.tar.gz asterisk
    ```

1. move the `tar.gz` on the asterisk node to be patched

1. decompress the `tar.gz`, remove the current version of asterisk and install the patched one
    ```bash
    cd /tmp
    tar -xvzf asterisk11-6-patched.tar.gz
    cd asterisk
    apt-get install build-essential
    apt-get remove asterisk asterisk-config asterisk-core-sounds-en asterisk-core-sounds-en-gsm asterisk-moh-opsound-gsm asterisk-modules
    make install
    ```
    
Start asterisk, make a call and check your CDR. **You will see that the data is there now!**

---

# Linkography

- [Compile asterisk](https://wiki.asterisk.org/wiki/display/AST/Building+and+Installing+Asterisk)
- [Install the needed software for asterisk compiling](https://wiki.asterisk.org/wiki/display/AST/Checking+Asterisk+Requirements#CheckingAsteriskRequirements-install_prereq)
- [Mailinglist post](http://lists.digium.com/pipermail/asterisk-bugs/2019-October/196852.html)
- [Asterisk Jira task](https://issues.asterisk.org/jira/browse/ASTERISK-28571)
- [Asterisk patch commit](https://gerrit.asterisk.org/c/asterisk/+/13027)
- [PostgreSQL remove adsrc commit](https://git.postgresql.org/gitweb/?p=postgresql.git;a=commitdiff;h=fe5038236c6b99d48c2faa2247b5cec9703add2a)

Cheers!