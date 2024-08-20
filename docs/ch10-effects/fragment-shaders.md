# Fragment Shaders

The fragment shader is called for every pixel to be rendered. In this chapter, we will develop a small red lens which will increase the red color channel value of the source.

## Setting up the scene

First, we set up our scene, with a grid centered in the field and our source image be displayed.

```
import QtQuick

Rectangle {
    width: 480; height: 240
    color: '#1e1e1e'

    Grid {
        anchors.centerIn: parent
        spacing: 20
        rows: 2; columns: 4
        Image {
            id: sourceImage
            width: 80; height: width
            source: '../../assets/tulips.jpg'
        }
    }
}
```

![image](assets/redlense1.png)

## A red shader

Next, we will add a shader, which displays a red rectangle by providing for each fragment a red color value.

```
#version 440

layout(location=0) in vec2 qt_TexCoord0;

layout(location=0) out vec4 fragColor;

layout(std140, binding=0) uniform buf {
    mat4 qt_Matrix;
    float qt_Opacity;
} ubuf;

layout(binding=1) uniform sampler2D source;

void main() {
    fragColor = vec4(1.0, 0.0, 0.0, 1.0) * ubuf.qt_Opacity;
}
```

In the fragment shader we simply assign a `vec4(1.0, 0.0, 0.0, 1.0)`, representing the color red with full opacity (alpha=1.0), to the `fragColor` for each fragment, turning each pixel to a solid red.

![image](assets/redlense2.png)

## A red shader with texture

Now we want to apply the red color to each texture pixel. For this, we need the texture back in the vertex shader. As we don’t do anything else in the vertex shader the default vertex shader is enough for us. We just need to provide a compatible fragment shader.

```
#version 440

layout(location=0) in vec2 qt_TexCoord0;

layout(location=0) out vec4 fragColor;

layout(std140, binding=0) uniform buf {
    mat4 qt_Matrix;
    float qt_Opacity;
} ubuf;

layout(binding=1) uniform sampler2D source;

void main() {
    fragColor = texture(source, qt_TexCoord0) * vec4(1.0, 0.0, 0.0, 1.0) * ubuf.qt_Opacity;
}
```

The full shader contains now back our image source as variant property and we have left out the vertex shader, which if not specified is the default vertex shader.

In the fragment shader, we pick the texture fragment `texture(source, qt_TexCoord0)` and apply the red color to it.

![image](assets/redlense3.png)

## The red channel property

It’s not really nice to hard code the red channel value, so we would like to control the value from the QML side. For this we add a _redChannel_ property to our shader effect and also declare a `float redChannel` inside the uniform buffer of the fragment shader. That is all that we need to do to make a value from the QML side available to the shader code.

{% hint style="info" %}
Notice that the `redChannel` must come after the implicit `qt_Matrix` and `qt_Opacity` in the uniform buffer, `ubuf`. The order of the parameters after the `qt_` parameters is up to you, but `qt_Matrix` and `qt_Opacity` must come first and in that order.
{% endhint %}

```
#version 440

layout(location=0) in vec2 qt_TexCoord0;

layout(location=0) out vec4 fragColor;

layout(std140, binding=0) uniform buf {
    mat4 qt_Matrix;
    float qt_Opacity;
    
    float redChannel;
} ubuf;

layout(binding=1) uniform sampler2D source;

void main() {
    fragColor = texture(source, qt_TexCoord0) * vec4(ubuf.redChannel, 1.0, 1.0, 1.0) * ubuf.qt_Opacity;
}
```

To make the lens really a lens, we change the _vec4_ color to be _vec4(redChannel, 1.0, 1.0, 1.0)_ so that the other colors are multiplied by 1.0 and only the red portion is multiplied by our _redChannel_ variable.

![image](assets/redlense4.png)

## The red channel animated

As the _redChannel_ property is just a normal property it can also be animated as all properties in QML. So we can use QML properties to animate values on the GPU to influence our shaders. How cool is that!

```
ShaderEffect {
    id: effect4
    width: 80; height: width
    property variant source: sourceImage
    property real redChannel: 0.3
    visible: root.step>3
    NumberAnimation on redChannel {
        from: 0.0; to: 1.0; loops: Animation.Infinite; duration: 4000
    }

    fragmentShader: "red3.frag.qsb"
}
```

Here the final result.

![image](assets/redlense5.png)

The shader effect on the 2nd row is animated from 0.0 to 1.0 with a duration of 4 seconds. So the image goes from no red information (0.0 red) over to a normal image (1.0 red).

## Baking

Again, we need to bake the shaders. The following commands from the command line does that:

```
qsb --glsl 100es,120,150 --hlsl 50 --msl 12 -o red1.frag.qsb red1.frag
qsb --glsl 100es,120,150 --hlsl 50 --msl 12 -o red2.frag.qsb red2.frag
qsb --glsl 100es,120,150 --hlsl 50 --msl 12 -o red3.frag.qsb red3.frag
```
