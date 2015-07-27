
==================
Model Construction
==================

Model construction consists of:

#. *Mesh Creation*: creating a finite element mesh with a topology and interpolation suited to describing the body of interest to the level of detail required.
#. *Geometric Fitting*: customising the geometry of the mesh to be physically realistic, or tailored to an individual.
#. *Material Field Definition*: describing and fitting additional spatially-varying fields which affect behaviour of the body e.g. material properties such as fibre orientations for anisotropic materials. 

This tutorial currently concentrates on geometric fitting, but gives overviews of the other topics. It uses the breast surface as the model for fitting, which is appropriate as customising breast models to individuals is needed to simulate deformation with change in pose, and this in turn is helps co-locate regions of possible cancerous tissue from multiple medical images each made with different imaging devices which necessarily use different body poses.

Mesh Creation
=============

We are concerned with constructing models consisting of 'finite elements' -- simple shapes such as triangles, squares, cubes etc. -- which join together to form a 'mesh' which covers the body and describes its topology. Over the elements of the mesh we interpolate coordinates (and eventually other fields of interest) to give the model its 3-D shape and location. Usually mesh creation involves creating the elements and specifying at least initial coordinates for the model.

Common methods for creating the finite element mesh include:

#. Automatic mesh generation to boundaries described by segmented edges, point clouds or CAD models. Automated algorithms are usually limited to creating triangle (in 2-D) or tetrahedron (3-D) elements, with linear or quadratic basis functions only.
#. Generating part or all the topology from simple, standard shapes such as plates, blocks and tubes, then relying on fitting to the geometry. The models used in the geometric fitting steps below were all generated from a plate topology and the tutorial involves fitting their geometry.
#. Manually building elements by selecting/creating corner points ('nodes') in the correct order. To ease creating a valid model, tools can assist by locking on to points from a surface or point cloud segmented from real medical images.

Surface model can be automatically extruded to 3-D models (triangles become wedges, squares become cubes). Also, different, higher-order basis functions can be used over the same topology to give more degrees of freedom in the model. This is particularly important for studying the human body as it contains few straight lines. In many models of body parts we employ C\ :sub:`1`\ -continuous cubic Hermite basis (interpolation) functions to model their inherent smoothness, and the following breast fitting examples demonstrate this. There is a downside to using Hermite bases: it is painstaking work to properly connect the mesh in areas where the topology is non-trivial, such as apex points, armpits etc.

Mesh creation is a very involved topic; one needs to consider favourable alignment of elements with expected material behaviour, having sufficient density of elements to describe the problem with sufficient accuracy (the fitting examples below employ 2 different sized meshes to give some indication of the importance of mesh refinement) but overall one wishes to minimise the total number of degrees of freedom to reduce computation time. This tutorial does not go further into mesh creation at this time.

Geometric Fitting
=================

This tutorial concentrates on directly fitting generic models to data point clouds obtained from an earlier segmentation or other digitisation step. Other types of fitting not covered include:

* Fitting to modes from a Principal Component Analysis, where the variation in geometry over a population is reduced to combinations of a small number of significant mode shapes and lesser modes are discarded;
* Host-mesh fitting where the body is embedded in a coarse, smooth 'host' mesh, data is used to morph the host mesh and the embedded 'slave' mesh is moved with it.

In many cases the above methods are used as a first step to get a close approximation before direct geometric fitting.

Smoothfit Tool
--------------

Fitting the geometry of a model to a point cloud consists of 3 steps:

#. Model alignment
#. Data point projection onto the model
#. Performing the fit

This tutorial uses the 'smoothfit' MAP client plugin for interactive fitting with these 3 steps. The inputs to smoothfit in a workflow are a model file and a point cloud file (each currently limited to EX or FieldML formats that can be read by OpenCMISS-Zinc). The workflow in the MAP client is shown in :numref:`fig_dtp_cp_modcon_fitting_workflow`, and requires only the input files to be specified (and workflow step identifiers to be named):

.. _fig_dtp_cp_modcon_fitting_workflow:

.. figure:: _static/fitting-workflow.png
   :align: center
   :figwidth: 95%
   :width: 90%

   Geometric fitting workflow in the MAP client framework.

When the workflow is executed, the smoothfit interface is displayed showing the model as a semi-transparent surface and the point cloud as a cloud of small crosses. The initial view is of the '1. Align' step, shown in :numref:`fig_dtp_cp_modcon_fitting_align`.

.. _fig_dtp_cp_modcon_fitting_align:

.. figure:: _static/fitting-align.png
   :align: center
   :figwidth: 95%
   :width: 90%

   Fitting step 1: Model alignment.

In any of the views you may rotate, pan and zoom the view using the standard OpenCMISS-Zinc controls of left, middle and right mouse button drag, click 'View All' to recentre the view and click 'Done' to close the workflow step.

The first step in fitting is to scale the model and bring it into alignment with the point cloud; this is done so that the projections are as close and consistent as possible, as described below. To scale and align the model in this step, hold down the Ctrl key as you left, middle and right mouse button drag in the window: this moves the model relative to the data cloud. Be aware that rotation is a little difficult and may take practice. Other controls include alignment reset, auto centre and the Load button which will load a saved alignment. (The Save button can be disabled in the smoothfit configuration so tutorialsters don't accidentally wipe the good one that is saved for progressing to the next step!)

Often the shape of the model and point cloud make it pretty clear where to align to. Note that this tool uses manual alignment, but other tools may make it automatic (based on shape analysis) or semi-automatic (e.g. by identifying 3 or more points on the data cloud as being key points on the model, and automatically transforming to align with them).

The second step in fitting is to project the data points onto the nearest location on the aligned mesh. Switch to the '2. Project' page of the tool bar, then click on the 'Project Points' button to get the view in :numref:`fig_dtp_cp_modcon_fitting_project`. When projections have been calculated, the view changes to show error bars between the data points and their projections, coloured by magnitude, plus the on-screen display of mean and maximum error.

.. _fig_dtp_cp_modcon_fitting_project:

.. figure:: _static/fitting-project.png
   :align: center
   :figwidth: 95%
   :width: 90%

   Fitting step 2: Projecting data points onto the model.

The key point is that the projections are what the fitting aims to minimise, and if they don't agree on where a point on the mesh should move to, the fit will have problems. It's good if the projection lines are short and/or near parallel, and it's bad if they cross over each other. Often you will see some projections that are very long and probably erroneous; in fitting applications these may be eliminated or weighted lower to have less effect on the solution, but this is not offered in smoothfit yet.

Switch to the next step '3. Fit' to configure and perform the fit. This is where fitting becomes less a science and more a dark art. The normal fit adjusts the coordinates to minimise the error bars; clicking the 'Perform Fit' button performs a single iteration and it may take multiple iterations to get close to the data. :numref:`fig_dtp_cp_modcon_fitting_fit` shows what the view looks like after a couple of iterations of fitting.

.. _fig_dtp_cp_modcon_fitting_fit:

.. figure:: _static/fitting-fit.png
   :align: center
   :figwidth: 95%
   :width: 90%

   Fitting step 3: Perform the fit

Note that the projections are not recalculated during the fitting, but you can switch back to step 2, reproject and then fit again. Switching back to the Align step clears the fitted solution.

The penalty values allow you to smooth the fit by penalising particular deformations. The strain penalty limits excessive strain in the model so in the absense of data (or in the presence of noisy data) the solution errs towards one with minimal deformation from the initial aligned state. The edge discontinuity penalty is only useful for non-C\ :sub:`1`\ -continuous coordinate fields such as the linear dome example later. Penalties always increase the data point projection error (in a least squares sense, which is the solution method used in the fitting), but generally give a much more attractive result. Penalty values should be adjusted in orders of magnitude until a likeable result is obtained, then fine-tuned. It is often better to use stiffer (higher penalty) values for initial iterations to prevent waviness from developing in the mesh, then dropping for a final iteration. As for the align step, you can load and save (latter if enabled) the fitting options.

Note that smoothfit does not yet offer a curvature penalty which is one of the most powerful tools for dealing with noisy or sparse data. Using the strain penalty is the next best thing but isn't as good at dealing with excessive waviness in the solution, particularly since higher values capable of helping the waviness may considerably reduce the accuracy of the fit. This shortcoming will hopefully be rectified soon.

The following tutorial tasks each have a workflow associated with them which should be run in the usual way. 

Task 1: Coarse plate model fitted to breast data
------------------------------------------------

Open the *DTP-ModelBuilding-Task1* workflow and execute it. The breast data was obtained in 'prone' pose (hanging down) as done in MRI scans; this is also the simplest pose to digitise and fit to. Try manually aligning the surface with the breast data by Ctrl-clicking the left, middle or right mouse button and dragging to rotate, pan or scale the model. Project points and attempt to fit without any smoothing parameters. It takes several seconds to perform the fit: be patient! Try multiple fit iterations until the solution is stable. Re-project and try again.

The result without smoothing even for this example with a coarse mesh and a relatively large number of high quality data points is quite wavy, particularly around the edges. It also has some unusual depressions about the front of the breasts which is not really representative of the data cloud in general.

For a second exercise we'll use a set sequence to obtain a good fit. Switch to the Align page to reset the fit, click on 'Load' to load a good alignment, project points, switch to the Fit page and click 'Load' to load a strain penalty of 0.001. Perform the fit 2 times which gets fairly close to the data points. Next switch to the Project page and reproject points. On the fit page, lower the strain penalty to 0.0001 and fit once more. The error bars almost disappear over most of both breasts (but can't over the edges where the outlying points are - ignore these). **Write down the mean error for comparison later.** Have a look at the tips of the breast to see where the fit has not been great - this is where the limited number of elements may make the model unable to fit the data (but it is also not helped by the 'pull' of the outlier points).

Next go back to the Align page to reset the fit, then re-project points. Try a much higher strain penalty (e.g. 0.01) and see how it limits the possible deformation (after several iterations) - this is what is considered a 'stiff' model.

Try fitting with poor initial alignment to see what happens.

Task 2: Fine plate model fitted to breast data
----------------------------------------------

Open the *DTP-ModelBuilding-Task2* workflow and execute it. It has the same data point cloud as the first task, but has a mesh with more than twice as many elements and approx. twice as many parameters, so it is more able to attain a close fit with the data, but takes longer to solve.

Try some of the exercises from Task 1 with this model. With more elements the model is more susceptible to wavy solutions so applying appropriate smoothing penalties is more critical. 

When performing the second exercise from Task 1, iterate 3 times with the initial strain penalty of 0.001, then re-project points and fit with a strain penalty of 0.0001. Note down the mean error which should be about 2/3 of the value from Task 1. More importantly, zoom in on the tips of the breasts to see that the fit is much better there. (Remember the actual mean error is swayed by the outlier points around the edge of the model.)

Task 3: Coarse breast model fitted to breast data
-------------------------------------------------

Open the *DTP-ModelBuilding-Task3* workflow and execute it. In this example the initial model is more breast-like in shape so when well-aligned the amount of fitting needed is reduced. You should be able to fit it with the lower strain penalty of 0.0001 directly in 2 iterations. Since the initial model is already so close, deformations will not be as great to get a close fit.

Task 4: Fine breast model fitted to noisy data
----------------------------------------------

Open the *DTP-ModelBuilding-Task4* workflow and execute it. This example uses a fine model with a breast-like shape, however random offsets up to +/- 5mm have been added to all data points. With a large enough number of data points the effect of randomness is diminished however in small areas the randomness can introduce waviness to the solution, so smoothing penalties must be applied.

Try fitting the model without any strain penalty, and fit with several iterations to see the waviness. Reset the fit and try with the regime from task 1: 2 iters at strain penalty 0.001, re-project, 1 iter at strain penalty 0.0001. The overall result is a good fit but there is unattractive waviness on the chest area. If a curvature penalty were available, these issues with noisy data could be better controlled.

Because of the random noise the mean error will never get very low, but the average fit of the breast surface can be a reasonable 'best fit'.


Task 5: Fine breast model fitted to sparse, noisy data
------------------------------------------------------

Open the *DTP-ModelBuilding-Task5* workflow and execute it. This example uses only 10% of the data points from the previous tasks and adds +/- 3mm error to each point.

Try fitting as before. The effect of sparse data with random noise makes it even harder to obtain a close fit. Using the successful regime from Task 1 gives a result that is quite wavy after the final less-stiff fitting. Curvature penalties would greatly assist such models.

Task 6: Bilinear model fitted to point cloud
--------------------------------------------

Open the *DTP-ModelBuilding-Task6* workflow and execute it. This example has a bilinear mesh and needs no alignment with the data point cloud.

Project points and fit with all smoothing penalties set to zero. Rotate the result to see that it has developed a 'ridge' along one side, and the under-constrained corner elements distort unacceptably. Reset the fit, reproject and fit with the 'edge discontinuity penalty' set to 1. The result is much smoother. This penalty discourages solutions with differences in surface normals across edges of the mesh. Since the mesh uses bilinear interpolation, exact satisfaction of this condition cannot be met, nevertheless it minimises it as much as possible, and in particular it evens out this discontinuity since it is minimised in a 'least squares' sense.

Experiment with different (much lower, much higher) edge discontinuity penalties to see how the fit is affected. Try combining with strain penalty values.

Material Field Fitting
======================

In addition to geometry, bioengineering models often need to include spatially varying data describing the alignment of tissue microstructures, concentrations of cell types, or other differences in material properties. In heart and skeletal muscle, fibre orientations must be described over the body to orient their anisotropic material properties. Similarly, Langer's lines affect properties of the skin, and collagen orientations within other tissue can affect material behaviour.

Each of these properties can be described by spacially-varying fields which interpolate the property of interest over the same elements the coordinates are defined on.

This topic is not covered further in this example, but the concepts of creating and fitting such fields are similar to geometry: one must define the interpolation of the values over the mesh, and fit the field to data obtained from imaging or other techniques. The difference lies mainly in that the data is not coordinates, but orientations when fitting fibres, known concentrations at points when fitting points etc.

A similar process is often used to obtain solution fields from results. Often the solution technique produces outputs with high accuracy only at certain points in the model. With the Finite Element Method, for example, stress is of highest accuracy at the Gauss points, and fitting can be used to give a better idea of these solution field values away from Gauss points.
