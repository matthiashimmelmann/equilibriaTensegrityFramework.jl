# Stable Equilibria of Tensegrity Frameworks

Assume we are given a tensegrity framework given by nodes <img src="https://render.githubusercontent.com/render/math?math=V=[n]">
 and edges <img src="https://render.githubusercontent.com/render/math?math=E=\{ij:i,j\in[n]\}"> together with an embedding <img src="https://render.githubusercontent.com/render/math?math=p:V\rightarrow \mathbb{R}^d">. The edges are partitioned into rigid bars <img src="https://render.githubusercontent.com/render/math?math=b_{ij}"> with lengths <img src="https://render.githubusercontent.com/render/math?math=\ell_{ij}"> and elastic cables <img src="https://render.githubusercontent.com/render/math?math=c_{ij}"> with corresponding elasticity coefficients <img src="https://render.githubusercontent.com/render/math?math=e_{ij}"> and restings lenngths <img src="https://render.githubusercontent.com/render/math?math=r_{ij}">. With Hooke's Law and the introduction of slack variables <img src="https://render.githubusercontent.com/render/math?math=\delta_{ij}"> for the elastic cables, a polynomial system <img src="https://render.githubusercontent.com/render/math?math=G=g_{ij}"> consisting of the polynomials

<img src="https://render.githubusercontent.com/render/math?math=b_{ij}=||p(i)-p(j)||^2-\ell_{ij}^2">,  
<img src="https://render.githubusercontent.com/render/math?math=c_{ij}=||p(i)-p(j)||^2-\delta_{ij}^2">

arises. Using the corresponding potential energy function <img src="https://render.githubusercontent.com/render/math?math=Q=\sum e_{ij}\frac{}{2} (r_{ij}-\delta_{ij})^2"> enables us to create the Lagrange multiplier <img src="https://render.githubusercontent.com/render/math?math=\mathcal{L}=Q-\sum\lambda_{ij} g_{ij}">. Its derivative contains the critical points of the energy function subject to the variety generated by the polynomial system <img  src="https://render.githubusercontent.com/render/math?math=G">. We are interested in its real, local minima. The sufficient condition of local minima is verified by looking at the Lagrange multiplier <img src="https://render.githubusercontent.com/render/math?math=\mathcal{L}">'s Hessian and checking whether it is positive definite. This process is more thoroughly described in the underlying paper Catastrophe in Elastic Tensegrity Frameworks ([Heaton and Timme, 2020](https://arxiv.org/pdf/2009.13408.pdf "Tensegrity Catastrophe")).

The main method of this program is `stableEquilibria`. It is exported in the module `functionsForStableEquilibria` and can be used from the main module `tensegrityEquilibria`. The former also exports a test suite `start_demo()` that is automatically executed when importing the main module. The main method of this package `stableEquilibria` has the following inputs with corresponding expected format:
+ `vertices`: This array consists of the embedded nodes of the tensegrity framework, given by arrays. If the nodes either contain internal variables or control parameters, they are included here as `@var` types from the numerical algebraic geometry package `HomotopyContinuation`. The expected input format in this case is `[[p_11, ..., p_1d], ..., [p_m1, ..., p_md]]`
+ `unknownBars`: This array consists of the rigid bars of the framework that are not yet determined by the choice of vertices. The expected input format is `[[i, j, l_ij], ...]` for a bar between the embedded vertices `p(i)` and `p(j)`.
+ `unknownCables`: This array consists of the elastic cables of the framework that are not yet determined by the choice of vertices. The expected input format is `[[i, j, r_ij, e_ij], ...]`  for a cable between the embedded vertices `p(i)` and `p(j)`.
+ `listOfInternalVariables` is a flat list of all the internal parameters of the framework (`X` in the paper).
+ `listOfControlParameters` is a flat list of all the control parameters of the framework (`\Omega` in the paper).
+ `targetParams` is a flat initial configuration of the variables in `listOfControlParameters` of the same length. It contains entries of type `Float64`.
+ `knownBars` is a list of the already determined bars for plotting purposes. The expected input format is `[[i,j], ...]` for a bar between the embedded vertices <img src="https://render.githubusercontent.com/render/math?math=p(i)"> and <img src="https://render.githubusercontent.com/render/math?math=p(j)">.
+ `knownCables` is a list of the already determined cables for plotting purposes. The expected input format is `[[i,j], ...]` for a cable between the embedded vertices <img src="https://render.githubusercontent.com/render/math?math=p(i)"> and <img src="https://render.githubusercontent.com/render/math?math=p(j)">.

The output of this functions is primarily an interactive plot created using `Makie.jl` and a configuration of the involved in stable equilibrium. Interacting with the plot window is possible by moving a slider corresponding to each control parameter that was given as an argument to the method via `listOfControlParameters`. When changing the position of one of the sliders, a parameter homotopy of the underlying polynomial system is solved in real time. Depending on the system's size, this might take up to a few seconds. Nevertheless, this method is significantly faster than recalculating the solutions from scratch each time a slider is moved. Solving the polynomial system is made possible by the library [`HomotopyContinuation.jl`](https://www.juliahomotopycontinuation.org/ "HomotopyContinuation.jl"). 

As an example, consider a triangular bipyramid framework with an equilateral triangel as base two unknown nodes and a rigid bar of unknown length between them. This tensegrity framework can be realized by the input
```
@var p[1:6] ell
stableEquilibria([p[1:3],p[4:6],[0,1,0],[sin(2*pi/3),cos(2*pi/3),0],[sin(4*pi/3),cos(4*pi/3),0]], [[1,2,ell]],
    [[1,3,1,1],[1,4,1,1],[1,5,1,1],[2,3,1,1],[2,4,1,1],[2,5,1,1]], p, [ell], [1.0], [[3,4],[3,5],[4,5]], [])
```
The input let's us deduce that 
<img src="https://render.githubusercontent.com/render/math?math=e_{ij}=1,r_{ij}=1"> and that the target value of 
<img src="https://render.githubusercontent.com/render/math?math=b_{ij}=\ell_{ij}"> is also <img src="https://render.githubusercontent.com/render/math?math=1">. The output of the plotting routine would then be the following image:

<p align="center">
  <img src="https://user-images.githubusercontent.com/65544132/110790981-f8156880-8271-11eb-8e3d-6e157604a113.jpg">
</p>

The vertices of this framework are displayed in red, the rigid bars are black and the elastic cables are portrayed in blue. If there were any visibly different solutions to the polynomial system, in this case obtained by mirroring the unknown vertices <img src="https://render.githubusercontent.com/render/math?math=p(1)"> and <img src="https://render.githubusercontent.com/render/math?math=p(2)"> in the top and bottom of the image respectively, they would be transparently plotted in grey as "shadow vertices".
