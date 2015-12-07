---
layout: post
title: Terminal powah
author: Tomas Slusny
tags:
  - programming
---

So hello again everyne. Today I was working really hard on Kaizo (in work, but who cares lol).
I implemented that subproject system I mentioned in last blog post. It is really awesome. You define

```lua
project("myproject")
```

at start of your project file. Then, to call task for it, you just use

```lua
task("myproject.mytask")
```

and from commandline

```
./kaizow myproject.mytask
```

And here is some eye candy for new update

![Beautifull terminal](http://i.imgur.com/S1oVruZ.png)

So that is all for now, but I will post more when I will got some more time.
