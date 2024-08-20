# A Basic Shape

The shape module lets you create arbitrarily paths and then stroke the outline and fill the interior. The definition of the path can be reused in other places where paths are used, e.g. for the `PathView` element used with models. But to paint a path, the `Shape` element is used, and the various path elements are put into a `ShapePath`.

In the example below, the path shown in the screenshot here is created. The entire figure, all five filled areas, are created from a single path which then is stroked and filled.

![](assets/automatic/basic.png)

```
import QtQuick
import QtQuick.Shapes

Rectangle {
    id: root
    width: 600
    height: 600

    Shape {
        anchors.centerIn: parent
        
        ShapePath {
        
            strokeWidth: 3
            strokeColor: "darkGray"
            fillColor: "lightGray"

            startX: -40; startY: 200
            
            // The circle
            
            PathArc { x: 40; y: 200; radiusX: 200; radiusY: 200; useLargeArc: true }
            PathLine { x: 40; y: 120 }
            PathArc { x: -40; y: 120; radiusX: 120; radiusY: 120; useLargeArc: true; direction: PathArc.Counterclockwise }
            PathLine { x: -40; y: 200 }

            // The dots
            
            PathMove { x: -20; y: 80 }
            PathArc { x: 20; y: 80; radiusX: 20; radiusY: 20; useLargeArc: true }
            PathArc { x: -20; y: 80; radiusX: 20; radiusY: 20; useLargeArc: true }

            PathMove { x: -20; y: 130 }
            PathArc { x: 20; y: 130; radiusX: 20; radiusY: 20; useLargeArc: true }
            PathArc { x: -20; y: 130; radiusX: 20; radiusY: 20; useLargeArc: true }

            PathMove { x: -20; y: 180 }
            PathArc { x: 20; y: 180; radiusX: 20; radiusY: 20; useLargeArc: true }
            PathArc { x: -20; y: 180; radiusX: 20; radiusY: 20; useLargeArc: true }

            PathMove { x: -20; y: 230 }
            PathArc { x: 20; y: 230; radiusX: 20; radiusY: 20; useLargeArc: true }
            PathArc { x: -20; y: 230; radiusX: 20; radiusY: 20; useLargeArc: true }            
        }
    }
}
```

The path is made up of the children to the `ShapePath`, i.e. the `PathArc`, `PathLine`, and `PathMove` elements in the example above. In the next section, we will have a close look at the building blocks of paths.
