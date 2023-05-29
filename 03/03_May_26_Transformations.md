## Transformations

In Java2D, Processing (and many other libraries). There is a concept called transformations. It is basically modifying origins and axis before start rendering.

The following transformation methods are most commonly used:

- `translate`
- `scale`
- `rotate`

Other than transformations, there is usually a "transformation stack" concept. You can `push` to save the current transformation state and then `pop` to restore the previous one. 

With transformations you can do some really cool and more dynamic graphics, as well as reducing a lot of workload in some cases such as: rolling background and camera shakes.

---

**Translate** (moving the origin)

In Processing: `void translate(x, y)` (or `void translate(x, y, z)` in 3D space)

In Java: `Graphics2D.translate(x, y)`

**Rotate** (rotate the axis about the origin)

Processing: `void rotate(angle)` 

Java: `Graphcis2D.rotate(angle)`

<u>(Note that parameter "`angle`" should be in radians)</u>

**Scale** (stretches/compresses the axis about the origin)

Processing: `scale(x, y)`

Java: `Graphics2D.scale(x, y)`

<u>(Note that parameter `x` and `y` are the ratio of stretch/compression. If you want to "scale" only one axis, then you should leave the other one as **1**, in other words, **1** means un-modified)</u>

---

Below is an example of how transformations look like: (translate $\to$ rotate $\to$ scale)

<iframe src="03_May_26_Transformations_original.html" width=400 height=400> </iframe>

**beware that the order of transformations matters**

Below is an example of a different order (scaling before rotating) (translate $\to$ **scale** $\to$ **rotate**)

<iframe src="03_May_26_Transformations_another_order.html" width=400 height=400> </iframe>
