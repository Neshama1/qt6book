# Curtain Effect

In the last example for custom shader effects, I would like to bring you the curtain effect. This effect was published first in May 2011 as part of [Qt labs for shader effects](http://labs.qt.nokia.com/2011/05/03/qml-shadereffectitem-on-qgraphicsview/).

![image](assets/curtain.png)

At that time I really loved these effects and the curtain effect was my favorite out of them. I just love how the curtain opens and hide the background object.

For this chapter, the effect has been adapted for Qt 6. It has also been slghtly simplifed to make it a better showcase.

The curtain image is called `fabric.png`. The effect then uses a vertex shader to swing the curtain forth and back and a fragment shader to apply shadows to show how the fabric folds.

The diagram below shows how the shader works. The waves are computed through a sin curve with 7 periods (7\*PI=21.99…). The other part is the swinging. The `topWidht` of the curtain is animated when the curtain is opened or closed. The `bottomWidth` follows the `topWidth` using a `SpringAnimation`. This creates the effect of the bottom part of the curtain swinging freely. The calulated `swing` component is the strengh of the swing based on the y-component of the vertexes.

![image](assets/curtain\_diagram.png)

The curtain effect is implemed in the `CurtainEffect.qml` file where the fabric image act as the texture source. In the QML code, the `mesh` property is adjusted to make sure that the number of vertixes is increased to give a smoother result.

```
import QtQuick

ShaderEffect {
    anchors.fill: parent

    mesh: GridMesh {
        resolution: Qt.size(50, 50)
    }

    property real topWidth: open?width:20
    property real bottomWidth: topWidth
    property real amplitude: 0.1
    property bool open: false
    property variant source: effectSource

    Behavior on bottomWidth {
        SpringAnimation {
            easing.type: Easing.OutElastic;
            velocity: 250; mass: 1.5;
            spring: 0.5; damping: 0.05
        }
    }

    Behavior on topWidth {
        NumberAnimation { duration: 1000 }
    }


    ShaderEffectSource {
        id: effectSource
        sourceItem: effectImage;
        hideSource: true
    }

    Image {
        id: effectImage
        anchors.fill: parent
        source: "../assets/fabric.png"
        fillMode: Image.Tile
    }

    vertexShader: "curtain.vert.qsb"

    fragmentShader: "curtain.frag.qsb"
}
```

The vertex shader, shown below, reshapes the curtain based on the `topWidth` and `bottomWidth` properies, extrapolating the shift based on the y-coordinate. It also calculates the `shade` value, which is used in the fragment shader. The `shade` property is passed through an additional output in `location` 1.

```
#version 440

layout(location=0) in vec4 qt_Vertex;
layout(location=1) in vec2 qt_MultiTexCoord0;

layout(location=0) out vec2 qt_TexCoord0;
layout(location=1) out float shade;

layout(std140, binding=0) uniform buf {
    mat4 qt_Matrix;
    float qt_Opacity;
    
    float topWidth;
    float bottomWidth;
    float width;
    float height;
    float amplitude;
} ubuf;

out gl_PerVertex { 
    vec4 gl_Position;
};
 
void main() {
    qt_TexCoord0 = qt_MultiTexCoord0;

    vec4 shift = vec4(0.0, 0.0, 0.0, 0.0);
    float swing = (ubuf.topWidth - ubuf.bottomWidth) * (qt_Vertex.y / ubuf.height);
    shift.x = qt_Vertex.x * (ubuf.width - ubuf.topWidth + swing) / ubuf.width;

    shade = sin(21.9911486 * qt_Vertex.x / ubuf.width);
    shift.y = ubuf.amplitude * (ubuf.width - ubuf.topWidth + swing) * shade;

    gl_Position = ubuf.qt_Matrix * (qt_Vertex - shift);

    shade = 0.2 * (2.0 - shade) * ((ubuf.width - ubuf.topWidth + swing) / ubuf.width);
}
```

In the fragment shader below, the `shade` is picked up as an input in `location` 1 and is then used to calculate the `fragColor`, which is used to draw the pixel in question.

```
#version 440

layout(location=0) in vec2 qt_TexCoord0;
layout(location=1) in float shade;

layout(location=0) out vec4 fragColor;

layout(std140, binding=0) uniform buf {
    mat4 qt_Matrix;
    float qt_Opacity;
    
    float topWidth;
    float bottomWidth;
    float width;
    float height;
    float amplitude;
} ubuf;

layout(binding=1) uniform sampler2D source;

void main() {
    highp vec4 color = texture(source, qt_TexCoord0);
    color.rgb *= 1.0 - shade;
    fragColor = color;
}
```

The combination of QML animations and passing variables from the vertex shader to the fragment shader demonstrates how QML and shaders can be used together to build complex, animated, effects.

The effect itself is used from the `curtaindemo.qml` file shown below.

```
import QtQuick

Item {
    id: root
    width: background.width; height: background.height


    Image {
        id: background
        anchors.centerIn: parent
        source: '../assets/background.png'
    }

    Text {
        anchors.centerIn: parent
        font.pixelSize: 48
        color: '#efefef'
        text: 'Qt 6 Book'
    }

    CurtainEffect {
        id: curtain
        anchors.fill: parent
    }

    MouseArea {
        anchors.fill: parent
        onClicked: curtain.open = !curtain.open
    }
}
```

The curtain is opened through a custom `open` property on the curtain effect. We use a `MouseArea` to trigger the opening and closing of the curtain when the user clicks or taps the area.
