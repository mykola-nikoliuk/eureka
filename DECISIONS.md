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
