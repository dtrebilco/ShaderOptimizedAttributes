# Shader Optimized Attributes

Below is a solution to a problem on deciding on vertex attributes and interpolators used in shaders in a generic material pipeline.
This was technique was shipped in games on X360/PS3 console generation - but could still be used today.


## The problem

 - deciding on what vertex attributes to support in the game
 - deciding if model materials should have a flag to say - include extra attribute - say second texture coordinate.

 - OK we need position
 - Texture coordinates - fine
 - Normal - sure - but what if vertex lit / light map lit
 - Color ?
 - Bi-Normal / Bi-Tangent? Is everything bump mapped? 
 - Light map coordinate?
 - Secondary UVs  - artists like to do things.

Then there are questions of precision - do we need full floats for position?
  or would f16 work with a scale and bias?
  Can color work with 32 bit - or do we need more precision

What about reuse of a mesh with multiple materials? In practice, 99% of all meshes only ever have one material

Initially - flags were passed to the material compiler and mesh compiler to select what attributes were going to be used, but this was constantly breaking with mismatches and inefficient attribute selection where attributes were selected and packed but not needed.  

## High level goal
  - Artists export all mesh attributes in full float precision into intermediate format
  - A cooking phase runs on assets to convert to a format to use at runtime
  - Mesh cooker finds the materials used on the mesh
  - Compiles the materials and finds what vertex attributes are used
  - Mesh only bakes the runtime attributes used - and in the needed precision


## Two pass shader compiling

 - First pass all inputs are to the stage are uniforms
 - Used uniforms are hooked up to inputs - others are just defined as constants 
 - Allows platform defines to define the vertex layouts
 - Allows low precision usages in cases where needed
 - Allow tight packing of interpolators




Compromises - If some models can have materials swapped at runtime 
            - list all materials - then do the union set of attributes
 - Some UI/PFX materials might still need a fixed vertex attribute format

 - Used on X30/PS3 level hardware
 - Used in forward renderer - deferred needs to write most attributes
 - Only really good if you have a cook phase for your shaders and meshes before the runtime format.
