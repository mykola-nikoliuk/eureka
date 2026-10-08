# Decisions

Why the demo is built the way it is. One line per decision, with the reason.

1. **The lesson.** The area of displaced fluid equals the area of the shape submerged in it
   (Archimedes' eureka). The water measures the shape; nothing is computed from the shape
   itself.
2. **2D.** Area is visible at a glance, the whole shape and the water level stay in view with no
   camera or perspective, and the time a 3D fluid surface would take goes into physics and UX.
   3D can come later as a second level.
3. **How shapes enter the water.** Shapes sit in cells above the vessel. A click (or the AI)
   opens or removes a cell's floor, and the shape falls straight down under gravity.
4. **Which shapes.** Any shape the API can describe, as long as it fits in a cell. We start with
   ready-made shapes; later a person can draw one on the grid, or the AI can write one.
5. **Shapes always sink.** A floating shape displaces fluid equal to its weight, not its area,
   so it would show less than its area. Floating can be a separate lesson later.
6. **Overflow vessel.** Before the lesson the vessel is filled exactly to the level of an
   overflow channel. After a shape sinks, the displaced water flows down the channel into a
   thin measuring cylinder, where a narrow cross-section makes the scale very clear.
7. **Accuracy.** The reading must land within half a scale division, so the learner reads the
   right mark; we aim for ±5%. The scale step comes from the lesson (shapes made of half cells
   need 0.5 steps), never coarser to hide error. A constant packing factor is removed by
   calibration; what calibration can't remove is the gap particles leave along a shape's edges,
   which grows with the perimeter (a 1×4 strip shows more than a 2×2 square). We shrink the
   particles instead of correcting by the perimeter, because a correction would mean the
   formula, not the water, measures the area. Rough estimate: about 12 particles per unit of
   length keep a 2×2 square within 5%.
8. **CPU first.** The first step checks accuracy, not speed, so it runs on the CPU in
   TypeScript. Whether we need the GPU (WebGPU with a WebGL fallback) is decided by profiling
   once accuracy is proven, since not every device has a strong GPU or WebGPU support.
9. **First step.** A vessel holding a 2×2 square, plus the measuring cylinder. Passes when, after
   everything settles, both the 2×2 square and a 1×4 strip read 4 ± 0.2, over several runs.
10. **Layout.** The top third of the screen is the container a shape starts in, the bottom two
    thirds is the vessel. The water is always deeper than the container is tall, and a shape is
    always smaller than its container with a gap around it, so a sunk shape is fully under water
    and the fluid can flow past it. The measuring cylinder comes after the simplified version
    works; until then the reading is the level rise in the vessel itself.
11. **Stencil.** Collisions with an arbitrary shape use a stencil: a fine grid in the shape's own
    coordinates, where each occupied cell stores a vector to the nearest free cell. A drop
    inside an occupied cell is pushed out along that vector. The stencil is grown by a drop's
    radius, so a drop whose centre is outside the shape but whose edge overlaps it is caught
    too. The shape is rigid and doesn't rotate, so the stencil is built once per shape, and a
    drop is checked by moving it into the shape's coordinates. The shape against the floor and
    walls uses its real outline, not the grown stencil, so it lies flat on the floor.
12. **Drops against a shape.** The push direction is the stencil vector, which is the normal to
    the edge, not the line between centres. The correction is split in inverse proportion to
    the masses (mass = area × density): the drop takes almost all of it and the shape slows down
    a little. Hundreds of such hits are what make the water resist a sinking shape.
