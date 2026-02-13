try it: https://emdiet.github.io/css-only-normal-maps-poc/test.html

Can normal maps be applied to css quads even when webgpu/webgl isn't available? yes! (sorta, requires more work, but looks promising)

https://github.com/user-attachments/assets/6b4e99a0-770d-4f0e-86d7-529e0afcce9e

still room for improvement, obviously.

100% vibecoded with codex

## Algorithm:

- base image/color map
- shadow container (multiply)
  - grayscale(1), contrast/brightness compensation
    - normal map (remapped from gl to color dome, but is a static image)
    - elevation screen (single color, white to black)
    - azimuth selector (single color, R = 255*Cos(theta), G = 255*Cos(theta+120), B = 255*Cos(theta+240))
    - grayscale normalization #571AFF (multiply)


## About grayscale normalization (#571AFF magic number)

The grayscale algorithm is biased (https://drafts.csswg.org/filter-effects/#grayscaleEquivalent

```xml
<filter id="grayscale">
  <feColorMatrix type="matrix"
             values="
    (0.2126 + 0.7874 * [1 - amount]) (0.7152 - 0.7152  * [1 - amount]) (0.0722 - 0.0722 * [1 - amount]) 0 0
    (0.2126 - 0.2126 * [1 - amount]) (0.7152 + 0.2848  * [1 - amount]) (0.0722 - 0.0722 * [1 - amount]) 0 0
    (0.2126 - 0.2126 * [1 - amount]) (0.7152 - 0.7152  * [1 - amount]) (0.0722 + 0.9278 * [1 - amount]) 0 0
    0 0 0 1 0"/>
</filter>
```

we can invert that more or less and get these coefficients

```
sr ≈ 1.5679
sg ≈ 0.4661
sb ≈ 4.6168
```

unfortunately, we can't multiply 255*4.6168 and still get a color - so we normalize to 1 (which is a mistake), and we compensate for it brightness.

In any case, we need to heavily derank green to get a useable result out of this.

What's interesting is that sepia is basically the same algorithm, so we could use that with a different magic number

Chat chippidy says #7B3FFF/rgb(0.4813028, 0.2453302, 1) could work.

## Room for Improvement

I think the normal remapping alogrithm could work better with a sine based approach to clear out these gaps and reduce the jerkiness.

## Worth

Is it worth it as a fallback? 

Potentially, the profiler says it's blazing fast.

## Textures

Textures sourced from here https://ambientcg.com/view?id=Rocks002




