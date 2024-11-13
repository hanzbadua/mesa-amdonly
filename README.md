# Mesa - modern AMD GPUs only, latest commit

Modified versions of [mesa-git (AUR)](https://aur.archlinux.org/packages/mesa-git) and [lib32-mesa-git (AUR)](https://aur.archlinux.org/packages/lib32-mesa-git), but only with support for modern AMD gpus (`radeonsi`) and compiled using branch `main`, and also Wayland-only (no X11 support built)

Additionally compiles with `-O3 -march=native` optimizations and alongside the appropriate AMD OpenGL + Vulkan drivers, also comes with the software rasterizer (`swrast`) and `zink` for OpenGL on Vulkan

Uses system LLVM (extra repo), and comes with rusticl as the OpenCL driver (clover is not compiled) - you may have to add the following to `/etc/environment` and restart to ensure rusticl is loaded as the OpenCL driver:
```
RUSTICL_ENABLE=radeonsi
OCL_ICD_VENDORS=/etc/OpenCL/vendors/rusticl.icd
```

To compile and install both the 64 bit and 32 bit versions:
```
git clone https://github.com/hanzbadua/mesa-amdonly.git
cd mesa-amdonly
makepkg -si
cd lib32-version
makepkg -si
```

