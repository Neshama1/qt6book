# Animating Shapes

One of the nice aspects of using Qt Quick Shapes, is that the paths drawn are defined directly in QML. This means that their properties can be bound, transitioned and animated, just like any other property in QML.

![](assets/automatic/animation.png)

In the example below, we reuse the basic shape from the very first section of this chapter, but we introduce a variable, `t`, that we animate from `0.0` to `1.0` in a loop. We then use this variable to offset the position of the small circles, as well as the size of the top and bottom circle. This creates an animation in which it seems that the circles appear at the top and disappear towards the bottom.

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
            id: shapepath
            
            property real t: 0.0
            
            NumberAnimation on t { from: 0.0; to: 1.0; duration: 500; loops: Animation.Infinite; running: true }
        
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
            
            PathMove { x: -20+(1.0-shapepath.t)*20; y: 80 + shapepath.t*50 }
            PathArc { x: 20-(1.0-shapepath.t)*20; y: 80 + shapepath.t*50; radiusX: 20*shapepath.t; radiusY: 20*shapepath.t; useLargeArc: true }
            PathArc { x: -20+(1.0-shapepath.t)*20; y: 80 + shapepath.t*50; radiusX: 20*shapepath.t; radiusY: 20*shapepath.t; useLargeArc: true }

            PathMove { x: -20; y: 130 + shapepath.t*50 }
            PathArc { x: 20; y: 130 + shapepath.t*50; radiusX: 20; radiusY: 20; useLargeArc: true }
            PathArc { x: -20; y: 130 + shapepath.t*50; radiusX: 20; radiusY: 20; useLargeArc: true }

            PathMove { x: -20; y: 180 + shapepath.t*50 }
            PathArc { x: 20; y: 180 + shapepath.t*50; radiusX: 20; radiusY: 20; useLargeArc: true }
            PathArc { x: -20; y: 180 + shapepath.t*50; radiusX: 20; radiusY: 20; useLargeArc: true }

            PathMove { x: -20+shapepath.t*20; y: 230 + shapepath.t*50 }
            PathArc { x: 20-shapepath.t*20; y: 230 + shapepath.t*50; radiusX: 20*(1.0-shapepath.t); radiusY: 20*(1.0-shapepath.t); useLargeArc: true }
            PathArc { x: -20+shapepath.t*20; y: 230 + shapepath.t*50; radiusX: 20*(1.0-shapepath.t); radiusY: 20*(1.0-shapepath.t); useLargeArc: true }            
        }
    }
}
```

Notice that instead of using a `NumberAnimation` on `t`, any other binding can be used, e.g. to a slider, an external state, and so on. Your imagination is the limit.
