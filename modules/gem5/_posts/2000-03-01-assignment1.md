---
Author: Jason Lowe-Power
Editor:  Maryam Babaie, Mahyar Samani
Title: ECS 201A Assignment 1
---

# Assignment 1 -- (100 Points)

Originally from University of Wisconsin-Madison CS/ECE 752 .

Modified for ECS 201A, Winter 2023.

**Due on *{{ site.data.course.dates.gem5_1 }}* 11:59 pm (PST)**: See [Submission](#submission) for details

## Table of Contents

- [Administrivia](#adminstrivia)
- [Introduction](#introduction)
- [Workload](#workload)
- [Experimental setup](#experimental-setup)
- [Analysis and simulation](#analysis-and-simulation)
- [Submission](#submission)
- [Grading](#grading)
- [Academic misconduct reminder](#academic-misconduct-reminder)
- [Hints](#hints)

## Adminstrivia

You should submit your report in **pairs** and in **PDF** format. Make sure to start early and post any questions you might have on Piazza. The standard late assignemt policy applies.

Use [classroom: assignment 1](https://classroom.github.com/a/j_DKuKaP) to create an assignment. You will be aksed to **join**/**create** an assignment. If your teammate has already created an assignment, please **join** their assignment instead of creating one assignment otherwise **create** your assignment and ask your teammate to **join** the assignment.

## Introduction

In this assignment you are going to:

- measure the performance differences of a single-cycle like processor vs an in-order pipelined processor,
- see how the measured performance scales as CPU clock frequency changes,
- and see the effect of memory bandwidth and latency on measured performance.

You are going to use a matrix multiplication program as the workload for your experiments. Matrix multiplication is a commonly used kernel in many domains such as linear algebra, machine learning, and fluid dynamics.

## Workload

For this assignment we are going to use a matrix multiplication program as our workload. The program takes and integer as input that determines the `size` of the square matrices `A`, `B`, and `C`.

```cpp
void multiply(double **A, double **B, double **C, int size)
{
    for (int i = 0; i < size; i++) {
        for (int k = 0; k < size; k++) {
            for (int j = 0; j < size; j++) {
                C[i][j] += A[i][k] * B[k][j];
            }
        }
    }
}
```

You can find the definitions for the workload objects in gem5 under `workloads/workloads.py`.
In this assignment, we will only be using `MatMulWorkload`.
In order to create an object of `MatMulWorkload` you just need to pass matrix size (an integer) `mat_size` to its constructor (`__init__`) function.
**In your configuration choose an appropriate value for `mat_size`**. It should be large enough that it makes your workload interesting.
Since changing `mat_size` will influence simulation time, as a guideline, choose a value that results in simulation times less than 10 minutes (hostSeconds < 600).
We found that setting mat_size to 224 will result in a simulation time of around 5 minutes which is a reasonable compromise.

**CAVEAT [PLEASE READ CAREFULLY]**: When using this workload with gem5, your simulation will output two sets of statistics in the same `stats.txt` file. Each set of statistics start with a line like below.

```text
---------- Begin Simulation Statistics ----------
```

Please make sure to **ignore** the **second** set of generated statistics in your analysis.

## Experimental setup

For this assignment, we will set up an experiment to see effect of changing a system's component on it performance. You will need to write configuration scripts using gem5 stdlib that allow you to change the CPU model, CPU and cache frequency, and memory model.
Under the `components` directory, you will find modules that define the different models that you should use in your configuration scritps.

- Board models: You can find all the models you need to use for your CPU (processor) under `components/boards.py`. You will only be using `HW1RISCVBoard` in this assignment.
- CPU models: You can find all the models you need to use for your CPU (processor) under `components/processors.py`.
- Cache models: You can find all the models you need to use for your cache hierarchy under `components/cache_hierarchies.py`. You will only use `HW1MESITwoLevelCache` in this assignment.
- Memory models: You can find all the models you need to use for your memory under `components/memories.py`.

## Analysis and simulation

Complete the following steps and answer the questions for your report. Collect data from your simulation runs and use simulator statistics to answer the questions. Use clear reasoning and visualization to drive your conclusions. You are allowed to submit your reports in **pairs** and in **PDF** format.

Before starting with simulations, answer the following questions in your report.

1. What metrics should you use to measure the performance of a computer system? Why?
2. Why is it not always possible to use the same metrics for performance to evaluate computer systems?

### Step I: Changing the CPU model and CPU and cache clock frequency

Before running any simulations try to answer these questions.

1. At the same clock frequency, between a single-cycle CPU (`HW1TimingSimpleCPU`) and an in-order pipelined CPU (`HW1MinorCPU`) which CPU will exhibit better performance? Why?
2. Between a single-cycle CPU (`HW1TimingSimpleCPU`) and an in-order pipelined CPU (`HW1MinorCPU`) CPU which one is going to be more sensitive to changing the clock frequency? Why?

In your configuration script allow for:

- changing the CPU model between `HW1TimingSimpleCPU` and `HW1MinorCPU`
- and changing the clock frequency between `1GHz`, `2GHz`, and `4GHz`

Use `HW1DDR3_1600_8x8` as the memory model.

In your report, answer the same questions after simulation supported with data. A complete set of simualtion data for this step should include **6 configurations** (2 options for CPU model * 3 options for clock frequency).

### Step II: Changing the CPU and memory model

Before running any simulations try to answer these questions:

1. If you double the double the performance of memory (double the bandwidth and halfen the latency) in a computer system, will the overall perforamance double as well? Why?
2. Which CPU model (between `HW1TimingSimpleCPU` and `HW1MinorCPU`) will benefit more from improving memory performance? Why?

In your configuration allow for:

- changing the CPU model between `HW1TimingSimpleCPU` and `HW1MinorCPU`
- and changine the memory model between `HW1DDR3_1600_8x8`, `HW1DDR3_2133_8x8`, and `HW1LPDDR3_1600_1x32`.

Use `4GHz` as the clock frequency.

**NOTE**: To become familiar with the different memory models you will use in this assignment, please read through the documentation for the different memory models in `components/memories.py`.

In your report, answer the same questions after simulation supported with data. A complete set of simualtion data for this step should include **6 configurations** (2 options for CPU model * 3 options for memory model).

### Step III: General questions

Now that you have completed your simulation runs and analyses. Answer this last question in your report.

1. If you were to use a different application, do you think your conclusions would change? Why?

## Submission

Your submission is split into two parts. Read the following sections for details on each part.

### Part I: Reproducibility package

As part of your submission, you should include any script/code/file that might be needed to rerun your gem5 experiments. This may include configuration scripts that define set up the simulation, python/shell/etc. scripts that drive your simulations using your configuration scripts, any document including instruction on how to run your simulations. You should do this through your assignment's repository. Make sure to commit and push your changes to your local repository to your remote. Add clear and relevant commit messages to your commits. **NOTE**: Any commits/pushes past the assignment deadline will be ignored.

### Part II: Report

As mentioned before, you are allowed to submit your assignments in **pairs** and in **PDF** format.
You should submit your report on [gradescope](https://www.gradescope.com/courses/487868).
In your report answer the questions presented in [Analysis and simulation](#analysis-and-simulation), [Analysis and simulation: Step I](#step-i-changing-the-cpu-model-and-cpu-and-cache-clock-frequency), [Analysis and simulation: Step II](#step-ii-changing-the-cpu-and-memory-model),and [Analysis and simulation: Step III](#step-iii-general-questions). Use clear reasoning and visualization to drive your conclusions.

## Grading

Like your submission, your grade is split into two parts.

1. Reproducibility Package (50 points):
    a. Instruction and automation to run simulations for different section and dump statistics (20 points)
        - Instructions (10 points)
        - Automation (10 points)
    b. Configuration scripts and correct simulation setup (30 points): 2.5 points for each configuration as described in [Analysis and simulation: Step I](#step-i-changing-the-cpu-model-and-cpu-and-cache-clock-frequency) and [Analysis and simulation: Step II](#step-ii-changing-the-cpu-and-memory-model)
2. Report (50 points): 7 points for each question presented in [Analysis and simulation](#analysis-and-simulation), [Analysis and simulation: Step I](#step-i-changing-the-cpu-model-and-cpu-and-cache-clock-frequency), [Analysis and simulation: Step II](#step-ii-changing-the-cpu-and-memory-model),and [Analysis and simulation: Step III](#step-iii-general-questions).

## Academic misconduct reminder

You are required to work on this assignment in teams. You are only allowed to share you scripts and code with your teammate(s). You may discuss high level concepts with others in the class but all the work must be completed by your team and your team only.

Remember, DO NOT POST YOUR CODE PUBLICLY ON GITHUB! Any code found on GitHub that is not the base template you are given will be reported to SJA. If you want to sidestep this problem entirely, don’t create a public fork and instead create a private repository to store your work.

## Hints

- Start early and ask questions on Piazza and in discussion.
- If you need help, come to office hours for the TA, or post your questions on Piazza.
