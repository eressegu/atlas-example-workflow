---
title: "Optional: Using condor"
teaching: 10
exercises: 20
questions:
- "How can I run this analysis on condor?"
objectives:
- "Use HTCondor to run an analysis so we could run on multiple files"
keypoints:
- "HTCondor is really cool"
---

# Setting up our condor job

Let's first create an additional folder, `batch`, at the same level as `source`, `run`, and `build`. This will help keep our other directories clearer. In `batch`, we will save our scripts to run on HTCondor and we'll also submit our jobs from there.

In order to set up a HTCondor job, we first need to create an executable for the commands we have run in order to run our analysis.

Let's call this script, `runAnalysisPayload.sh`. The script should look like this.
~~~bash
#!/bin/bash
export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase;source ${ATLAS_LOCAL_ROOT_BASE}/user/atlasLocalSetup.sh
cd /home/<path to your folder>/batch
asetup AnalysisBase,21.2.186
source ../build/x86_64-centos7-gcc8-opt/setup.sh
AnalysisPayload {1}
~~~
{: .source}

We have written all the commands we use for the setup. We then call the executable `AnalysisPayload`.

Instead of specifying the file, we use a variable `{1}`. This will allow us to run over multiple files.

Now, let's write our submit job called `ap.sub`.
~~~bash
Universe   = vanilla
Executable            = runAnalysisPayload.sh
Output                = log/ap.$(ClusterId).$(ProcId).out
Error                 = log/ap.$(ClusterId).$(ProcId).err
Log                   = log/ap.$(ClusterId).log
should_transfer_files   = IF_NEEDED
when_to_transfer_output = ON_EXIT

queue arguments from files.txt
~~~

We call our executable we have created above. The files will be read in from a text file.

Don't forget to create a `log` folder inside `batch`.

Now, we need to create one more file, the text file that will contain our files we want to run over.

Your `files.txt` file should look like this:
~~~
/work/eresseguie/bootcamp/DAOD_EXOT27.24604725._000001.pool.root.1
~~~

Now, you can submit your jobs.
~~~code
condor_submit ap.sub
~~~

If there are no errors, you will see `myOutputFile.root` appear in your `batch` folder.
