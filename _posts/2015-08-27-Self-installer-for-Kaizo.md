---
layout: post
title: Self installer for Kaizo
author: Tomas Slusny
tags:
  - programming
---

I finished self-installer and runner for [Kaizo](https://github.com/nondev/kaizo) finally. It is based on same principe as Gradle's `gradlew`.

So, to get it, simply clone this repository and from `bootstrap/` folder, copy these 3 files:

* `kaizow.exe`
* `kaizow`
* `kaizow.bat`

And then put it to your project's directory and execute your project like normal Kaizo project, but with `kaizow` at beginning:

```
./kaizow <insert_kaizo_arguments_here>
```

Right now, `kaizow` will download latest release from it's GitHub repository. It will download it only once to your HOME directory to `.kaizo` subdirectory. To force update Kaizo, run

```
./kaizow update
```

And that is everything for now folks. Here, take this banana:

![Banana](http://www.bbcgoodfood.com/sites/bbcgoodfood.com/files/glossary/banana-crop.jpg)
