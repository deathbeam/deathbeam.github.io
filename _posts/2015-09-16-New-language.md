---
layout: post
title: New language?
author: Tomas Slusny
tags:
  - spoon
---

So hello again everyone. Recently, I was messing with [Haxe](https://haxe.org). It is really great language with awesome compiler that can target multiple platforms **natively**.
And, then I looked at syntax. It is good, but... yeah it can be better. I am thinking about writing some simple language that will compile directly to Haxe. So, I came up with some significant whitespace syntax, here is small example on how this new language should look:

```haxe
package myproject

import openfl.display.Sprite
import openfl.events.Event
import openfl.Lib

class Main extends Sprite
  var cacheTime : Int
  var speed : Float
  var sprite : Sprite

  function this.new ()
    super ()

    sprite = new Sprite ()
    sprite.graphics.beginFill (0x24AFC4)
    sprite.graphics.drawRect (0, 0, 100, 100)
    sprite.y = 50
    addChild (sprite)

    speed = 0.3
    cacheTime = Lib.getTimer ()
    addEventListener (Event.ENTER_FRAME, onEnterFrame)

  local function this.update (deltaTime : Int) : Void
    if (sprite.x + sprite.width >= stage.stageWidth || sprite.x <= 0)  
      speed *= -1

    sprite.x += speed * deltaTime

  local function this.onEnterFrame (event : Event) : Void
    var currentTime = Lib.getTimer ()
    update (currentTime - cacheTime)
    cacheTime = currentTime
```

If you noticed, I use `local` as replacement for `private` and methods and variables are public by default. Also, they are static by default, but for instance functions you must add `this.` before function name.

And here is same code as above in classic Haxe:

```haxe
package myproject;

import openfl.display.Sprite;
import openfl.events.Event;
import openfl.Lib;

class Main extends Sprite {
  var cacheTime : Int;
  var speed : Float;
  var sprite : Sprite;

  public function new () {
    super ();

    sprite = new Sprite ();
    sprite.graphics.beginFill (0x24AFC4);
    sprite.graphics.drawRect (0, 0, 100, 100);
    sprite.y = 50;
    addChild (sprite);

    speed = 0.3;
    cacheTime = Lib.getTimer ();
    addEventListener (Event.ENTER_FRAME, onEnterFrame);
  }

  private function update (deltaTime : Int) : Void {
    if (sprite.x + sprite.width >= stage.stageWidth || sprite.x <= 0) {
      speed *= -1;
    }

    sprite.x += speed * deltaTime;
  }

  private function onEnterFrame (event : Event) : Void {
    var currentTime = Lib.getTimer ();
    update (currentTime - cacheTime);
    cacheTime = currentTime;
  }
}
```
