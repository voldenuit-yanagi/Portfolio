<div align="center">
    <h1>Water</h1>
    <p><img alt="Static Badge for Software" src="https://img.shields.io/badge/Software-Unreal-CDCFEC"> <img alt="Static Badge for Techniques" src="https://img.shields.io/badge/Techniques-Screen--Space_Reflection_(SSR)%2C_Stencil Buffer%2C_Post_Process%2C_Decal%2C_Procedural_Generation_with_Noise%2C_Render_Target%2C_Dynamic_Material_Instance%2C_HLSL%2C_Blueprint%2C_C%2B%2B-C2D8EE"></p>
    <p>This project demonstrates a comprehensive approach to simulating water in Unreal Engine, covering surface behavior, underwater effects, and interactions with the environment and characters.</p>
    <p><img src="media/preview.gif" alt="Preview" title="Preview" height=500></p>
</div>


## Table of Contents <!-- omit from toc -->
- [Inspiration](#inspiration)
- [Key Features](#key-features)
- [Individual Sections](#individual-sections)
  - [Water Surface - Realistic water surface behavior, including waves, reflections, and refractions](#water-surface---realistic-water-surface-behavior-including-waves-reflections-and-refractions)
  - [Underwater Effects - The simulation of visual effects and lighting under the water](#underwater-effects---the-simulation-of-visual-effects-and-lighting-under-the-water)
  - [Water Interaction - Interactions between the environment/character and the water](#water-interaction---interactions-between-the-environmentcharacter-and-the-water)

## Inspiration
Water is a common feature in games, and it presents many well-known characteristics that make it a great learning opportunity. From natural water surfaces to the immersive experience of underwater exploration, I wanted to capture the subtle visual and interactive elements that make water behavior believable. My approach is rooted in real-world observations combined with techniques seen in modern game development. While aiming for realism, the water effects are not based on purely physics but also incorporate artistic considerations to enhance visual appeal while maintaining performance.

## Key Features
- Realistic water surface with dynamic waves, reflections, and refractions.
- Comprehensive underwater effects, including depth fog, distortion, and waterline.
- Advanced interaction with both the environment and characters, featuring caustics, foam, procedural water droplets on the lens and interactive wave simulation.

## Individual Sections

### [Water Surface](WaterSurface/README.md) - Realistic water surface behavior, including waves, reflections, and refractions
The water surface module features dynamic waves driven by noise-based displacement, and two types of reflections: screen-space reflections (SSR) for realism and a custom HDR-based reflection for compensating screen-space limitations. Refraction effects are achieved through screen-space sampling and controlled depth fades, and a Fresnel term is added for realism at the edges.
<p><img src="media/watersurface1.png" alt="Water Surface" title="Water Surface" width=500> <img src="media/watersurface2.png" alt="Water Surface" title="Water Surface" width=500></p>

### [Underwater Effects](UnderwaterEffects/README.md) - The simulation of visual effects and lighting under the water
The underwater module simulates visual effects like depth fog, viggnetting and underwater blur, adding immersive distortion as objects shift in perspective. A procedural waterline effect separates above-water and underwater views, enhancing the realism of transitioning between the two environments.
<p><img src="media/underwatereffects1.png" alt="Underwater Effects" title="Underwater Effects" width=500> <img src="media/underwatereffects2.png" alt="Underwater Effects" title="Underwater Effects" width=500></p>

### [Water Interaction](WaterInteraction/README.md) - Interactions between the environment/character and the water
The water not only includes environmental interactions such as caustics, foam near shallow water, and decal-based wetting for surrounding and underwater objects, but also create realistic feedback when characters move through or emerge from water, respectively with wave simuation on water surface and procedurally generated droplets on the camera lens.
<p><img src="media/waterinteraction1.png" alt="Water Interaction" title="Water Interaction" width=500> <img src="media/waterinteraction2.png" alt="Water Interaction" title="Water Interaction" width=500></p>

