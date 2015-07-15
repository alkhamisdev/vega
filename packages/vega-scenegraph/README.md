# vega-scenegraph

[Vega](http://gihub.com/vega/vega) scenegraph and renderers.

Renderers and event handlers for Vega's [mark-based](https://github.com/vega/vega/wiki/Marks) scenegraph. This module supports both pixel-based (canvas) and vector graphics (SVG) output. Renderers can either (re-)draw a complete scene or perform incremental re-rendering for a set of provided "dirty" items. A fast SVG string renderer is also provided to generate static SVG outside the browser environment.

The [node-canvas](https://github.com/Automattic/node-canvas) library is used for server-side canvas rendering and bounds calculation. Node-canvas requires the native Cairo graphics library and compiles native code as part of the installation process. In some instances this may result in installation hiccups. Should you run into issues, you are likely to quickly resolve them more quickly if you first search for help regarding node-canvas (as opposed to vega-scenegraph) installation.

### Scenegraph Definition

The Vega scenegraph is a hierarchical (tree) data structure. The levels of the tree alternate between an enclosing _mark_ definition and contained sets of mark instances called _items_.

For example, here is a simple scenegraph containing three rectangles:
```
{
  "marktype": "rect",
  "items": [
    {"x": 0, "y": 0, "width": 50, "height": 50, "fill": "steelblue"},
    {"x": 100, "y": 50, "width": 50, "height": 50, "fill": "firebrick"},
    {"x": 50, "y": 100, "width": 50, "height": 50, "fill": "forestgreen"}
  ]
}
```

The supported mark types are rectangles (`rect`), plotting symbols (`symbol`), general paths or polygons (`path`), circular arcs (`arc`), filled areas (`area`), lines (`line`), images (`image`), text labels (`text`), and chart gridlines or rules (`rule`). Each item has a set of supported properties (`x`, `y`, `width`, `fill`, and so on) appropriate to the mark type.

Scenegraphs may also contain `group` marks, which serve as containers for other marks. Groups may also include specialized subsets for axes and legends.

For example, a top-level group mark may look like:
```
{
  "marktype": "group",
  "items": [
    {
      "x": 0,
      "y": 0,
      "width": 200,
      "height": 200,
      "items": [...], // array of contained mark instances
      "axisItems": [...], // special array for axis groups
      "legendItems": [...] // special array for legend groups
    }
  ]
}
```

In this example, the group _mark_ contains only a single group _item_. In practice a group mark may contain a number of group items, for example to describe a scene with multiple layers or sub-plots.

For more information regarding supported mark properties, please see the [Vega marks documentation](https://github.com/vega/vega/wiki/Marks).

### Scenegraph Serialization

The top-level export of this package includes `fromJSON` and `toJSON` methods to support scenegraph serialization. The `fromJSON` method expects a JSON string as input (similar to the examples listed above). It will then add additional parent pointers to the tree structure. For example, each item will have a `mark` property pointing to it's parent mark, and each mark will have a `group` property pointing to it's parent group (if any). The `toJSON` method maps a scenegraph instance to a JSON string, stripping any parent pointers or other non-standard properties.