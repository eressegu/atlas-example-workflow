---
layout: lesson
root: .  # Is the only page that doesn't follow the pattern /:path/index.html
permalink: index.html  # Is the only page that doesn't follow the pattern /:path/index.html
---
The ATLAS software, called *athena*, itself is a very large project containing many packages. To help define all of the dependencies, CMake is used to manage the build process. To make a developer's life easier, athena defines several custom macros. This lesson describes how to use these custom macros to build an user package.


> ## Prerequisites
>
> This assumes that you'll have a basic knowledge of git, cmake, for example:
>
> 1. How to add git submodules
> 2. How to generate a `Makefile` from a `CMakeLists.txt`
>
> This lesson will also build on the `AnalysisPayload` project started in the previous ATLAS GitLab lesson.
{: .prereq}

{% include links.md %}
