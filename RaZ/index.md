---
layout: page
title: RaZ
permalink: /RaZ/
---

### Modern & multiplatform 3D/game engine

RaZ is a personal project I started in mid-2017, in my last years of study. I was eager to learn about computer graphics, and was really interested in <a href="https://github.com/Razakhel/ArcV" title="ArcV-GitHub">image processing</a> & <a href="https://github.com/Razakhel/RaZtracer" title="RaZtracer-GitHub">offline rendering</a> at the time.

RaZ was originally started as a 3D engine toy project, intended solely for learning purposes. However, the more I was working on it, the more I wanted to keep doing so. Over time, RaZ has evolved to be now more of a (basic) game engine rather than a simple 3D one.

If you're interested in the project, feel free to explore (and star!) the project on [GitHub](https://github.com/Razakhel/RaZ "RaZ - GitHub"). If you think of improvements or features you'd want to see in RaZ, don't hesitate to [fill an issue](https://github.com/Razakhel/RaZ/issues/new "RaZ - Create issue") or submit a pull request!

Some tutorials & explanations are available on the [GitHub's wiki](https://github.com/Razakhel/RaZ/wiki).

If you already know of RaZ and seek the documentation, you're in luck: it's [right here](doc/ "RaZ - Documentation")!

### Some examples

| **Crytek Sponza** (Blinn-Phong)                                                      |
| :----------------------------------------------------------------------------------: |
| [![Crytek Sponza](https://i.imgur.com/Tr1nnjV.jpg)](https://i.imgur.com/Tr1nnjV.jpg) |

| **Hylian shield** (PBR/Cook-Torrance)                                                |
| :----------------------------------------------------------------------------------: |
| [![Hylian shield](https://i.imgur.com/UZ90KKJ.jpg)](https://i.imgur.com/UZ90KKJ.jpg) |

### Features

RaZ is entirely written in C++17 and architectured as an ECS (Entity-Component-System). It is compatible with Windows, Linux & macOS, and uses CMake as its build system.

- Math:
  - Vectors
  - Matrices
  - Quaternions
  - Angles (degrees/radians)
  - Transformations (translation, rotation, scale)

- Rendering:
  - OpenGL 3.3 or 4.5
  - Vulkan _[in progress]_
  - Material models:
    - Blinn-Phong
    - Cook-Torrance PBR (metallic/roughness)
  - Camera (perspective/orthographic)
  - Light sources (point & directional)
  - Cubemap
  - Normal mapping
  - Render passes _[in progress]_

- Physics:
  - Shapes:
    - Line
    - Plane
    - Sphere
    - Triangle
    - Quad
    - AABB (axis-aligned bounding box)
    - OBB (oriented bounding box) _[in progress]_
  - Shape/shape collision checks _[in progress]_
  - Ray/shape intersection checks _[in progress]_
  - Rigid body simulation _[in progress]_

- Misc:
  - Meshes:
    - OBJ import/export
    - FBX import (using the FBX SDK)
    - OFF import
  - Images:
    - PNG import/export (using libpng)
    - TGA import
    - HDR import _[in progress]_
  - Windowing:
    - Window (using GLFW)
    - Overlay (using ImGui)
    - Keyboard/mouse inputs with custom callbacks
  - Threading:
    - Automatic by-thread split over a container (with either indices or iterators)
  - Debug:
    - [Pretty-printers](https://github.com/Razakhel/RaZ/wiki/Use-RaZ%27s-pretty-printers "RaZ - Pretty-printers") (debugger values formatter) are available with GDB
