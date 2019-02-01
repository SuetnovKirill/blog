---
layout: post
title:  "mplcursor with many installed artists" 
date:   2019-01-31                             
categories: python              
---

Suddenly I spent huge amount of time trying to understand: why code that worked several months ago
doesn't work anymore:

~~~python
import matplotlib.pyplot as plt
import mplcursors
import numpy as np
import imageio
import cv2

labels = ["a", "b", "c", "d", "e"]
x = np.array([0, 100, 200, 300, 400])
img = imageio.imread(r"d:\Figure_1.png")
img = cv2.cvtColor(img, cv2.COLOR_RGB2GRAY)
fig, ax = plt.subplots()

ax.imshow(img)
line = ax.plot(x, x, "ro", scalex=False, scaley=False)

what_to_track = ax.get_lines()
mplcursors.cursor(what_to_track).connect(
    "add", lambda sel: sel.annotation.set_text(labels[sel.target.index]))

fig.show()
~~~


mplcursors acts differently depending of the type of artist found. In case of AxesImage mplcursor expects 
that plot is 2D-plot, and `sel` will be tuple of (x,y). In case of Line2D sel will be index of selected point.
 
The key problem is that mplcursor uses the **FIRST** added artist if you provide Axes object to cursor() function. If it will be image - you will get pixel coordinates instead of point index. 
The solution is to provide Artist to mplcursor directly: set `what_to_track` to `ax.get_lines()` or `ax.get_images()` instead of `ax`.

Usefull links: [matplotlib class hierarhy](https://matplotlib.org/api/artist_api.html#matplotlib.artist.Artist)

  