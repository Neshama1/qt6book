# Filling Shapes

A shape can be filled in a number of different ways. In this section we will have a look at the general filling rule, and also the various ways a path can be filled.

Qt Quick Shapes provides two filling rules controlled using the `fillRule` property of the `ShapePath` element. The different results are shown in the screenshot below. It can be set to either `ShapePath.OddEvenFill`, which is the default. This fills each part of the path individually, meaning that you can create a shape with holes in it. The alternative rule is the `ShapePath.WindingFill`, which fills everything between the extreme endpoints on each horizontal line across the shape. Regardless of the filling rule, the shape outline is then drawn using a pen, so even when using the winding fill rule, the outline is drawn inside the shape.

![](assets/automatic/fillmode.png)

The examples below demonstrate how to use the two fill rules as shown in the screenshot above.

```
Shape {
    ShapePath {
        strokeWidth: 3
        strokeColor: "darkgray"
        fillColor: "orange"
        
        fillRule: ShapePath.OddEvenFill
        
        PathPolyline {
            path: [
                Qt.point(100,  20),
                Qt.point(150, 180),
                Qt.point( 20,  75),
                Qt.point(180,  75),
                Qt.point( 50, 180),
                Qt.point(100,  20),
            ]
        }
    }
}
```

```
Shape {
    ShapePath {
        strokeWidth: 3
        strokeColor: "darkgray"
        fillColor: "orange"
        
        fillRule: ShapePath.WindingFill
        
        PathPolyline {
            path: [
                Qt.point(300,  20),
                Qt.point(350, 180),
                Qt.point(220,  75),
                Qt.point(380,  75),
                Qt.point(250, 180),
                Qt.point(300,  20),
            ]
        }
    }
}
```

Once the filling rule has been decided on, there are a number of ways to fill the outline. The various options are shown in the screenshot below. The various options are either a solid color, or one of the three gradients provided by Qt Quick.

![](assets/automatic/gradients.png)

To fill a shape using a solid color, the `fillColor` property of the `ShapePath` is used. Set it to a color name or code, and the shape is filled using it.

```
Shape {
    ShapePath {
        strokeWidth: 3
        strokeColor: "darkgray"
        
        fillColor: "lightgreen"
        
        startX: 20; startY: 140

        PathLine {
            x: 180
            y: 140
        }
        PathArc {
            x: 20
            y: 140
            radiusX: 80
            radiusY: 80
            direction: PathArc.Counterclockwise
            useLargeArc: true
        }
    }
}
```

If you do not want to use a solid color, a gradient can be used. The gradient is applied using the `fillGradient` property of the `ShapePath` element.

The first gradient we look at is the `LinearGradient`. It creates a linear gradient between the start and end point. The end points can be positioned anyway you like, to create, for instance, a gradient at an angle. Between the end points, a range of `GradientStop` elements can be inserted. These are put at a `position` from `0.0`, being the `x1, y1` position, to `1.0`, being the `x2, y2` position. For each such stop, a color is specified. The gradient then creates soft transitions between the colors.

{% hint style="info" %}
If the shape extends beyond the end points, the first or last color is either continued, or the gradient is repeated or mirrored. This behaviour is specified using the `spread` property of the `LinearGradient` element.
{% endhint %}

```
Shape {
    ShapePath {
        strokeWidth: 3
        strokeColor: "darkgray"
        
        fillGradient: LinearGradient {
            x1: 50; y1: 300
            x2: 150; y2: 280
            
            GradientStop { position: 0.0; color: "lightgreen" }
            GradientStop { position: 0.7; color: "yellow" }
            GradientStop { position: 1.0; color: "darkgreen" }
        }

        startX: 20; startY: 340

        PathLine {
            x: 180
            y: 340
        }
        PathArc {
            x: 20
            y: 340
            radiusX: 80
            radiusY: 80
            direction: PathArc.Counterclockwise
            useLargeArc: true
        }
    }
}
```

To create a gradient that spreads around an origin, a bit like a clock, the `ConicalGradient` is used. Here, the center point is specified using the `centerX` and `centerY` properties, and the starting angle is given using the `angle` property. The gradient stops are then spread from the given angle in a clockwise direction for 360 degrees.

```
Shape {
    ShapePath {
        strokeWidth: 3
        strokeColor: "darkgray"
        
        fillGradient: ConicalGradient {
            centerX: 300; centerY: 100
            angle: 45
            
            GradientStop { position: 0.0; color: "lightgreen" }
            GradientStop { position: 0.7; color: "yellow" }
            GradientStop { position: 1.0; color: "darkgreen" }
        }
        
        startX: 220; startY: 140

        PathLine {
            x: 380
            y: 140
        }
        PathArc {
            x: 220
            y: 140
            radiusX: 80
            radiusY: 80
            direction: PathArc.Counterclockwise
            useLargeArc: true
        }
    }
}
```

To instead create a gradient that forms circles, a bit like rings on the water, the `RadialGradient` is used. For it you specify two circles, the focal circle and the center. The gradient stops go from the focal circle to the center circle, and beyond those circles, the last color continues, is mirrored or repeats, depending on the `spread` property.

```
Shape {
    ShapePath {
        strokeWidth: 3
        strokeColor: "darkgray"
        
        fillGradient: RadialGradient {
            centerX: 300; centerY: 250; centerRadius: 120
            focalX: 300; focalY: 220; focalRadius: 10
            
            GradientStop { position: 0.0; color: "lightgreen" }
            GradientStop { position: 0.7; color: "yellow" }
            GradientStop { position: 1.0; color: "darkgreen" }
        }

        startX: 220; startY: 340

        PathLine {
            x: 380
            y: 340
        }
        PathArc {
            x: 220
            y: 340
            radiusX: 80
            radiusY: 80
            direction: PathArc.Counterclockwise
            useLargeArc: true
        }
    }
}
```

{% hint style="info" %}
The advanced user can use a fragment shader to fill a shape. This way, you have full freedom to how the shape is filled. See the Effects chapter for more information on shaders.
{% endhint %}
