---
title: "Integrating submodules"
teaching: 10
exercises: 30
questions:
- "How can I integrate my submodules into the analysis code?"
- "What changes to cmake will I need to do to add the new libraries?"
objectives:
- "Retrieve additional objects from the ATLAS EDM"
- "Review how to make changes to cmake"
keypoints:
- "When we add a new submodule, we need to update cmake."
- "Running over xAODs/DAODs is not that scary"
---

# Retrieving new objects in AnalysisPayload

We added two submodules to help with the electron and muon selections. Now we need to properly add them to our code so we can retrieve those objects and use them in our analysis.

## Updating libraries

We need to add the electron and muon libaries from the ATLAS EDM. We want to retrieve electron and muon containers from the event store, just like we did for jets.

In the block, `ATLAS EDM functionality`, you should add
~~~
#include "xAODEgamma/ElectronContainer.h"
#include "xAODMuon/MuonContainer.h"
~~~
{: .source}

To make use of the helper functions we added in the submodules, we also need to our new `HelperSelection` libaries under `#include "JetSelectionHelper/JetSelectionHelper.h"`
~~~
#include "ElectronSelectionHelper/ElectronSelectionHelper.h"
#include "MuonSelectionHelper/MuonSelectionHelper.h"
~~~
{: .source}

## Retrieving new objects

Just like we retrieved the jet container from the event store, we need to do the same for the electrons and the muons. 

Electrons are retrieved from the `ElectronContainer` as follows
~~~
const xAOD::ElectronContainer *electrons = nullptr;
event.retrieve(electrons, "Electrons" ) ;
~~~
{: .source}

The muons are retrieved from the `MuonContainer` as follows
~~~
const xAOD::MuonContainer *muons = nullptr;
event.retrieve(muons, "Muons") ;
~~~
{: .source}

Normally, we would want to calibrate the electrons and the muons, just as we did for the jets; however, we will not do this today.

If you are interested in how to retrieve additional objects and their calibrations, you can refer to this [xAOD analysis tutorial twikli](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/SoftwareTutorialxAODAnalysisInROOT).


## Selecting electrons 

Now that we have retrieved the electrons and muons, we want to save the electron and muon vectors that are present in our dataset as well as those that pass the pt and eta requirements defined in the `ElectronSelectionHelper` and `MuonSelectionHelper`. 

Let us first declare the `ElectronSelectionHelper` tool, similar to what was done for jets.

~~~
ElectronSelectionHelper myElectronTool;
~~~
{: .source}

We can then create vectors for the electrons for the electrons in the dataset `electrons_raw` and those that pass the requirements in `ElectronSelectionHelper`. 

~~~
std::vector<xAOD::Electron> electrons_raw;
std::vector<xAOD::Electron> electrons_kin;
~~~
{: .source}

We can loop over our electron container and fill electrons into the vectors, similar to what is done for the jets.

~~~
for(const xAOD::Electron* electron : *electrons) {
    electrons_raw.push_back(*electron);

    // perform kinematic selections and store in vector of "selected electrons"                                                
    if( myElectronTool.isElectronGood(electron) ){
        electrons_kin.push_back(*electron);
    }
}
~~~
{: .source}

In the above `for loop`, we can add a printout of electron kinematics that we will use later on. 
~~~
std::cout << "Electron : " << electron->pt() << "\t" << electron->eta() << "\t" << electron->phi() << "\t" << electron->m() << "\t" << electron->charge() << std::endl;
~~~
{: .source}

The only variable that is new in this printout for electrons, as opposed to jets, is the `charge`. 

> ## Adding muons
>
> Modify the `AnalysisPayload` program to retrieve loop over the `muons` collection and store the muons into two vectors: one for all muons, another for those that pass the `isMuonGood` selection from `MuonSelectionHelper`.
>
{: .challenge}

# Updating the CMakeLists

In the CMake module, you saw that when you added the `jet calibration` libraries, you needed to update the `CMakeLists.txt` in the `AnalysisPayload` folder, otherwise you would get a build error.

Since we have added 2 submodules and included them in  `AnalysisPayload`, we also need to add them to the `CMakeLists.txt`.

The libraries we need to link are `xAODEgamma`, `xAODMuon`, `ElectronSelectionHelperLib`, and `MuonSelectionHelperLib`.

> ## Solution
>
> Your `AnalysisPayload/CMakeLists.txt` should now contain the following.
>
> ~~~cmake
> ATLAS_ADD_EXECUTABLE ( AnalysisPayload utils/AnalysisPayload.cxx
> INCLUDE_DIRS ${ROOT_INCLUDE_DIRS}
> LINK_LIBRARIES ${ROOT_LIBRARIES}
                xAODEventInfo
                xAODRootAccess
                xAODJet
                xAODEgamma
                xAODMuon
                AsgTools
                JetCalibToolsLib
                JetSelectionHelperLib
                ElectronSelectionHelperLib
                MuonSelectionHelperLib)
> ~~~
> {: .source}
>
{: .solution}


{% include links.md %}

