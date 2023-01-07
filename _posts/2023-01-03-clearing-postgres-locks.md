---
layout: post
title:  Clearing Postgres Locks
date:   2023-01-03 14:07:03 -0800
categories: postgres db
---

Every once in a while I run into issues where a script that uses a Postgres
Advisory Lock crashes and the lock doesn't get released. The script is run
via cron and since the Advisory Lock wasn't properly released, the next time
the script starts up it fails to aquire the lock and pings our errbit system
and then shuts down. This will continue forever unless I manually clean up
the lock.


## The Clean up Process
### View A Specific Lock
{% highlight sql %}
select * from pg_locks where objid = <lockid>;

-[ RECORD 1 ]------+----------------
locktype           | advisory
database           | 16401
relation           |
page               |
tuple              |
virtualxid         |
transactionid      |
classid            | 0
objid              | 1661777215
objsubid           | 1
virtualtransaction | 132/0
pid                | 28843
mode               | ExclusiveLock
granted            | t
fastpath           | f
waitstart          |
{% endhighlight %}

Among other things, we can see the pid of the process that initiated this lock: 28843

### Stop the Process
{% highlight sql %}
select pg_cancel_backend(28843);
----------------------------------
t
(1 row)
{% endhighlight %}

This problem with this method, is that it may or may not have worked. We actually
need to query pg_stat_activity to see if was actually shudown.

{% highlight sql %}
select * from pg_stat_activity where pid = 28843;
{% endhighlight %}

If the process is still running, we need to use a more forceful method:

### Seriously, Stop the Process!
{% highlight sql %}
select pg_terminatel_backend(28843);
----------------------------------
t
(1 row)
{% endhighlight %}

And now we can confirm that the lock has been released:

{% highlight sql %}
select * from pg_locks where objid = <lockid>;
...
 (0 rows)
{% endhighlight %}

The next time the script runs via cron it will successfully aquire the lock.
