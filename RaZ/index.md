---
layout: page
title: RaZ
permalink: /RaZ/
---

### Modern & multiplatform 3D game engine

RaZ is a personal project I started in mid-2017, in my last years of study. I was eager to learn about computer graphics, and was really interested in [image processing](https://github.com/Razakhel/ArcV "ArcV - GitHub") & [offline rendering](https://github.com/Razakhel/RaZtracer "RaZtracer - GitHub") at the time.

RaZ has originally started as a 3D rendering engine toy project, intended solely for learning purposes. However, the more I was working on it, the more I wanted to keep doing so. Over time, it has evolved to be now more of a (basic) game engine rather than a simple rendering one.

As every proper engine has an editor, RaZ also has its own, [RaZor](https://github.com/Razakhel/RaZor "RaZor - GitHub"). It does yet require a lot of work, as not much is possible at the moment, but several features are available and can be tinkered with.

If you're interested, feel free to explore (and star ⭐) the project on [GitHub](https://github.com/Razakhel/RaZ "RaZ - GitHub"). If you think of improvements or features you'd want to see in RaZ, don't hesitate to [file an issue](https://github.com/Razakhel/RaZ/issues/new "RaZ - Create issue") or [submit a pull request](https://github.com/Razakhel/RaZ/compare "RaZ - Submit PR")!

Some tutorials & explanations are available on the [GitHub's wiki](https://github.com/Razakhel/RaZ/wiki "RaZ - Wiki"). If you already know of RaZ and seek the documentation, you're in luck: it's [right here](doc/ "RaZ - Documentation")!

A Discord server is also available, feel free to drop by if you have any question or just want to say hi:

<iframe src="https://discordapp.com/widget?id=734342940960358446&theme=dark" width="250" height="300" allowtransparency="true" frameborder="0" sandbox="allow-popups allow-popups-to-escape-sandbox allow-same-origin allow-scripts"></iframe>

### Some examples

Below are shown several scenes and projects made with RaZ. Note that some have corresponding online demos; don't hesitate to try them out!

| **Crytek Sponza** (Blinn-Phong)                                                      | **Hylian shield** (PBR/Cook-Torrance) ([demo](demo/ "RaZ - Demo"))                   |
| :----------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------: |
| [![Crytek Sponza](https://i.imgur.com/Tr1nnjV.jpg)](https://i.imgur.com/Tr1nnjV.jpg) | [![Hylian shield](https://i.imgur.com/UZ90KKJ.jpg)](https://i.imgur.com/UZ90KKJ.jpg) |

| [**Atmos**](https://github.com/Razakhel/Atmos) - Atmospheric simulation ([demo](https://razakhel.github.io/Atmos/demo/)) | [**Midgard**](https://github.com/Razakhel/Midgard) - Terrain generation ([demo](http://razakhel.github.io/Midgard/demo/)) |
| :----------------------------------------------------------------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------------------: |
| [![Atmos](https://imgur.com/I52D5AK.png)](https://imgur.com/I52D5AK.png)                                                 | [![Midgard](https://imgur.com/esW8H1N.png)](https://imgur.com/esW8H1N.png)                                                |

### Features

RaZ is entirely written in C++, [scriptable in Lua](https://github.com/Razakhel/RaZ/wiki/Lua "RaZ - Wiki Lua"), and architectured as an ECS (Entity-Component-System). It is compatible with Windows, Linux & macOS, and can also be used for [web-based applications](demo/ "RaZ - Demo") using [Emscripten](https://emscripten.org/).

#### Animation

- Skeleton data structure
- Animation support (in progress)

#### Audio

- Using [OpenAL Soft](https://openal-soft.org/)
- Playing/pausing/stopping/repeating sounds
- Positional audio sources & listener
- Sound effects (reverberation, chorus, distortion, echo, ...)
- Audio input (microphone) mono/stereo support

#### Data

- [Bounding Volume Hierarchy (BVH)](https://en.wikipedia.org/wiki/Bounding_volume_hierarchy) acceleration structure
- [Directed graph](https://en.wikipedia.org/wiki/Directed_graph) structure
- Mesh signed distance field
- Dynamic bitset
- File formats:
    - Meshes:
        - [glTF/GLB](https://en.wikipedia.org/wiki/GlTF) import (using [fastgltf](https://github.com/spnda/fastgltf))
        - [OBJ](https://en.wikipedia.org/wiki/Wavefront_.obj_file) import/export
        - [FBX](https://en.wikipedia.org/wiki/FBX) import (using the [FBX SDK](https://www.autodesk.com/developer-network/platform-technologies/fbx))
        - [OFF](https://en.wikipedia.org/wiki/OFF_(file_format)) import
    - Images:
        - [PNG](https://en.wikipedia.org/wiki/PNG), [JPEG](https://en.wikipedia.org/wiki/JPEG), [BMP](https://en.wikipedia.org/wiki/BMP_file_format), [TGA](https://en.wikipedia.org/wiki/Truevision_TGA), [HDR](https://en.wikipedia.org/wiki/RGBE_image_format), [GIF](https://en.wikipedia.org/wiki/GIF), [PPM/PGM](https://en.wikipedia.org/wiki/Netpbm#File_formats), [PSD](https://en.wikipedia.org/wiki/Adobe_Photoshop#File_format), PIC import (using [stb_image](https://github.com/nothings/stb))
        - [PNG](https://en.wikipedia.org/wiki/PNG), [JPEG](https://en.wikipedia.org/wiki/JPEG), [BMP](https://en.wikipedia.org/wiki/BMP_file_format), [TGA](https://en.wikipedia.org/wiki/Truevision_TGA), [HDR](https://en.wikipedia.org/wiki/RGBE_image_format) export (using [stb_image_write](https://github.com/nothings/stb))
        - [TGA](https://en.wikipedia.org/wiki/Truevision_TGA) import
    - Audio: [WAV](https://en.wikipedia.org/wiki/WAV) import/export
    - Animation: [BVH](https://en.wikipedia.org/wiki/Biovision_Hierarchy) import (in progress)

#### Math

- Vectors, matrices & quaternions
- Angles (degrees/radians)
- Transformations (translation, rotation, scale)
- Noise ([Perlin](https://en.wikipedia.org/wiki/Perlin_noise), [Worley](https://en.wikipedia.org/wiki/Worley_noise))

#### Physics

- Shapes (line, plane, sphere, triangle, quad, AABB, OBB)
- Shape/shape collision checks (in progress)
- Ray/shape intersection checks (in progress)
- Rigid body simulation (in progress)

#### Rendering

- OpenGL (4.6-3.3)
- Vulkan (in progress)
- [PBR](https://en.wikipedia.org/wiki/Physically_based_rendering) (Cook-Torrance) & legacy ([Blinn-Phong](https://en.wikipedia.org/wiki/Blinn–Phong_reflection_model)) material models
- [Deferred rendering](https://en.wikipedia.org/wiki/Deferred_shading), using a custom render graph
- Post effects: [bloom](https://en.wikipedia.org/wiki/Bloom_(shader_effect)), [tone mapping](https://en.wikipedia.org/wiki/Tone_mapping), SSR, [SSAO](https://en.wikipedia.org/wiki/Screen_space_ambient_occlusion), ... (in progress)
- Tessellation & compute shaders support
- Camera (perspective/orthographic)
- Light sources (point & directional)
- Windowing (window, keyboard/mouse inputs with custom callbacks), using [GLFW](https://www.glfw.org/)
- Overlay, using [ImGui](https://github.com/ocornut/imgui) & [ImPlot](https://github.com/epezent/implot)
- [Cubemap](https://en.wikipedia.org/wiki/Cube_mapping)
- [Normal mapping](https://en.wikipedia.org/wiki/Normal_mapping)

#### Scripting

- [Lua](https://www.lua.org/about.html) scripting, using [Sol2](https://github.com/ThePhD/sol2)

#### Misc

- Custom [ECS (Entity Component System)](https://en.wikipedia.org/wiki/Entity_component_system) implementation
- Uniformized platform-dependent path strings
- Logging utilities
- Multithreading utilities, thread pool implementation & parallelization functions
- Plugin utilities, to load dynamic libraries
- Compiler, enum, string, file, floating-point & type utilities
- [Tracy](https://github.com/wolfpld/tracy) profiler integration
