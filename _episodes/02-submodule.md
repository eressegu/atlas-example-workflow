---
title: "Adding object kinematic selection modules"
teaching: 5
exercises: 15
questions:
- "How do I prepare a workspace and add two new modules?"
objectives:
- "Review concepts learned in previous lessons: `git`, `cmake`, `make`."
keypoints:
- "We can add more than one submodule"
---

# Changing the data path

Since the `inputFilePath` in `source/util/AnalysisPayload.cxx` is out of date, let's first update that since we will need to run our analyis during the module.

The new data file is:
~~~
/work/eresseguie/bootcamp/DAOD_EXOT27.24604725._000001.pool.root.1
~~~

# Preparing your repository and compiling existing code

Before making modifications, it is always good to compile the exisiting directory to make sure there are no errors.

Inside the folder you have cloned, you will need to first create two folders: one for the compiled binaries (`build`), one for the analysis results (`run`).

Let's create our two folders:
~~~shell
mkdir build/
mkdir run/
~~~

You should then build and compile your code. 

> ## Solution
>
> Here is the sequence of commands to build and compile your code.
>
> ~~~shell
> cmake -Ssource -Bbuild
> cmake --build build
> source build/x86_64-centos7-gcc8-opt/setup.sh
> ~~~
> {: .source}
>
{: .solution}


You can also run your code now if you would like before moving to the next step.

# Adding two new submodules

The MC process we are studying does not only have b-jets, it also has leptons. For leptons, we'll only consider electrons and muons.

For the jet selection, we added the git submodule `JetSelectionHelper` in a previous module. We will now add two new submodules for the electron and muon selections: [`ElectronSelectionHelper`](https://gitlab.cern.ch/usatlas-computing-bootcamp-2021/ElectronSelectionHelper) and the [`MuonSelectionHelper`](https://gitlab.cern.ch/usatlas-computing-bootcamp-2021/MuonSelectionHelper). 

We want to add both of these subomdules to the source folder.

> ## Solution
>
> The commands to add the submodules are the following.
>
> ~~~shell
> git submodule add https://gitlab.cern.ch/usatlas-computing-bootcamp-2021/MuonSelectionHelper.git source/MuonSelectionHelper
> git submodule add https://gitlab.cern.ch/usatlas-computing-bootcamp-2021/ElectronSelectionHelper.git source/ElectronSelectionHelper
> ~~~
> {: .source}
>
{: .solution}

Your source folder should look like this:

~~~
AnalysisPayload  CMakeLists.txt  ElectronSelectionHelper  JetSelectionHelper  MuonSelectionHelper
~~~
{: .output}


{% include links.md %}

