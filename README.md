# microGUI

microGUI (µGUI) is a single-header, zero-dependency library for very small (often embedded) graphical user interfaces. Its architecture and functionality are heavily inspired by nicbarker's wonderful [Clay](https://github.com/nicbarker/clay/tree/main) library, drawing on Clay's approach to nested declarative layouts. The libraries' differences lie in: 

- **The layout types prioritized:** *where Clay is the main menu, µGUI is the HUD.* µGUI does not support scrolling layouts. Instead, layouts are based on tiling and pinned nodes. 
- **The set of features offered:** µGUI's feature set is much smaller than Clay's. This reduces expressiveness, but is critical for compatibility with basic render systems like [Adafruit_GFX](https://github.com/adafruit/Adafruit-GFX-Library).

``` c
MG({.flow = MG_FLOW_H}) {
    MG_PIN({.anchor = MG_ANCHOR_SE, .x = -5, .y = -5}) {
        MG({.flow = MG_FLOW_V}) {
            MG_TEXT{"Hello from the top of the pin!"}
            MG_TEXT{"Hello from the bottom!"}
        }
    }
}
```

<!-- TODO insert example image -->

## Tiles

Unless pinned, a container's children will follow a tiling layout. Depending on the container's `.flow` field, its content can tile horizontally (`MG_FLOW_H`) or vertically (`MG_FLOW_V`)

### Sizing
TODO
<!--
MG_SIZING_PX(px: uint32_t)
MG_SIZING_GROW(proportion: uint32_t)
MG_SIZING_PERCENT(percent: double)
-->

## Pins
Pinning an element pops it out of the tiling layout, much like absolute positioning in CSS. 

### Pin positioning
An element is pinned relative to its `anchor`. Pinning an element with `.anchor = MG_ANCHOR_SE` will position the element's southeastern corner relative to the southeastern corner of its parent container. The pin's `.x` and `.y` fields determine the child's offset, in pixels, from the anchor. 

If a pin's `.anchor` is unspecified, the anchor will be dropped at the current gravity source (see [Gravity](#gravity))

### Pin sizing
Within a container, a pinned element does not affect the positioning or sizing of tiled elements. Likewise, a pinned element's sizing is not influenced by tiled elements; unless otherwise specified, it will inherit the width and height of its parent.

## Gravity
Not all elements stretch to fill the screen, so sometimes a true tiling layout is impossible. Consider a box containing two (unpinned) images: how does µGUI know whether to place them

```
like this:    or this:      or this?
+-------+     +-+-+---+     +-------+
| +-+-+ |     |1|2|   |     |   +-+-+
| |1|2| |     +-+-+   |     |   |1|2|
| +-+-+ |     |       |     |   +-+-+
+-------+     +-------+     +-------+
```

The answer is *gravity*. Gravity can be configured in two ways:

- **By specifying the `.gravity` of a container:** A container's descendants will fall toward the container's gravity source.
- **By specifying the `.anchor` of a pin:** A pin's descendants will fall toward the pin's anchor.

By default, gravity is inherited from a node's parent.
