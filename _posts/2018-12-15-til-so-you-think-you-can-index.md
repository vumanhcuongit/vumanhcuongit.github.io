---
layout: post
title: "[TIL] #3 So you think you can index?"
description: ""
categories:
  - Ruby
tags:
 - til
 - postgresql
 - database
excerpt: "Tips and tricks to keep in mind while creating indexes."
hashtag: postgresql_index
<!-- comments: true -->
---
Today I learned more about PostgreSQL indexes.

**Theory**

*The theory says, as long as the leftmost column in the query already has a single index or that leftmost column is the leftmost column on some other composite index, then there is no need to have another composite index . The crux: other columns should not be having a lot of data corresponding to that left most column.*

**Index usage of the table**

{% highlight sql %}
  select * from pg_stat_all_indexes where relname = 'dummy_table';
{% endhighlight %}

![index usage](https://cdn-images-1.medium.com/max/1600/1*Lt3HUbEqtcd5qDGIcEAOrg.png)

The space taken by an index:

{% highlight sql %}
select pg_size_pretty(pg_table_size('idempotency_index'));
{% endhighlight %}

![index space](https://cdn-images-1.medium.com/max/1600/1*P0aJ2jKy_9KS9Gakzkl7LA.png)

**Percentage of Index used**

{% highlight sql %}
SELECT
  relname,
  100 * idx_scan / (seq_scan + idx_scan) percent_of_times_index_used,
  n_live_tup rows_in_table
FROM
  pg_stat_user_tables
WHERE
    seq_scan + idx_scan > 0
ORDER BY
  n_live_tup DESC;
{% endhighlight %}

![percentage of index used](https://cdn-images-1.medium.com/max/1600/1*81-iPkrzOu_h-5wmreXNWg.png)


**Cache hit ratio**

{% highlight sql %}
SELECT
  sum(idx_blks_read) as idx_read,
  sum(idx_blks_hit)  as idx_hit,
  (sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit) as ratio
FROM
  pg_statio_user_indexes;
{% endhighlight %}

![cache hit ratio](https://cdn-images-1.medium.com/max/1600/1*PCGTw5ulGzn7KhE_8kwrIQ.png)


**Impact Caused By Unused Indexes:**

*It reduces the cache hit ratio since it will occupy some space in cache in the beginning. It’s highly probable that part of some other useful index lies in the disk rather than cache. So when a query comes, it goes and checks in disk, thereby reducing the cache hit ratio.*

*Percentage of indexes used will decrease.*

### Reference
<https://blog.gojekengineering.com/so-you-think-you-can-index-d66869e589dd>
