<div align="center">
    <h1>Procedual Ice Cream Cone</h1>
    <p><img alt="Static Badge for Software" src="https://img.shields.io/badge/Software-Houdini-E4D0C1"> <img alt="Static Badge for Techniques" src="https://img.shields.io/badge/Techniques-Procedural_Modeling%2C_VDB_Operations%2C_Copy_to_Points%2C_VEX_Scripting-C2D8EE"></p>
    <p>A procedural ice cream cone created in Houdini, featuring a waffle cone, three different types of ice cream scoops, and a decorative biscuit stick. The project also demonstrates a procedural method for placing a varying number of ice cream scoops on the cone in a visually appealing and logical manner.</p>
    <p><img src="media/scoop_number.gif" alt="Scoop Number" title="Scoop Number" height=500> <br> Scoop Number: Controls the number of ice cream scoops, with the scoop positions dynamically adjusting accordingly.</p>
    <p><img src="media/cone_radius.gif" alt="Cone Radius" title="Cone Radius" height=500> <br> Cone Radius: Controls the size of the cone, which the scoop size automatically adjusts with.</p>
</div>


## Table of Contents <!-- omit from toc -->
- [Inspiration](#inspiration)
- [Key Features](#key-features)
- [Implementation Details](#implementation-details)
  - [Waffle Cone](#waffle-cone)
  - [Ice Cream Scoops](#ice-cream-scoops)
  - [Decorative Biscuit Stick](#decorative-biscuit-stick)
  - [Scoop Positioning](#scoop-positioning)
- [Future Improvements](#future-improvements)

## Inspiration
The inspiration for this procedural ice cream cone project came from my deep love for ice cream. Recently, I came across some adorable ice cream illustrations that sparked my creativity. The vibrant colors, playful designs, and sheer joy that these illustrations evoked made me want to bring a similar concept to life in 3D. As I was just beginning my journey with Houdini, I saw this project as the perfect opportunity to dive into procedural modeling. I aimed to create a system that could not only generate a variety of ice cream cones but also allow for customization in the number of scoops with different flavors and decorations. This project became a fun and challenging way to combine my passion for ice cream with my newfound skills in Houdini, resulting in a delightful and versatile digital creation.

## Key Features
- Procedural waffle cone with irregular damage simulation
- Three distinct ice cream scoop styles: chocolate drizzle, sugar sprinkles, and embedded fruit pieces with a bitten effect
- Dynamic parameter controls for cone radius and number of scoops, with automatic adjustments to scoop size and positioning
- Procedural placement of varying numbers of ice cream scoops on the cone
- Decorative biscuit stick with procedural striped pattern

## Implementation Details

### Waffle Cone
- **Base Shape:** Created by extruding a grid with `polyextrude` to form the basic waffle structure. <p><img src="media/cone_base.png" alt="Cone Base Shape" title="Cone Base Shape" height=250> <img src="media/cone_base_nodes.png" alt="Cone Base Shape Nodes" title="Cone Base Shape Nodes" height=250></p>

- **Damaging Particles:** Achieved by scattering points on the grid, modifying them with `pointwrangle` to add size variation, and using `copytopoints` to add small particles with `mountain` applied to create bumpy surfaces. <p><img src="media/cone_particles.png" alt="Cone Damaging Particles" title="Cone Damaging Particles" height=250> <img src="media/cone_particles_nodes.png" alt="Cone Damaging Particles Nodes" title="Cone Damaging Particles Nodes" height=250></p>

- **Waffle Pattern Formation:** The waffle grid and scattered particles were combined using VDB to simulate slight irregularities and minor damage. After merging, a `pointvop` was used to add noise to the `P` attribute, and another `pointvop` was applied to assign two slightly different colors to the waffle structure. <p><img src="media/cone_waffle.png" alt="Cone Waffle Pattern" title="Cone Waffle Pattern" height=250> <img src="media/cone_waffle_nodes.png" alt="Cone Waffle Pattern Nodes" title="Cone Waffle Pattern Nodes" height=250> <img src="media/cone_vop1.png" alt="Cone VOP1" title="Cone VOP1" height=250> <img src="media/cone_vop2.png" alt="Cone VOP2" title="Cone VOP2" height=250></p>

- **Cone Shaping:** The waffle structure was finally transformed into a cone shape using `bend` and `lineartaper`. <p><img src="media/cone_shaping.png" alt="Cone Shaping" title="Cone Shaping" height=250> <img src="media/cone_shaping_nodes.png" alt="Cone Shaping Nodes" title="Cone Shaping Nodes" height=250></p>

### Ice Cream Scoops
- **Base Shape:** An uneven sphere was created with `mountain` and applied with a different `color`. Three variations were made:
  
  1. **Chocolate Drizzle:**
     - Added noise to normals using `attribnoise` and created a `group` to select the points on the upper part of the sphere based on their directions. <p><img src="media/scoop1_group.png" alt="Scoop1 Group" title="Scoop1 Group" height=250> <img src="media/scoop1_group_nodes.png" alt="Scoop1 Group Nodes" title="Scoop1 Group Nodes" height=250></p>

     - Assigned a weight attribute to these points and applied `attribblur` and `attribremap` to smooth and remap the weights. <p><img src="media/scoop1_weight.png" alt="Scoop1 Weight" title="Scoop1 Weight" height=250> <img src="media/scoop1_weight_nodes.png" alt="Scoop1 Weight Nodes" title="Scoop1 Weight Nodes" height=250></p>

     - Scattered points by density specified by weights, and remapped the weights to the `pscale` attribute, adding noise to `pscale` for variation. <p><img src="media/scoop1_scatter.png" alt="Scoop1 Scatter" title="Scoop1 Scatter" height=250> <img src="media/scoop1_scatter_nodes.png" alt="Scoop1 Scatter Nodes" title="Scoop1 Scatter Nodes" height=250></p>

     - Converted the particles to a VDB, and used `vdbreshape` with `vdbsmooth` to refine the shape. <p><img src="media/scoop1_vdb.png" alt="Scoop1 VDB" title="Scoop1 VDB" height=250> <img src="media/scoop1_vdb_nodes.png" alt="Scoop1 VDB Nodes" title="Scoop1 VDB Nodes" height=250></p>
  
     - Converted the VDB back to geometry and applied `attribblur` to the position and normal attributes to further smooth the geometry and then finally assigned a chocolate color to the geometry using a `color` node and merged it with the main part of the scoop. <p><img src="media/scoop1_final1.png" alt="Scoop1 Final1" title="Scoop1 Final1" height=250> <img src="media/scoop1_final2.png" alt="Scoop1 Final2" title="Scoop1 Final2" height=250> <img src="media/scoop1_final_nodes.png" alt="Scoop1 Final Nodes" title="Scoop1 Final Nodes" height=250></p>

  2. **Sugar Sprinkles:**
     - Selected the upper part of the sphere based on normals, then scattered points and randomized attributes such as scale, orientation, and color. <p><img src="media/scoop2_scatter.png" alt="Scoop2 Scatter" title="Scoop2 Scatter" height=250> <img src="media/scoop2_scatter_nodes.png" alt="Scoop2 Scatter Nodes" title="Scoop2 Scatter Nodes" height=250></p>

     - Copied a sugar sprinkle model (created with `tube` and refined with `polybevel`) to the scattered points using `copytopoints`, and then merged them with the scoop. <p><img src="media/scoop2_final1.png" alt="Scoop2 Final1" title="Scoop2 Final1" height=250> <img src="media/scoop2_final2.png" alt="Scoop2 Final2" title="Scoop2 Final2" height=250> <img src="media/scoop2_final_nodes.png" alt="Scoop2 Final Nodes" title="Scoop2 Final Nodes" height=250></p>

  3. **Fruit Pieces with a Bite:**
     - Created a "bitten" effect using `boolean` operations between another sphere and the ice cream scoop. <p><img src="media/scoop3_bite.png" alt="Scoop3 Bite" title="Scoop3 Bite" height=250> <img src="media/scoop3_bite_nodes.png" alt="Scoop3 Bite Nodes" title="Scoop3 Bite Nodes" height=250></p>
     - Used `pointsfromvolume` to generate points within the bitten scoop, then randomized scale and orientation. <p><img src="media/scoop3_scatter.png" alt="Scoop3 Scatter" title="Scoop3 Scatter" height=250> <img src="media/scoop3_scatter_nodes.png" alt="Scoop3 Scatter Nodes" title="Scoop3 Scatter Nodes" height=250></p>
     - Copied fruit piece models (created with `box`) to the points and merged them with the scoop. <p><img src="media/scoop3_final1.png" alt="Scoop3 Final1" title="Scoop3 Final1" height=250> <img src="media/scoop3_final2.png" alt="Scoop3 Final2" title="Scoop3 Final2" height=250> <img src="media/scoop3_final_nodes.png" alt="Scoop3 Final Nodes" title="Scoop3 Final Nodes" height=250></p>

### Decorative Biscuit Stick
- **Base Shape:** Started with a `tube` and used VEX to select alternating columns, assigning different colors to create a striped effect. <p><img src="media/stick.png" alt="Stick" title="Stick" height=250> <img src="media/stick_nodes.png" alt="Stick Nodes" title="Stick Nodes" height=250> <img src="media/stick_vex.png" alt="Stick VEX" title="Stick VEX" height=250></p>

### Scoop Positioning
The main challenge in this demo was to procedurally place a varying number of ice cream scoops on the cone logically. To address this, I followed these steps:
<p><img src="media/positioning_nodes.png" alt="Positioning Nodes" title="Positioning Nodes" height=250></p>

- **Defining Scoop Region and Scattering Points:** Defined a general region for the scoop centers using a sphere, which was then converted to a volume with `isooffset` node, and scattered points within the volume, representing the potential positions for the scoops. <p><img src="media/positioning_volume.png" alt="Positioning Volume" title="Positioning Volume" height=250></p>

- **Adjusting Positions with VEX:** Adjusted the positions of these points to create a spiraling upward distribution with gradually decreasing radius, using using `attribwrangle` VEX. <p><img src="media/positioning_vex.png" alt="Positioning VEX" title="Positioning VEX" height=250></p>

- **Assigning Geometry Index and Placing:**  Assigned a `geom_index` corresponding to different scoop to each point, which allowed the `copytopoints` operation to accurately place each scoop at its designated position, ensuring a balanced and natural arrangement. <p><img src="media/positioning_merge.png" alt="Positioning Merge" title="Positioning Merge" height=250></p>

## Future Improvements
Future enhancements could include adding more variations in the ice cream types, such as different textures and flavors, as well as additional decorations like fruits or syrups. I also plan to improve the procedural controls, making them more intuitive and flexible, allowing for even greater customization options.
