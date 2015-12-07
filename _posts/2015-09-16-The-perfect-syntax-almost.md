---
layout: post
title: The perfect syntax (almost)
author: Tomas Slusny
tags:
  - spoon
---

Hey. Some time passed since I last posted something, but this one will be big. I was messing with that new language on what I am working and I think the results are great.

So basically, this new language is mix between Ruby and Haxe, so I named it **Raxe**. I was totally bored, so I also created nice logo for it:

![Raxe logo](http://i.imgur.com/O5gseG4.png)

And, what are the awesome things I added? So, let's go:

  1. Awesome looking block comments
  2. Awesome looking multiline strings
  3. Define system - no different keywords for declaring properties and functions
  4. Automatic package and module naming

So, I can explain first 3 things via code example, what I will do later in this post. So I will explain 4th one.

**Package naming** - root package is defined by closest subdirectory to your working directory. If your working directory is same as your project directory, then package will be empty.
So, for example you build **Raxe** project from directory `myproject-master` and you have your source in `myproject-master/myproject`. Your root package will be then set to `myproject`.
But if you have your source also in `myproject-master`, your root package will be empty. And subpackages are defined also based on subdirectories.
So if you have f.e. `Utils.rx` in folde `myproject-master/myproject/internal/utils` then full package for `Utils.rx` will be `myproject.internal.utils`.

**Module naming** - this is simple. Your module will be named same as your source file, without `.rx` extension.

And now, to first 3 things:

```ruby
---
This is how block (multiline) comments look in Raxe.
You can plase as much text here as you want, you just must
terminate block comment with same number of hypnens as you
initialized it.
---
module class

static def multilinestring = """
  And this is multiline string.
  Looking great right?
"""

---
Anonymous array declaration is dynamic by default.
So you can put anything in there.
---
static def array = [
  "a", "b", "c", 6
]

---
Same applies to anonymous structures.
---
static def struct = {
  a: "hello",
  b: "yolo",

  ---
  And this is how we define anonymous function. It is also
  dynamic by default, so all parameters are correctly typed
  by Haxe compiler
  ---
  callback: def(event)
    return event + " World"
  end
}

static def main()
  -- Here we will call our anonymous function
  def result = struct.callback("Hello")

  -- And now, print everything to console
  trace(result)
  trace(array)
  trace(struct)
end
```

And here is how above code will look when compiled to Haxe. And do not say that output is ugly, yes it is, but focus is to have 1-1 compiler, so line numbers stays same, so you will have easy time debugging it and you will be able to see exactly where error happened in your **Raxe** code from **Haxe** error ouput.

```haxe
package myproject;/*
This is how block (multiline) comments look in Raxe.
You can plase as much text here as you want, you just must
terminate block comment with same number of hypnens as you
initialized it.
 */
 class Main {

static var multilinestring = "
  And this is multiline string.
  Looking great right?
";

/*
Anonymous array declaration is dynamic by default.
So you can put anything in there.
 */
static var array = [
  "a", "b", "c", 6
];

/*
Same applies to anonymous structures.
 */
static var struct = {
  a: "hello",
  b: "yolo",

  /*
  And this is how we define anonymous function. It is also
  dynamic by default, so all parameters are correctly typed
  by Haxe compiler
   */
  callback: function(event){
    return event + " World";
  }
};

static function main(){
  // Here we will call our anonymous function
  var result = struct.callback("Hello");

  // And now, print everything to console
  trace(result);
  trace(array);
  trace(struct);
}}
```

Raxe compiler isn't fully working yet, so when compiled using current version, above code will be compiled without semicolons.

So, if you want to support Raxe, do not forget to check official [Raxe repository on GitHub](https://github.com/nondev/raxe) and if you have any ideas or things to contribute, please, do it, I maybe can handle project as big as this is, but with help from others, it will be a lot easier a a lot more fun.
