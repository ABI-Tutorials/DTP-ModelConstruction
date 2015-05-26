
==================
Model Construction
==================

This tutorial is on model construction. We aim to learn about the design of the mesh topology, aligning a template model with some data, projections of data onto surfaces, convergence of mesh fitting and embedding material fields.

Overview
======== 

Model construction is an art not a science and this is especially true when it comes to fitting.  As an art it is not possible to create a full set of rules that set out the process of creating a model.  So in this tutorial we will take you through the process of model construction and the aspects that need to be considered.  This tutorial will follow an example on creating a mesh for the breast.  What we cover here is transferrable to other mesh creation problems when the right brush strokes are applied.

Here we will focus on fitting and the effects that discretisation and smoothing have on the final mesh. 

Task 1
======
 
In this task we consider the design of the mesh topology.  The target system for the mesh has a big influence on the design and there are a number of factors to consider.  When creating a mesh we first create an initial or template mesh that can be related to the target system somehow.  The somehow is discussed in the next tasks where we consider mesh fitting and alignment.  The topology of the mesh needs to accurately represent the geometric features of the tissue geometry.  A mesh is made up of discrete elements and they are required to represent different tissue types (e.g. soft tissue, muscles, bones).  So the mesh must be designed so that the elements can be aligned with the structures of the target system.  But that is not all we must also think about the boundary conditions of the model, the types of interactions between the model and the external environment i.e. what types of kinematic/pressure/contact constraints will be required?

In this tutorial we will consider the specific example of the breast.  In this case we are considering a model of the breast that will be suitable for tracking lumps or masses within the breast.  We must consider the tissue around the breast and the bones of the ribs.  We must also consider where our data for fitting the mesh is coming from.  The data for the meshes can come from a mammmogram or an MRI.  In the case of the mammogram source the contact constraints required between the breast and compression plates must be considered.

Using the MAP Client application load 'DTP Model Construction - Task 1' and execute the workflow.  Here we can see the anterior part of the torso including the left and right breast (excluding the arms and shoulders).  Take note of the two layers to the mesh this is to enable the fitting of the skin, muscle and rib interfaces.

Task 2
======

The Aligning the template model with data

Start with an existing template mesh and get user to align/register template model with a given set of data
Use a sliding bar to manually control scaling, rotation and translation for aligning the model 

The template model will need to be aligned and registered to a given set of data such that they are in close proximity to each other and co-exist in the same coordinate system
Types of transformations include scaling, rotation, translation
Both manual and automatic (e.g. k-d tree, matching landmark to target points, host mesh fitting) techniques available

Task 3
======

Nonlinear least squares fitting
 
Projections of data onto surfaces; different methods include closest, orthogonal projections, etc.
Important to check that data have correctly projected onto the appropriate surfaces and correct if needed
Introduce the concept of Sobelev smoothing: only use smoothing in under-determined problems/regions, whereby there is insufficient amount of data with respect to the DOF of the mesh

Perform 2D surface fitting using aligned template model and data
Fit surfaces to different tissue types: skin, muscle and rib
Visualisation of error projections with multiple iterations in real-time using OpenCMISS-ZINC
 
Task 4
======

Mesh convergence

Investigate the effects of mesh discretisation after initial fit
Using a set of meshes with increasing discretisation, visualise the changes in the fitted geometric model
 
After initial fit, decide if the current mesh discretisation is sufficient to represent the geometric features of the model.
Perform mesh refinement and convergence analysis if required.

Task 5
======

Investigate effects of applying Sobelev smoothing
Use a slider bar to change smoothing parameters and visualise the changes in the model
 
Task 6
======

Embedding material fields
 
Embedding tissue microstructure by fitting a field of collagen orientations or Langer’s lines to the skin layer of the model
Use a slider bar for changing fibre orientation in the data, perform fitting and visualise the fitted fibre fields
Outcomes/goals
fit an existing mesh to a given set of data?

Such as microstructural axes, mechanical properties, current density distribution
Intrinsic tissue microstructure (such as collagen fibre orientations) that give rise to anisotropic material properties can be embedded within the mesh
