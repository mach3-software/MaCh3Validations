# Welcome to MaCh3 Tutorial
After this tutorial you should know how to run MCMC, implement systematic uncertainties and how to plot standard Bayesian diagnostic.

[![Code - Documented](https://img.shields.io/badge/Code-Documented-2ea44f)](https://github.com/mach3-software/MaCh3/wiki)
[![Container Image](https://img.shields.io/badge/Container-Image-brightgreen)](https://github.com/mach3-software/MaCh3Tutorial/pkgs/container/mach3tutorial)
[![Code - Doxygen](https://img.shields.io/badge/Code-Doxygen-2ea44f)](https://mach3-software.github.io/MaCh3/index.html)

## How to Start?
To compile simply
```
mkdir build;
cd build;
cmake ../
make -jN [set number of threads]
make install
```
then
```
source bin/setup.MaCh3.sh
source bin/setup.MaCh3Tutorial.sh
```
alternatively you can use containers by
```
docker pull ghcr.io/mach3-software/mach3tutorial:alma9latest
```
To reed more how to use containers check our wiki [here](https://github.com/mach3-software/MaCh3/wiki/12.-Containers)

## How to run LLH scan
To run an LLH scan simply do
```
./bin/MCMCTutorial Inputs/ManagerTest.yaml
```
These are very useful for validations and to compare with other fitters

## How to run MCMC
To run MCMC simply
```
./bin/MCMCTutorial Inputs/ManagerTest.yaml
```
Congratulations! 🎉
You have just completed finished you first MCMC chain.

## How to Plot?

There are a number of apps included to make plots from the results of your fits, llh scans etc. You can find more details on them and how they work in the main MaCh3 wiki [here](https://github.com/mach3-software/MaCh3/wiki). There you will also find some instructions on how you can write yor own plotting scripts. 

The plotting library is configured using yaml files. You can see some examples of such config files in the plotting directory, and a detailed explanation of them is given in [the wiki](https://github.com/mach3-software/MaCh3/wiki).

Some examples on how to make some "standard" plots are given below.

### MCMC Chain
You can produce simple plots with
```
./bin/ProcessMCMC bin/TutorialDiagConfig.yaml Test.root
```
where Test.root is the output of running MCMCTutorial as described [here](#how-to-run-mcmc)

You can then take the output of running ProcessMCMC which will be called something like <inputName>_Process.root, and make fancier error plots from it using the `GetPostfitParamPlots` app like 

```
GetPostfitParamPlots Test_Process.root
```

### LLH Scans

You can plot the results of an LLH scan using the aptly named PlotLLH app like so

```
PlotLLH LLH_Test.root
```

where LLH_Test.root is the result of running the LLH scan as described [here](#how-to-run-llh-scan).

### Plotting with Python

If you have installed the python interface for MaCh3 as described [here](https://github.com/mach3-software/MaCh3?tab=readme-ov-file#python) then you can also use the provided python plotting module

## How to Setup your analysis
In the next step you gonna modify analysis setup and repeat steps.
First let's better understand `Inputs/SystematicsTest.yaml`. This config controls what systematic uncertainties will be used in the analysis for example like this:
```
- Systematic:
    Names:
      FancyName: Norm_Param_0
    Error: 0.11
    FlatPrior: false
    IsFixed: false
    Mode: [ 0 ]
    ParameterBounds: [0, 999.]
    ParameterGroup: Xsec
    TargetNuclei: [12, 16]
    KinematicCuts:
      - TrueQ2: [0.25, 0.5]
    ParameterValues:
      Generated: 1.
      PreFitValue: 1.
    DetID: 985
    StepScale:
      MCMC: 0.2
    Type: Norm
```
If you want to read more about implementation please go [here](https://github.com/mach3-software/MaCh3/wiki/02.-Implementation-of-Systematic)

As first step let's modify `Error: 0.11` to `Error: 2.0`, this should significantly modify error which shou.d be noticeable in MCMC.

Lastly we need to modify name of output file. This is governed by manager class (read more [here](https://github.com/mach3-software/MaCh3/wiki/01.-Manager-and-config-handling)) modify `OutputFile: "Test.root"` in `Inputs/ManagerTest.yaml` to for example
`OutputFile: "Test_Modified.root"`.

Now let's run MCMC again
```
./bin/MCMCTutorial Inputs/ManagerTest.yaml
```
Congratulations! 🎉
Next step is to compare both chains.

Warning: If you modified files in main folder not build you will have to call make install!
## How to Plot Comparisons?
Now that you have two chains you can try comparing them using following.
```
./bin/ProcessMCMC bin/TutorialDiagConfig.yaml Test.root Default_Chain Test_Modified.root Modified_Chain
```
This will produce pdf file with overlayed posteriors. Most should be similarly except modified parameter.


## MCMC Diagnostic
Crucial part of MCMC is diagnostic whether chain converged or not. You can read more on [here](https://github.com/mach3-software/MaCh3/wiki/11.-Step-size-tuning)

```
./bin/DiagMCMC Test.root bin/TutorialDiagConfig.yaml
```

## More advanced development????
