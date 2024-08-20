# Wave Effect

In this more complex example, we will create a wave effect with the fragment shader. The waveform is based on the sinus curve and it influences the texture coordinates used for the color.

The qml file defines the properties and animation.

```
import QtQuick 2.5

Rectangle {
    width: 480; height: 240
    color: '#1e1e1e'

    Row {
        anchors.centerIn: parent
        spacing: 20
        Image {
            id: sourceImage
            width: 160; height: width
            source: "../assets/coastline.jpg"
        }
        ShaderEffect {
            width: 160; height: width
            property variant source: sourceImage
            property real frequency: 8
            property real amplitude: 0.1
            property real time: 0.0
            NumberAnimation on time {
                from: 0; to: Math.PI*2; duration: 1000; loops: Animation.Infinite
            }

            fragmentShader: "wave.frag.qsb"
        }
    }
}
```

The fragment shader takes the properties and calculates the color of each pixel based on the properties.

```
#version 440

layout(location=0) in vec2 qt_TexCoord0;

layout(location=0) out vec4 fragColor;

layout(std140, binding=0) uniform buf {
    mat4 qt_Matrix;
    float qt_Opacity;
    
    float frequency;
    float amplitude;
    float time;
} ubuf;

layout(binding=1) uniform sampler2D source;

void main() {
    vec2 pulse = sin(ubuf.time - ubuf.frequency * qt_TexCoord0);
    vec2 coord = qt_TexCoord0 + ubuf.amplitude * vec2(pulse.x, -pulse.x);
    fragColor = texture(source, coord) * ubuf.qt_Opacity;
}
```

The wave calculation is based on a pulse and the texture coordinate manipulation. The pulse equation gives us a sine wave depending on the current time and the used texture coordinate:

```glsl
vec2 pulse = sin(ubuf.time - ubuf.frequency * qt_TexCoord0);
```

Without the time factor, we would just have a distortion but not a traveling distortion like waves are.

For the color we use the color at a different texture coordinate:

```glsl
vec2 coord = qt_TexCoord0 + ubuf.amplitude * vec2(pulse.x, -pulse.x);
```

The texture coordinate is influenced by our pulse x-value. The result of this is a moving wave.

![image](assets/wave.png)

In this example we use a fragment shader, meaning that we move the pixels inside the texture of the rectangular item. If we wanted the entire item to move as a wave we would have to use a vertex shader.
