---
layout: post
title:  Quickly Drop Postgres User with Hundreds of Dependent Objects
date:   2023-01-07 12:10:03 -0800
categories: postgres db
---

I recently needed to remove a user from our database and got the following error:

```
ERROR:  role "old-baboon" cannot be dropped because some objects depend on it
... <huge list of objects>
```

These objects should never have been owned by this particular user in the first place 
and I knew which user should own them, but it was impractical to do them as one offs.
It ended up being pretty easy after doing some quick research:

```
reassign owned by old-baboon TO young-baboon; 
drop owned by old-baboon;
drop user old-baboon;
```
