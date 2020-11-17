Affine Particle in Cell in 2D
================
![Screenshot](http://www.cs.columbia.edu/cg/raymond/apic2d.jpg)
APIC2D is an educational project to illustrate the affine-particle-in-cell algorithm in 2D, for water simulation.

The papers implemented here include:

Jiang, Chenfanfu, et al. "The affine particle-in-cell method." ACM Transactions on Graphics (TOG) 34.4 (2015): 51.

Batty, Christopher, Florence Bertails, and Robert Bridson. "A fast variational framework for accurate solid-fluid coupling." ACM Transactions on Graphics (TOG). Vol. 26. No. 3. ACM, 2007.

Ando, Ryoichi, Nils Thurey, and Reiji Tsuruno. "Preserving fluid sheets with adaptively sampled anisotropic particles." IEEE transactions on visualization and computer graphics 18.8 (2012): 1202-1214.

Brackbill, Jeremiah U., and Hans M. Ruppel. "FLIP: A method for adaptively zoned, particle-in-cell calculations of fluid flows in two dimensions." Journal of Computational physics 65.2 (1986): 314-343.

The general FLIP integrator is contributed by Qi Guo.

It contains multiple integrators that you may switch and compare through changing the `integration_scheme` variable in the code. Its value can be one of the following:
```
IT_PIC: original particle-in-cell (PIC)
IT_FLIP_BRACKBILL: Jeremiah U. Brackbill's FLIP scheme
IT_FLIP_BRIDSON: Robert Bridson's FLIP scheme
IT_FLIP_JIANG: Chenfanfu Jiang's FLIP scheme
IT_APIC: affine particle-in-cell (APIC)
IT_AFLIP_BRACKBILL: Affine version of Brackbill's FLIP
IT_AFLIP_BRIDSON: Affine version of Bridson's FLIP
IT_AFLIP_JIANG: Affine version of Jiang's FLIP
```

We also noticed that each of these integrators is a specific case of a more general one, where we have a general integrator for affine FLIP (AFLIP) integration (refer to the `map_g2p_aflip_general` function in the code):
```
Vector2s next_grid_velocity = get_velocity(p.x);
Vector2s original_grid_velocity = get_saved_velocity(p.x);
Vector2s lagrangian_velocity = p.v;

p.v = next_grid_velocity +
      (lagrangian_velocity - original_grid_velocity) * lagrangian_ratio;
p.c = get_affine_matrix(p.x) * affine_ratio;
p.x += (original_grid_velocity +
        (next_grid_velocity - original_grid_velocity) * eulerian_symplecticity +
        (lagrangian_velocity - original_grid_velocity) * lagrangian_ratio *
            lagrangian_symplecticity) *
       dt;
```
where `p.v` is the Lagrangian particle velocity, `p.c` is the specific angular momentum tensor used in affine integration, `p.x` is the particle position. The PIC then corresponds to AFLIP with zero Lagrangian ratio (how much Lagrangian velocity is preserved), zero affine ratio (how much affine momentum is preserved), full Eulerian symplecticity (how symplectic the Eulerian part of the velocity is), and zero Lagrangian sympecticity (how symplectic the Lagrangian part of the velocity is).

Dependencies
--------------------
APIC2D depends on the Eigen libraries (included) as well as GLUT for simple visualization.

Compilation
-----------------
To compile APIC2D, you'll need CMake on Mac OS X or Linux, or CMake-GUI (https://cmake.org) on Windows.

On Mac OS X or Linux:
1. make a directory, say, *build*, with *mkdir build*, enter the *build* directory, type *cmake ..*
2. Optionally you can adjust the options with *ccmake .*
3. type *make* to compile the code. For speeding up the compilation process you may use *make -j*.

On Windows:
1. open CMake-GUI, enter the correct directory for source code and build. Then click *Configure*, choose your installed version of the Microsoft Visual Studio.
2. after configuration you may find several libraries not found, check the *Advanced* box and locate those missing libraries manually. Please make sure you have picked the libraries corresponding to the architecture you have selected (say, 32-bit libraries for x86, and 64-bit libraries for x64).
3. click generate after fixing all missing variables to generate your Visual Studio solution.
4. open the solution and compile the code.
