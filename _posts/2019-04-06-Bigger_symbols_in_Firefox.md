---
layout: post
title: Bigger Icons in Firefox
---

On screens with a high resolution Firefoxs icons are sometimes very small. Here is the way to customize their size:

1. Enter `about:config` in the address field of the browser
1. Search for the key `layout.css.devPixelsPerPx`
1. Change the value from its initial `-1` to something different. Values greater than 1.0 produce bigger icons, values lesser than 1.0 result in smaller icons.
