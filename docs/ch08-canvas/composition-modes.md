# Composition Modes

Composition allows you to draw a shape and blend it with the existing pixels. The canvas supports several composition modes using the `globalCompositeOperation(mode)` operations. For instance:

* `source-over`
* `source-in`
* `source-out`
* `source-atop`

Let's begin with a short example demonstrating the exclusive or composition:

```
onPaint: {
    var ctx = getContext("2d")
    ctx.globalCompositeOperation = "xor"
    ctx.fillStyle = "#33a9ff"

    for(var i=0; i<40; i++) {
        ctx.beginPath()
        ctx.arc(Math.random()*400, Math.random()*200, 20, 0, 2*Math.PI)
        ctx.closePath()
        ctx.fill()
    }
}
```

The example below will demonstrate all composition modes by iterating over them and combining a rectangle and a circle. You can find the resulting output below the source code.

```
property var operation : [
    'source-over', 'source-in', 'source-over',
    'source-atop', 'destination-over', 'destination-in',
    'destination-out', 'destination-atop', 'lighter',
    'copy', 'xor', 'qt-clear', 'qt-destination',
    'qt-multiply', 'qt-screen', 'qt-overlay', 'qt-darken',
    'qt-lighten', 'qt-color-dodge', 'qt-color-burn',
    'qt-hard-light', 'qt-soft-light', 'qt-difference',
    'qt-exclusion'
    ]

onPaint: {
    var ctx = getContext('2d')

    for(var i=0; i<operation.length; i++) {
        var dx = Math.floor(i%6)*100
        var dy = Math.floor(i/6)*100
        ctx.save()
        ctx.fillStyle = '#33a9ff'
        ctx.fillRect(10+dx,10+dy,60,60)
        ctx.globalCompositeOperation = root.operation[i]
        ctx.fillStyle = '#ff33a9'
        ctx.globalAlpha = 0.75
        ctx.beginPath()
        ctx.arc(60+dx, 60+dy, 30, 0, 2*Math.PI)
        ctx.closePath()
        ctx.fill()
        ctx.restore()
    }
}
```

![image](assets/composite-operations.png)
