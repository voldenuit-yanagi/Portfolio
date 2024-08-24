<div align="center">
    <h1>Glass Bottle Material</h1>
    <p><img alt="Static Badge for Software" src="https://img.shields.io/badge/Software-Unity-C2D5C7"><img alt="Static Badge for Software" src="https://img.shields.io/badge/Software-Blender-E9CFCF"> <img alt="Static Badge for Techniques" src="https://img.shields.io/badge/Techniques-Shader_(Graph)%2C_Screen--Space_Refraction%2C_Translucent_Shadow%2C_Post Processing-C2D8EE"></p>
    <p>A realistic glass bottle material with refraction, reflection, surface details and translucent shadow in Unity's Universal Render Pipeline (URP).</p>
    <p><img src="media/demo.gif" alt="Demo" title="Demo" height=500></p>
</div>


## Table of Contents <!-- omit from toc -->
- [Inspiration](#inspiration)
- [Key Features](#key-features)
- [Implementation Details](#implementation-details)
  - [Model Preparation](#model-preparation)
  - [Shader Graph](#shader-graph)
  - [HLSL Shader](#hlsl-shader)
  - [Rendering Order](#rendering-order)
  - [Post-Processing](#post-processing)
- [Future Work](#future-work)
- [References](#references)

## Inspiration
This project was inspired by the common yet challenging task of creating realistic glass materials in real-time rendering. The unique properties of glass, such as transparent, refraction, reflection, surface imperfections, and translucent shadows, require careful handling. I aimed to develop a glass material that not only looks convincing but also performs efficiently in a game engine.

## Key Features
- Simulated thickness and screen-space refraction
- Surface detail enhancements with roughness and normal maps to simulate smudges and scratches
- Translucent shadows
- Reflection and specular highlights
- Separated inner and outer walls with an optimized rendering order to prevent transparency sorting issues
- Post-processing

<p><img src="media/feature.jpg" alt="Base Setting" title="Base Setting" height=350></p>

## Implementation Details

### Model Preparation
The glass bottle model was prepared in Blender to separate the inner and outer walls. This allowed for more precise control over the material properties in Unity and easy solving for transparency sorting issues.

### Shader Graph
1. **Thickness and Refraction**
    - **Simulated Thickness:** The glass refraction was apparent and darker at the edges, where the glass is thicker. Normals and view direction were used to calculate the perceived thickness, which was later applied as the alpha channel and processed for the multiplier for refraction. <p><img src="media/thickness.png" alt="Thickness" title="Thickness" height=250> <img src="media/thickness_nodes.png" alt="Thickness Nodes" title="Thickness Nodes" height=250></p>
    - **Screen-Space Refraction:** Refraction was primarily achieved using screen-space sampling. By calculating the refraction direction from the normals and view direction, and offsetting the absolute world space, the screen-space position was determined for sampling. <p><img src="media/refraction.png" alt="Refraction" title="Refraction" height=250> <img src="media/refraction_alpha.png" alt="Refraction Alpha" title="Refraction Alpha" height=250> <img src="media/refraction_nodes.png" alt="Refraction Nodes" title="Refraction Nodes" height=250></p>

2. **Reflection and Specular**

    Initially, I used a reflection probe to capture real-time reflections of the environment around the bottle. However, since the reflection details was unclear on the glass, I found it was unnecessary to use the reflection probe. The built-in reflections in the lit shader was sufficient with with better performance, and its "Preserve Specular Lighting" option was enabled to maintain the specular highlights in the transparent material. <p><img src="media/reflection.png" alt="Refection" title="Reflection" height=250></p>
    - Matcap Limitations: Matcap was initially considered but ultimately not used due to its limitations with camera and light source positions, which would have affected the realism of the glass material.

3. **Surface Details**

    To increase the realism of the glass material, a roughness map was used to simulate smudges, while a combined normal map was applied to create the appearance of fine scratches on the glass surface. <p><img src="media/details.png" alt="Details" title="Details" height=250> <img src="media/detail1.png" alt="Detail1" title="Detail1" height=250> <img src="media/detail2.png" alt="Detail2" title="Detail2" height=250> <img src="media/normal_nodes.png" alt="Normal Nodes" title="Normal Nodes" height=250> <img src="media/roughness_nodes.png" alt="Roughness Nodes" title="Roughness Nodes" height=250></p>

### HLSL Shader
To achieve the translucent shadows, I had to modify the default shadowcasting behavior of the Lit Shader Graph. Instead of relying on the URP ShadowCaster pass (ShadowCasterPass.hlsl), I generated the Shader Graph's .shader code and made custom adjustments to it. The final shader applied to the material is this modified version, rather than the one directly created by Shader Graph.
- **Translucent Shadows**

    Unlike the built-in pipeline, where the Standard shader supports semi-transparent shadows, URP does not support this by default. To simulate translucent shadows, a dither method was used. This approach leverages alpha clipping to handle transparency, which blends well with shadow casting. Instead of directly using Unity's built-in 3D dither texture, the hardcoded dither matrix provided by URP was employed for performance reasons. <p><img src="media/shadow1.png" alt="Shadow1" title="Shadow1" height=250> <img src="media/shadow2.png" alt="Shadow2" title="Shadow2" height=250> <img src="media/shadow_code1.png" alt="Shadow Code1" title="Shadow Code1" width=400> <img src="media/shadow_code2.png" alt="Shadow Code2" title="Shadow Code2" width=400></p>

### Rendering Order
To solve the transparency sorting issues commonly encountered with semi-transparent materials, the rendering order was carefully managed. The inner wall was rendered first (RenderQueue: 2998), followed by the liquid inside (RenderQueue: 2999), and finally the outer wall (RenderQueue: 3000). <p><img src="media/inside.png" alt="Inside" title="Inside" height=200> + <img src="media/liquid.png" alt="Liquid" title="Liquid" height=200> + <img src="media/outside.png" alt="Outside" title="Outside" height=200> = <img src="media/combine.png" alt="Combine" title="Combine" height=200></p>

### Post-Processing
Post-processing effects were added to correct the overexposure of the HDR skybox, and a bloom effect was applied to enhance the glow of the glass and other light-reflective surfaces. <p><img src="media/without_post.png" alt="Without Post-Processing" title="Without Post-Processing" height=250> → <img src="media/with_post.png" alt="With Post-Processing" title="With Post-Processing" height=250></p>

## Future Work
In the future, I plan to enhance the glass material by adding **caustics**, which will further improve the realism by simulating the light patterns created when light passes through the glass. Additionally, I aim to explore more advanced and realistic translucent shadow techniques, such as **Fourier Opacity Maps**, which could provide more accurate shadow representations compared to the current dithering method.

## References
https://www.youtube.com/watch?v=VMsOPUUj0JA

https://docs.unity3d.com/Packages/com.unity.shadergraph@16.0/manual/Dither-Node.html