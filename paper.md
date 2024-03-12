---
title: 'CoastSeg: an accessible and extendable hub for satellite-derived-shoreline (SDS) detection and mapping'
tags:
  - Python
  - shoreline
  - satellite-derived shoreline
  - coastal landcover mapping
  - coastal change detection
  - google earth engine
  - Doodleverse
  - semantic segmentation
authors:
  - name: Sharon Fitzpatrick
    orcid: 0000-0001-6513-9132
    affiliation: 1
  - name: Daniel Buscombe
    orcid: 0000-0001-6217-5584
    affiliation: 1    
  - name: Jonathan A. Warrick
    orcid: 0000-0002-0205-3814
    affiliation: 2  
  - name: Mark A. Lundine
    orcid: 0000-0002-2878-1713
    affiliation: 2  
  - name: Kilian Vos
    orcid: 0000-0002-9518-1582
    affiliation: 3      
affiliations:
 - name: Contracted to U.S. Geological Survey Pacific Coastal and Marine Science Center, Santa Cruz, California, United States.
   index: 1
 - name: U.S. Geological Survey Pacific Coastal and Marine Science Center, Santa Cruz, California, United States.
   index: 2
 - name: New South Wales Department of Planning and Environment, Sydney, Australia
   index: 3   
date: 12 March 2024
bibliography: paper.bib
---

<!-- --------------------------------------- -->
# Summary
``CoastSeg`` is an interactive browser-based program that aims to broaden the adoption of satellite-derived shoreline (SDS) detection and coastal landcover mapping workflows among coastal scientists and coastal resource management practitioners. SDS is a sub-field of coastal sciences that aims to detect and post-process a time-series of shoreline locations from publicly available satellite imagery [@turner2021satellite; @vitousek2023future]. ``CoastSeg`` is a python package installed via pip into a ``conda`` environment that serves as an API for building custom SDS workflows. ``CoastSeg`` also provides full SDS workflow implementations via jupyter notebooks and python scripts that call functions and classes in the core ``CoastSeg`` API for specific workflows. Two fully functioning SDS workflows are already provided, and more could be added by collaborators in the SDS software community. All API codes, notebooks, scripts, and documentation are hosted on the ``CoastSeg`` GitHub repository.

So-called `instantaneous' SDS workflows, where shorelines are extracted from each individual satellite image rather than temporal composites [@bishop2021mapping], follow a basic recipe, namely 1) waterline estimation, where the 2D (x,y) location of the land-sea interface is determined, and 2) water-level correction, where the waterline location is mapped onto a shore-perpendicular transect, converted to a linear distance along that transect, then corrected for water level, and referenced to a particular elevation contour on the beach [@vos2019coastsat]. The resulting measurement is called a 'shoreline', and is an elevation-based measurement. Water level corrections typically only account for tide [@vos2019coastsat] but recently SDS workflows have incorporated both wave setup and runup correction, which are a function of the instantaneous wave field at the time of image acquisition [@konstantinou2023satellite; @vitousek2023future; @vitousek2023model].

``CoastSeg`` has three broad aims. The first is to be an API consisting of core SDS workflow functionalities such as file input/output, image downloading, geospatial conversion, tidal model API handling, mapping 2D shorelines to 1D transect-based measurements, and numerous other workflows common to a basic SDS workflow, regardless of a particular waterline estimation workflow. This waterline detection algorithm will be crucial to the success of any SDS workflow because it is the step that identifies the the boundary between sea and land which serves as the basis for shoreline mapping. The idea behind the API design of ``CoastSeg`` is that users could extend or customize functionality using scripts and notebooks.

The second aim of ``CoastSeg`` is therefore to provide fully functioning SDS implementations in an accessible browser notebook format. Our principal objective to date has been to re-implement and improve upon a popular existing toolbox, ``CoastSat`` [@vos2019coastsat], allowing the user to carry out the well-established ``CoastSat`` SDS workflow with a well-supported literature [@castelle2021satellite; @castelle2022primary; @vos2023pacific; @vos2023benchmarking; @warrick2023large; @konstantinou2023satellite; @vitousek2023model; @mclean202350; @vandenhove2024secular], but in a more accessible and convenient way within the ``CoastSeg`` platform. In order to achieve this, we created and maintain ``CoastSat-package`` [@voscoastsat], a python package installed via pip into the ``CoastSeg`` ``conda`` environment that contains re-implemented versions of many of the original ``CoastSat`` codes. The ``CoastSeg`` re-implementation of the ``CoastSat`` workflow is end-to-end within a single notebook. That notebook allows the user to, among other tasks: a) define a region of interest on a webmap and upload geospatial vector format files; b) define, download and post-process satellite imagery; c) identify waterlines in that imagery using the ``CoastSat`` method [@vos2019coastsat]; d) correct those waterlines to elevation-based shorelines using tidal elevation-datum corrections provided through interaction with the pyTMD [@alley2017pytmd] API; and e) download output files in a variety of modern geospatial and other formats for subsequent analysis. 

The third and final aim of ``CoastSeg`` is to implement a method to carry out SDS workflows in experimental and collaborative contexts, which aids both oversight and reproducibility as well as practical needs based on division of labor. We do this using workflow ``sessions``, a system that enables users to iteratively experiment with different combinations of settings. ``CoastSeg`` enables fully reproducible workflows because everything is saved in a way that users can share their sessions with others, enabling peers to replicate experiments, build upon previous work, or access data downloaded by someone else. This simplifies handovers to new users from existing users, simplifies teaching of the program, and encourages collective experimentation which may result in better shoreline data.

``CoastSeg`` is also designed to be extendable, serving as a hub that hosts alternative SDS workflows and similar workflows that can be encoded in a jupyter notebook built upon the ``CoastSeg`` and ``CoastSat-package`` core functionalities. Additional notebooks can be designed to carry out shoreline extraction and coastal landcover mapping using alternative methods. We provide an example of an alternative SDS workflow based on a deep-learning based semantic segmentation model, that is briefly summarized at the end of this paper. To implement a custom waterline detection workflow, the originator of that workflow would contribute a new jupyter notebook, and also likely contribute to the ``CoastSeg`` source code to add their specific waterline detection algorithm, which could be called in their notebook. 

<!-- --------------------------------------- -->

# Statement of Need
Coastal scientists and resource managers now have access to extensive collections of satellite data spanning more than four decades. However, it's only in recent years that advancements in algorithms, machine learning, and deep learning have enabled the automation of processing this satellite imagery to accurately identify and map shorelines from imagery, a process known as Satellite-Derived Shorelines, or SDS. SDS workflows [@garcia2015evaluating; @almonacid2016evaluation] are gaining rapidly in popularity, and in particular since the publication of the open-source implementation of the ``CoastSat`` workflow [@coastsat] for instantaneous SDS in 2018 [@vos2019coastsat]. Existing open-source software for SDS often require the user to navigate between platforms (non-reproducible elements), develop custom code, and/or engage in substantial manual effort.

We sought to build a platform that not only allowed the user to adopt the ``CoastSat`` workflow in a re-implementation than within a single jupyter notebook, in a quicker, and in a more seamless manner, but also one that facilitates experimentation with the many settings that can govern shoreline accuracy, extent, and number. Further, ``CoastSeg`` has been designed specifically to host alternative SDS workflows, recognizing that it is a nascent field of coastal science, and the optimal methodologies for all coastal environments and sources of imagery are yet to be established. Therefore ``CoastSeg`` will provide a means with which to extract shorelines using multiple methods and adopt the one that most suits their needs, or implement a new methods.

We summarize the needs met by the ```CoastSeg``` project as follows:

* A re-implementation of (and improvement of) the ``CoastSat`` workflow with pip-installable APIs, and ``coastsat-package``.

* A browser-based workflow and an interactive mapping interface provided by Leafmap [@wu2021leafmap].

* A more accessible, entirely graphical and menu-based SDS workflow, with no (mandatory) exposure of source code to the user.

* A session system that streamlines the experimentation process to find the settings that extract optimal shorelines from satellite imagery.

* Improved core SDS workflow components, such as a faster and more seamless tidal correction workflow, and faster image downloading.

* Consolidation of workflows in a single platform and reusable codebase.

* An extendable hub of alternative SDS workflows in one location.

<!-- --------------------------------------- -->

# Implementation of core SDS workflow

## Architecture & Design
At a high level, ``CoastSeg`` is designed to be an accessible and extendable hub for both ``CoastSat``-based and alternate workflows, each of which is implemented in a single notebook. The user is therefore presented with a single menu of notebooks, each of which calls on a common set of core functionalities provided by ``Coastseg`` and ``Coastsat-package``, and exporting data to common file formats and conventions. 

``CoastSeg`` is installable as a pip package into a ```conda``` environment. ``CoastSeg`` notebooks are accessed from GitHub. We also created a pip package for the ``Coastsat`` workflow in order to a) improve the ``CoastSat`` method's software implementation without affecting the parent repository, and b) to install as a pip package into a ```conda``` environment, rather than duplicate code from CoastSat. 

``CoastSeg`` is built with a object-oriented architecture, where elements required by the ``CoastSat`` workflow such as Regions of Interest, reference shorelines, and transects are represented as distinct objects on the map. Each class stores data specific to that feature type as well as encompassing methods for styling the feature on the map, downloading default features, and executing various post-processing functions.

## Sessions 
SDS workflows require manipulating various settings in order to extract optimal shorelines. There are numerous settings in the ``CoastSat`` workflow, and sometimes determining optimal shorelines can be an iterative process requiring experimentation with settings. Sub-optimal shoreline extraction may result merely through user fatigue or a combination of misconfigured settings. Therefore, ``CoastSeg`` employs a `session`-based system that enables users to iteratively experiment with different combinations of settings. Each time the user makes adjustments to the settings used to extract shorelines from the imagery a new session folder is saved with the updated settings. This session system is what makes ``CoastSeg`` fully reproducible because all the settings, inputs, and outputs are stored within each session as well as a reference to what downloaded data was used to generate the extracted shorelines in the session. Moreover, the session system in ``CoastSeg`` fosters a collaborative environment. Users can share their sessions with others, enabling peers to replicate experiments, build upon previous work, or access data downloaded by someone else. This simplifies the process for new users and encourages collective experimentation and data sharing. This reproducibility and collaboration are beneficial in research contexts.

## Improvements to the ``CoastSat`` workflow

### Accessibility
``CoastSeg`` facilitates entirely browser-based workflows with an interactive webmap and ```ipywidget``` controls. It interfaces with the Zenodo API to download reference shorelines for any location in the world, organized into 5x5 degree chunks in GeoJSON format [@buscombe_2023_7786276] as well as transects, themselves providing beachface slope metadata [@buscombe_2023_8187949] available on hover. We have implemented rigorous error handling using developer log files, user report files, and informative error messages that suggest problem fixes. We have also provided a set of utility scripts for common data input/output tasks, often the result of specific requests from our software testers (see Acknowledgments). In addition to a project wiki and improved documentation, we have researched minimum, maximum, and recommended values for all settings, and have provided visual project management aids.

### Performance
``CoastSeg`` improves upon the Google Earth Engine-based image retrieval process adopted by ```CoastSat``` by offering a more reliable and efficient download mechanism. Like ``CoastSat``, we limit image sources to only the Landsat and Sentinel missions, which are publicly available to all. ``CoastSeg`` supports downloading multiple regions of interest in a single session, and ensures downloads persist even over an unstable internet connection. This is important because SDS users typically download all available imagery from an region of interest, which may amount to several hundred to thousand individual downloaded scenes. Should a download error occur, ``CoastSeg`` briefly pauses before reconnecting to Google Earth Engine, ensuring the process doesn't halt completely. In cases where image downloading fails repeatedly, the filename is logged to a report file located within the downloaded data folder. This report file tracks the status of all requested images from Google Earth Engine. ``CoastSeg``'s reliable image retrieval process enhances coastal monitoring by facilitating easier data management and collaboration. 

We added helpful workflow components such as image filtering options; for example, users can now filter their imagery based on image size and the proportion of no data pixels in an image. Additionally, the user can decide to turn off cloud masking, which is necessary when the cloud masking process fails and obscures non-cloudy regions such as bright pixels of sand beaches. Finally, we replaced non-cross-platform components of the original workflow, for example the pickle format was replaced with JSON or geoJSON formats which are both human-readable and compatible with GIS and webGIS.

![Schematic of the tidal correction workflow used by a) ``CoastSat`` and b) ``CoastSeg``.](figs/coastseg_figure_1.png){#sylt width="100%"}

### Tide
Tidal correction (Figure 1) of shorelines involves estimating the tide height for any location and time using the ``pyTMD`` API [@alley2017pytmd] to model the tide. ``pyTMD`` provides an accessible script for the widely used FES14 [@lyard2021fes2014] tidal model data access, and includes several models other than FES14 including polar-specific models. We created an automated workflow that splits the FES2014 model data into 11 global regions (an idea adopted from [@krause2021dea]). This allows the program to access only a subset of the data, facilitating fast tide estimates (in minutes rather than hours for multi-decadal satellite time-series).

![Schematic of the SDS workflows currently available in ``CoastSeg``. a) ``CoastSat`` workflow; b) ``Zoo`` workflow.](figs/coastseg_figure_2.png){#sylt width="100%"}


<!-- --------------------------------------- -->

# Implementation of an Alternative Deep-Learning-Based SDS Workflow

As we noted above, we have developed a notebook that carries out an alternative SDS workflow based on a deep-learning based semantic segmentation model. To implement this custom workflow, we created a new jupyter notebook, and added source code to the ``CoastSeg`` API. The changes ensured that the inputs and outputs were those expected by the ``CoastSeg`` core API. We call this alternative workflow the ``Zoo`` workflow, in reference to the fact that the deep learning models implemented originate from the ``Segmentation Zoo`` GitHub repository, and result from the ``Segmentation Gym`` deep-learning based image segmentation model training package [@buscombe2022reproducible]. The name `Zoo' has become a standard for online trained ML models [@modelzoo; @modelzoo2], and the repository contains both SDS models and others. Figure 2 describes in detail how the two workflows differ. The SDS workflow adopted for waterline detection will be the subject of a future manuscript.

<!-- --------------------------------------- -->

# Project Roadmap

We intend ``CoastSeg`` to be a collaborative research project and encourage contributions from the SDS community. As well as implementing alternative SDS waterline detection workflows, other improvements that could continue to be made include more (or more refined) outlier detection methods, image filtering procedures, and other basic image pre- or post-processing routines, especially image restoration on degraded imagery [@vitousek2023future]. Such additions would all be possible without major changes to the existing ``CoastSeg`` API. 

Integration of new models for the deep-learning workflow are planned, based on non-dimensionalized water index (NDWI) and modified non-dimensionalized water index (MNDWI) spectral indices, as is a new ``CoastSeg`` toolbox extension for daily 3-m Planetscope imagery [@doherty2022python] from Planet Labs [@planet]. Docker may be adopted in the future for managing dependencies in the ``conda`` virtual environment required to run the program. Other sources of imagery and other spectral indices may have value in SDS workflows, and we encourage SDS users to contribute their advances through a ``CoastSeg`` jupyter notebook implementation.

It would be also be possible to incorporate automated satellite image subpixel co-registration in ``CoastSeg`` using the AROSICS package [@scheffler2017arosics]. This would co-register all available imagery to the nearest-in-time LandSat image. Further, future work could include accounting for the contributions of runup and setup to total water level [@vitousek2023model; @vos2023benchmarking]. In practice, this would merely add/subtract a height from the instantaneous predicted tide, then apply horizontal correction. However, the specific methods uses to estimate runup or setup from the prevailing wave field would require research and software integration.


<!-- --------------------------------------- -->


# Acknowledgments
The author would like to thank Qiusheng Wu, developer of ``Leafmap``, which adds a lot of functionality to ``CoastSeg``. Thanks also to the developers and maintainers of ``pyTMD``, ``DEA-tools``, ``xarray``, and ``GDAL``, without which this project would be impossible. We acknowledge contributions from Robbi Bishop-Taylor, Evan Goldstein, Venus Ku, software testing and suggestions from Catherine Janda, Eli Lazarus, Andrea O'Neill, Ann Gibbs, Rachel Henderson, Emily Himmelstoss, Kathryn Weber, and Julia Heslin, and support from USGS Coastal Hazards and Resources Program, and USGS Merbok Supplemental. 

<!-- --------------------------------------- -->

# References


