---
title: "Understanding the analysis organization"
teaching: 20
exercises: 10
questions:
- "How can I structure my analysis code?"
- "How can I make kinematic selection on our objects?"
objectives:
- "Understand the submodules in the context of developing an analysis framework"
keypoints:
- "When you write an analysis framework, you want to split your code into different libraries"
- "You can integrate different libraries to aid in object selection."
---

# Organizing your analysis framework

We have been working with different `cxx` files during the workshop and they all show how you can modularize your analysis framework. Different physics objects have different requirement and are retrieved differently from the `xAOD` or `DAOD` dataset. As a result, many analyses will have different libraries and files to retrieve and calibrate objects.

We usually separate our analysis framework into : 
- Object retrieval and calibration
- Event selection where we apply event level requirements: i.e was the data good quality during these runs?
-  Analysis selector: we apply simple analysis selection and store variables into a `.root` file for further analysis

In our example, we have simplifed this a bit. The `JetSelectionHelper` functions helps apply kinematic requirements on the objects. `AnalysisPayload` applies the calibration and performs the selection. We will go through both of these in a bit more detail.

# Understanding the object selection

Every analysis on ATLAS makes selections based on the objects. Those are limited by object detection and calibrations. For example, if a particle has too low momentum or too high pseudorapidity, it will not detected by the ATLAS detector.

The `<object>SelectionHelper` folders are there to select basic kinematics about the objects. 


## Momentum and pseudorapidity requirements 

If you open `JetSelectionHelper.cxx` in `JetSelectionHelper/src/`, you will see two functions: `isJetGood` and `isJetBFlavor`.

The first function checks that the following requirements are satisfied:
~~~
if ( jet->pt() > 50000 && fabs(jet->eta()) < 2.5 ){
  passSelect = true;
}
~~~
{: .source}

Note: the `pt` here is in `MeV` instead of the unit `GeV` that is more commonly used. 

The next function is to help determine the flavor of the jet by applying `b-tagging`.  The b-quarks hadronize before they decay, resulting in a decay length of ~0.5mm. As a result, specialized tagging algorithms are developed to determine if a jet is a b-jet. 

You can learn more about the b-tagging algorithms in the [ b-tagging subgroup twiki](https://twiki.cern.ch/twiki/bin/view/AtlasProtected/BTaggingAlgorithmsSubgroup).

In the `isJetBFlavor` function, we first retrieve the probability of the jet being tagged as a b-jet.
~~~
btag->pb("DL1r",prob_b);
~~~
{: .source}

We then apply a selection only this probability to identify the jet as being b-tagged.
~~~
if(prob_b>0.8)
  passSelect = true;
~~~
{: .source}

The muons and electrons have similar `SelectionHelper` algorithms but those only contain requirements on the `pt` and `eta`. Feel free to take a look at the requirements applied to the electrons and the muons.

> ## Electron eta requirement
>
> Why do we veto electrron eta between 1.37 and 1.52?
>
> > ## Solution
> >
> > In the eta range of 1.37 to 1.52 the calorimeter has a transition from the barrrel and the endcap with a lot of inactive material.
> {: .solution}
{: .challenge}


# Going through the analysis payload

## Libraries included 

To be able to run through the events in our MC dataset, we need to add different includes. The first are event-level from the ATLAS event data model (EDM). Some are event level: `Init.h`, `TEvent.h`, and `EventInfo.h`. Others are based on the objects being considered: `JetContainer.h`. We also have the jet calibration libraries that were added in a previous module.
~~~
// ATLAS EDM functionality                                                                                                       
#include "xAODRootAccess/Init.h"
#include "xAODRootAccess/TEvent.h"
#include "xAODEventInfo/EventInfo.h"
#include "xAODJet/JetContainer.h"
// jet calibration                                                                                                               
#include "AsgTools/AnaToolHandle.h"
#include "JetCalibTools/IJetCalibrationTool.h"
~~~
{: .source}

We also have the libraries we had added as a submodule: `JetSelectionHelper.h`. In the next part of the lesson, we will worry about properly adding our new submodules.
~~~
// our library functionality                                                                                                     
#include "JetSelectionHelper/JetSelectionHelper.h"
~~~
{: .source}


## Running over events

We first initialize the EDM as shown in the following code snippet 

~~~
xAOD::Init();
~~~
 {: .source}
 
 To be able to read events one by event, we first loop over all the events and we need to load the event.
 ~~~
 event.getEntry( i );
 ~~~
 {: .source}
 
 Object containers need to be retrieved from the event store. We create null pointer and fill all `AntiKt4EMTopoJets` into our `JetContainer`.  
 ~~~
 const xAOD::JetContainer* jets = nullptr;
 event.retrieve(jets, "AntiKt4EMTopoJets");
~~~
 {: .source}
 
 To be able to make use of the b-tagging later, let's change the jet container we retrieve from `AntiKt4EMTopoJets` to `AntiKt4EMTopoJets_BTagging201810`. Your code should now look like this.
 ~~~code
 event.retrieve(jets, "AntiKt4EMTopoJets_BTagging201810");
~~~
 {: .source}
 
 We can then apply the kinematic requirements we defined in the `JetSelectionHelper` files.
 ~~~
 if( myJetTool.isJetGood(jet) ){
   jets_kin.push_back(*jet);
 }
~~~
{: .source}


{% include links.md %}

