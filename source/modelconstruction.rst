
==================
Model Construction
==================

Model construction consists of:

#. *Mesh Creation*: creating a finite element mesh with a topology and interpolation suited to describing the body of interest to the level of detail required.
#. *Geometric Fitting*: customising the geometry of the mesh to be physically realistic, or tailored to an individual.
#. *Material Field Definition*: describing and fitting additional spatially-varying fields which affect behaviour of the body e.g. material properties such as fibre orientations for anisotropic materials. 

This tutorial currently concentrates on geometric fitting, but gives overviews of the other topics. It uses the breast surface as the model for fitting, which is appropriate as customising breast models to individuals for simulation of deformation with change in pose, is used to assist co-locating regions of possible cancerous tissue from medical imaging made with different imaging devices which necessarily use different body poses.

Mesh Creation
=============

We are concerned with constructing models consisting of 'finite elements' -- simple shapes such as triangles, squares, cubes etc. -- which join together to form a 'mesh' which covers the body and describes its topology. Over the elements of the mesh we interpolate coordinates (and eventually other fields of interest) to give the model its 3-D shape and location. Usually mesh creation involves creating the elements and specifying at least initial coordinates for the model.

Common methods for creating the finite element mesh include:

#. Automatic mesh generation to boundaries described by segmented edges, point clouds or CAD models. Automated algorithms are usually limited to creating triangle (in 2-D) or tetrahedron (3-D) elements, often used with linear or quadratic basis functions only.
#. Generating part or all the topology from simple, standard shapes such as plates, blocks and tubes, then relying on fitting to the geometry. The models used in the geometric fitting steps below were all generated from a plate topology and the tutorial involves fitting their geometry.
#. Manually building elements by selecting/creating corner points ('nodes') in the correct order. To ease creating a valid model, tools can assist by locking on to points from a surface or point cloud segmented from real medical images.

Surface model can be automatically extruded to 3-D models (triangles become wedges, squares become cubes). Also, different, higher-order basis functions can be used over the same topology to give more degrees of freedom in the model. This is particularly important for studying the human body as it contains few straight lines. In many models of body parts we employ C\ :sub:`1`\ -continuous cubic Hermite bases to model their inherent smoothness, and the following breast fitting examples demonstrate this. There is a downside to using Hermite bases: it is painstaking work to properly connect the mesh in areas where the topology is non-trivial, such as apex points, armpits etc.

Mesh creation is a very involved topic; one needs to consider favourable alignment of elements with expected material behaviour, having sufficient density of elements to describe the problem with sufficient accuracy (the fitting examples below employ 2 different sized meshes to give some indication of the importance of mesh refinement) but overall one wishes to minimise the total number of degrees of freedom to reduce computation time. This tutorial does not go further into mesh creation at this time.

Geometric Fitting
=================

This tutorial concentrates on directly fitting generic models to data point clouds obtained from an earlier segmentation or other digitisation step. Other types of fitting not covered include:

* Fitting to modes from a Principal Component Analysis (where the variation in geometry over a population is reduced to a small number of significant mode shapes and lesser modes are discarded);
* Host-mesh fitting where the body is embedded in a coarse, smooth 'host' mesh, data is used to morph the host mesh and the embedded 'slave' mesh is moved with it.

Smoothfit Tool
--------------

Fitting the geometry of a model to a point cloud consists of 3 steps: model alignment, data point projection and the fitting itself, described in the following.

This tutorial uses the 'smoothfit' MAP client plugin for interactive fitting with these 3 steps. The inputs to smoothfit in a workflow are a model file and a point cloud file (each currently limited to EX or FieldML formats that can be read by OpenCMISS-Zinc). A practical workflow in the MAP client looks as follows, and requires only the input files to be specified (and step identifiers to be named):

.. figure:: _static/fitting-workflow.png
   :align: center
   :figwidth: 95%
   :width: 90%

   Geometric fitting workflow.

When the workflow is executed, the smoothfit interface is displayed showing the model as a semi-transparent surface and the point cloud as a cloud of small crosses. The initial view is of the 'align' step:

.. figure:: _static/fitting-align.png
   :align: center
   :figwidth: 95%
   :width: 90%

   Fitting step 1: Model alignment.

You can always rotate, pan and zoom the view using the standard OpenCMISS-Zinc controls of left, middle and right mouse button drag, click 'View All' to recentre the view and click 'Done' to close the step.

The first step in fitting is to scale the model and bring it into alignment with the point cloud; this is done so that the projections are as simple as possible (as described below). To scale and align the model in this step, hold down the Ctrl key as you left, middle and right mouse button drag in the window: this moves the model relative to the data cloud. Be aware that rotation is a little difficult and may take practice. Other controls include alignment reset, auto centre and Load button which will load a saved alignment. (The Save button can be disabled in the smoothfit configuration so tutorialsters don't accidentally wipe the good one that is saved for progressing to the next step!)

Often the shape of the model and point cloud make it pretty clear where to align to. Note that this tool uses manual alignment, but other tools may make it automatic (based on shape analysis) or semi-automatic (by identifying 3 or more points on the data cloud as being key points on the model, and automatically transforming to align with them).

Step 2 in fitting is to project the data points onto the nearest location on the aligned mesh. Switch to the second page (labelled '2. Project') in the tool bar and click on the 'Project Points' button. When projections are calculated the view changes to show error bars between the data points and their projections, coloured by magnitude, plus the on-screen display of mean and maximum error:

.. figure:: _static/fitting-project.png
   :align: center
   :figwidth: 95%
   :width: 90%

   Fitting step 2: Projecting data points.

The key point is that the projections are what the fitting aims to minimise, and if they don't agree on where a point on the mesh should move to, the fit will have problems. It's good if the projection lines are short, and it's bad if they cross over each other.

Switch to the next step '3. Fit' to configure and perform the fit. This is where fitting becomes less a science and more a dark art. The normal fit adjusts the coordinates to minimise the error bars; clicking the 'Perform Fit' button performs a single iteration and it may take multiple iterations to get close to the data. This is what the view looks like after a couple of iterations in the model shown:

.. figure:: _static/fitting-fit.png
   :align: center
   :figwidth: 95%
   :width: 90%

   Fitting step 3: Perform the fit

Note that the projections are not recalculated during the fitting, but you can switch back to step 2, reproject and then fit again.

The penalty values allow you to smooth the fit by penalising particular deformations. The strain penalty limits excessive strain in the model so in the absense of data (or in the presence of noisy data) the solution errs towards one with minimal deformation from the initial aligned state. The edge discontinuity penalty is only useful for non-C\ :sub:`1`\ -continuous coordinate fields such as the linear dome example later. Penalties always increase the data point projection error (in a least squares sense, which is the solution method it uses), but generally give a much more attractive result. Penalty values should be adjusted in orders of magnitude until a likeable result is obtained, then fine-tuned. It is often better to use stiffer (higher penalty) values for initial iterations to prevent waviness from developing in the mesh, then dropping for a final iteration. As for the align step, you can load and save (latter if enabled) the fitting options.

The following tutorial tasks each have a workflow associated with them which should be run in the usual way. 

Task 1: Coarse plate model fitted to breast data
------------------------------------------------

Task 2: Fine plate model fitted to breast data
----------------------------------------------

Task 3: Coarse breast model fitted to breast data
-------------------------------------------------

Task 4: Fine breast model fitted to noisy data
----------------------------------------------

Task 5: Fine breast model fitted to sparse, noisy data
------------------------------------------------------

Task 6: Bilinear model fitted to point cloud
--------------------------------------------


Material Field Fitting
======================

Embedding material fields
 
Embedding tissue microstructure by fitting a field of collagen orientations or Langer’s lines to the skin layer of the model
Use a slider bar for changing fibre orientation in the data, perform fitting and visualise the fitted fibre fields
Outcomes/goals
fit an existing mesh to a given set of data?

Such as microstructural axes, mechanical properties, current density distribution
Intrinsic tissue microstructure (such as collagen fibre orientations) that give rise to anisotropic material properties can be embedded within the mesh
