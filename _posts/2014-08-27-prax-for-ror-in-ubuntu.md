---
layout: post
title: "Prax for ROR in Ubuntu"
description: ""
category: Ruby
tags: []
---
You know Prax ? if you don't, close this page and enjoy your life :) <br />
You're tired  when setting Prax. OK, I used to be like you :)

I followed this [instructions]  (http://ysbaddaden.github.io/prax/) to setup Prax on my computer, but it didn't work.

```
myapp.dev   #failed
myapp.dev:3000 #successful
```

```myapp.dev:3000``` is n't what I want. You can follow the below steps to setup Prax successfully:

  * start rails server
  * cd /opt/prax/bin
  * prax start
  * ln -sf ~/myapp  ~/.prax/
  * firefox http://myapp.dev

WOAH! Là lá la la


![alt text](https://camo.githubusercontent.com/92a0e6c8cfa3aae0e70f442fc915263ee59bf5bf/687474703a2f2f6d656469612e74756d626c722e636f6d2f74756d626c725f6c74757a6a766251364c31717a677078392e676966 "Amazing")


Next time, you just start rails server and run ```prax start```

That's all, and thanks for your concern. If you have any questions, please don't hesitate to ask me :).
