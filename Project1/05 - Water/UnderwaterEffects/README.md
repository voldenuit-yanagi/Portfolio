<div align="center">
    <h1>Water - Underwater Effects</h1>
    <p><img alt="Static Badge for Software" src="https://img.shields.io/badge/Software-Unreal-CDCFEC"> <img alt="Static Badge for Techniques" src="https://img.shields.io/badge/Techniques-Stencil Buffer%2C_Post_Process%2C_HLSL%2C_Material_Graph%2C_Blueprint-C2D8EE"></p>
    <p>This section covers the visual and technical underwater effects used to simulate the experience of being submerged in water. From distortion, viggnetting, depth fog, blur, to waterline and underwater view of surface, the effects are designed to create an immersive underwater environment in Unreal Engine.</p>
    <p>
      <picture>
          <source srcset="media/preview.webp" type="image/webp">
          <img src="media/preview.gif" alt="Preview" title="Preview" height=500>
      </picture>
    </p>
</div>


## Table of Contents <!-- omit from toc -->
- [Key Features](#key-features)
- [Investigation](#investigation)
- [Implementation Details](#implementation-details)
  - [Post Process Volume](#post-process-volume)
  - [Vignetting](#vignetting)
  - [Distortion](#distortion)
  - [Blur](#blur)
  - [Depth Fog](#depth-fog)
  - [Waterline](#waterline)
    - [Underwater Mask](#underwater-mask)
    - [Waterline Mask](#waterline-mask)
  - [Underwater View of Water Surface](#underwater-view-of-water-surface)
- [Future Work](#future-work)
- [References](#references)

## Key Features
- Underwater Distortion: Simulates light distortion caused by water movement.
- Vignetting: Adds darkened edges to enhance the feeling of depth and immersion.
- Depth Fog: Realistically represents how water depth affects the visibility and lighting.
- Waterline Effect: Smooth transition between above-water and underwater views.
- Underwater View of Surface: Simulates the view from underwater looking up at the water surface.

## Investigation
The main area of investigation was how to effectively implement the waterline. I explored three different solutions:

1. SceneCaptureComponent2D Method: This method involved placing a box with its top surface aligned with the water surface and enclosing the underwater area. The box had an outer black and inner white surface. A SceneCaptureComponent2D captured this box separately and wrote the result to a render target, producing the underwater mask. However, this approach incurred relatively high overhead due to the cost associated with using SceneCaptureComponent2D.

2. Stencil Test Method with Full Box: Another approach used a box with its bottom surface aligned with the water surface (stencil value 1) and a plane exactly at the water surface (stencil value 0). Using the stencil test, this method generated the underwater mask. The challenge here was that the box and plane had to match the water surface displacement. If the water area was large, this approach could lead to significant performance costs.

3. Dynamic Small Box with Stencil Test: This approach was similar to the second method using the stencil test, but involved a smaller box that didn’t cover the entire water area. Instead, the box was sized just enough to generate the mask within the camera's view and would dynamically move and rotate with the player. However, this method lacked the additional plane, causing abrupt changes in the mask when the height difference between the water surface and the camera exceeded a threshold, leading to less smooth transitions.

In the end, I had chosen a hybrid solution that combined the strengths of these methods. I used a smaller, localized box (stencil value 1) with its top surface aligned with the water and a small plane (stencil value 0) that overlapped with the box's top surface. This box moved and rotated with the player, and I used stencil testing to generate the underwater mask efficiently.

## Implementation Details
<p><img src="media/underwater_params.png" alt="Underwater Parameters" title="Underwater Parameters" width=400> <br> <img src="media/underwater_nodes.png" alt="Underwater Nodes" title="Underwater Nodes" width=800></p>

### Post Process Volume
A Post Process Volume had been placed in the scene and assigned the corresponding material instance, not set to unbound, with its volume confined to the underwater area.
<p><img src="media/ppvolume.png" alt="Post Process Volume" title="Post Process Volume" width=400></p>

### Vignetting
Based on the distance from the center of the screen to each pixel, an elliptical vignetting mask was calculated to darken the edges of the screen, emulating the natural light falloff seen in camera lenses. This effect enhanced the sense of immersion by subtly guiding the player's focus toward the center of the screen.
<p><img src="media/vignetting.png" alt="Vignetting" title="Vignetting" width=500> <img src="media/vignetting_nodes.png" alt="Vignetting Nodes" title="Vignetting Nodes" width=400></p>

### Distortion
For underwater distortion, I used a noise texture to distort the screen-space UV coordinates. This created the illusion of visual distortion of objects caused by the water. I applied a mask to limit the distortion to the center of the screen, ensuring that the effect doesn't appear on the screen's edges, where sampling issues might occur. The distortion strength was controlled by scene depth, with closer objects being less distorted than those farther away.
<p>
  <picture>
    <source srcset="media/distortion.webp" type="image/webp">
    <img src="media/distortion.gif" alt="Distortion" title="Distortion" width=500>
  </picture> <br> 
  <img src="media/distortion_nodes.png" alt="Distortion Nodes" title="Distortion Nodes" width=900>
</p>

### Blur
To simulate the blurriness of underwater objects, I applied a Gaussian blur to the underwater scene, which enhanced the diffusing of light underwater.
<p><img src="media/blur_comparison.png" alt="Blur" title="Blur" width=600> <img src="media/blur_nodes.png" alt="Blur Nodes" title="Blur Nodes" width=300> <br> <img src="media/blur_code1.png" alt="Blur Code" title="Blur Code" width=400> <img src="media/blur_code2.png" alt="Blur Code" title="Blur Code" width=500></p>

### Depth Fog
Depth fog was essential for representing underwater visibility. I used scene depth to calculate the fog intensity, making distant objects appear hazy and obscured. The further an object was from the camera, the more it was affected by the fog, simulating how light diminishes in deep water.
<p><img src="media/depthfog1.png" alt="Depth Fog" title="Depth Fog" width=500> <img src="media/depthfog2.png" alt="Depth Fog" title="Depth Fog" width=500> <br> <img src="media/depthfog_nodes1.png" alt="Depth Fog Nodes" title="Depth Fog Nodes" width=400></p>

### Waterline
<p>
  <picture>
    <source srcset="media/waterline.webp" type="image/webp">
    <img src="media/waterline.gif" alt="Waterline" title="Waterline" width=500>
  </picture> 
  <img src="media/waterline.png" alt="Waterline" title="Waterline" width=500> <br> 
  <img src="media/waterline_nodes.png" alt="Waterline Nodes" title="Waterline Nodes" width=650>
</p>

#### Underwater Mask
The underwater mask that differentiated between the above-water and underwater regions was created using a stencil buffer. I used two separate meshes in a blueprint actor: one smaller, localized box (stencil value 1) with its top surface aligned with the water and one small plane (stencil value 0) that overlapped (a small offset to avoid Z-fighting) with the box's top surface. By enabling stencil testing, I was able to generate a black-and-white mask, where areas above water are 0 and areas below water are 1. The actor were set to dynamically follow the character’s movement and rotation using a blueprint, ensuring that the mask accurately updated as the player's view changed when entering or exiting the water.
<p><img src="media/underwatermask.png" alt="Underwater Mask" title="Underwater Mask" width=500> <img src="media/maskactor.png" alt="Underwater Mask Actor" title="Underwater Mask Actor" width=350> <br> <img src="media/box_setting.png" alt="Underwater Mask Actor Box" title="Underwater Mask Actor Box" width=350><img src="media/plane_setting.png" alt="Underwater Mask Actor Plane" title="Underwater Mask Actor Plane" width=350> <img src="media/maskactor_bp.png" alt="Underwater Mask Actor Blueprint" title="Underwater Mask Actor Blueprint" width=600></p>

#### Waterline Mask
To achieve the waterline effect, I applied a blur to the underwater mask and then multiplied the original mask by its inverse (1-x), creating a smooth transition area where the waterline was located. The width and sharpness of the waterline were controlled by adjustable parameters, allowing for fine-tuning based on the desired visual style.
<p><img src="media/waterlinemask.png" alt="Waterline Mask" title="Waterline Mask" width=500></p>

### Underwater View of Water Surface
I simulated the underwater view of the surface by placing a plane with normals facing downwards and using the same displacement and normal maps with the water surface material to mimic the water surface's behavior from underneath. A simple HDR texture resembling the sky was used, and the vignetting mask was also applied here to enhance the brightness in the center and darken the edges.
<p><img src="media/backsurface.png" alt="Back Surface" title="Back Surface" width=500> <img src="media/backsurface_nodes.png" alt="Back Surface Nodes" title="Back Surface Nodes" width=1000></p>

## Future Work
One area of improvement will be the addition of ray marching for underwater volumetric lighting. Implementing this technique will enable more realistic light scattering and shafts of light penetrating through the water, and greatly enhance the visual immersion of underwater scenes by simulating how light interacts with particles in the water, creating a more dynamic and atmospheric effect.

## References
[1] https://www.youtube.com/watch?v=ZdTcFRrYpg8&list=TLPQMjAwOTIwMjQgXhoGhgKcBA&index=1

[2] https://www.youtube.com/watch?v=BNldzmlSvCA

[3] https://www.bilibili.com/video/BV1Ud4y1d78v/?spm_id_from=333.999

[4] https://www.bilibili.com/video/BV1hP4y1d7qY/?spm_id_from=333.788
