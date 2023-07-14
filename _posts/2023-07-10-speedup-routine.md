---
layout: post
title:  "Boost your development routines with these six software tools"
# date:   2023-04-15 10:00:30 +0200
categories: jekyll update
---


> If you need to do something more than twice, automate it.
>
> - A. Hunt and D. Thomas, The Pragmatic Programmer

Why spend precious focus on what one can automate and execute infallibly in a matter of seconds? Not only one can gain in speed of getting things done, but also in confidence that the task is executed correctly and no typos or wrong commands are involved. In this post we go step by step through a series of practices that will help you to enhance your programming workflow and automate your repetitive tasks.

These practices are not limited to programming, infrastructure, or any other type development branch. If you interact with a computer on a daily basis, you will find something useful here. Through a series of tasks I will demonstrate applicability of these practices and showcase how they can be used. Let's get started.

## Table of Contents

- [Table of Contents](#table-of-contents)
  - [1. Use a clipboard manager ](#1-use-a-clipboard-manager-)
  - [2. Use aliases ](#2-use-aliases-)
  - [3. Use functions ](#3-use-functions-)
  - [4. Use scripts ](#4-use-scripts-)
  - [5. Use Makefiles ](#5-use-makefiles-)
  - [6. Use Taskfiles ](#6-use-taskfiles-)
  - [Summary](#summary)

Imagine. You landed a software engineering job and at the moment in front of you there is a brand-new laptop with an NVIDIA GPU on board. Our first assignment is to install NVIDIA drivers on a fresh Ubuntu installation you have just set up. After a round of googling, we have identified CUDA capability of the GPU at hand and found the latest driver version that supports it. Having it installed, we went for a coffee only to come back and find out there is a compatibility issue between the CUDA version we installed and one of our favorite software libraries (think PyTorch). We remove every bit of the drivers we have installed and redo the procedure with a different version. No luck? Repeat, rigorously studying [available compatibility docs](https://elenacliu-pytorch-cuda-driver.streamlit.app/). Throughout the process we have typed the same commands over and over again: `sudo apt-get purge "nvidia-*"`, `sudo apt install nvidia-driver-x cuda-toolkit-x`, `sudo apt-get -y install cuda`, etc.

To get the commands we went back and forth between the terminal and the browser, closing and opening the same tabs over and over again looking for the information we found once. And here comes the first practice that will help us to speed up the process.

### 1. Use a clipboard manager <a name="practice-1"></a>

A tool that saves everything you copy to the clipboard and allows you to access it later. No need to parse the installation manual again, just go through the clipboard history. I use [Clipboard indicator](https://extensions.gnome.org/extension/779/clipboard-indicator/) for GNOME, but there are plenty of alternatives for other desktop environments.

Armed with a working CUDA installation, we want to install some Python libraries to work with. `pip install numpy`, `pip install numba`, `pip install torch` is something we do with confidence. But note the repetitive `pip install` part. Can we do better than typing it again and again? Think aliases.

### 2. Use aliases <a name="practice-2"></a>

alias pi='pip install'
alias piu='pip install --upgrade'

And then we can use `pi seaborn`, `piu matplotlib`, `piu lightning`, etc. Looks more efficient, right? Think about those commands that come up often in your workflow and create aliases for them. For example, it could be:

```bash
alias pd='python -m ipdb' # start a debugger
alias k9='kill -9' # kill a process
alias cn='code -n' # open a directory in VS Code
alias via='vim ~/.alias' # edit aliases
alias sa='source ~/.alias' # reload aliases
alias wns='watch -n 1 nvidia-smi' # watch GPU usage
alias rmr='rm -rf' # remove a directory recursively
alias rs='rsync -avP' # sync files between directories
```

Aliases are a great way to save time on typing simple commands that don't require many arguments. Clipboard manager saves you time in a short run, while a suite of aliases will serve you for long.

I suggest to keep aliases in a separate file, e.g. `~/.bash_aliases` and source it from `~/.bashrc`: `source ~/.bash_aliases`

---

Aliases, though quick to create, are inflexible: arguments must correspond to the aliased command, no dynamic behavior is possible, and extra logic is limited to a single line. More complicated than aliases, functions can encapsulate far greater command complexity, empowered by all the features of the Bash language.

### 3. Use functions <a name="practice-3"></a>

Now we need to connect to a remote server residing in the cloud.

```bash
function gssh() {
    gcloud compute ssh "$1" --ssh-key-file $SSH_KEY_NODE_A --zone "europe-west3-a" --project "gcp-project-048"
}
```

Put this in `~/.functions` and run `source ~/.functions`. Called as `gssh node-a` this function will connect to the `node-a` instance in the GCP project using the SSH key stored in the `$SSH_KEY_NODE_A` environment variable. No need to remember the key path or the project name. Have the name of an instance (think about picking it up automatically in the function) and you are good to go.

Here is another use case.

```bash
function ksh() {
    shell="${2:-/bin/bash}"
    kubectl exec --stdin --tty $1 -- $shell
}
```

The function above will open a shell in a pod with a given name. If no shell is specified, it will default to `/bin/bash`. `ksh pod-name` will open a bash shell in the pod, while `ksh pod-name /bin/sh` will open a shell of a different type.

Functions are more powerful than aliases, but they are also more involved to write. The rule of thumb is to use aliases for simple commands and functions for those that require some dynamic behavior.

However, functions are not the ultimate means as automation gets more complicated. Usually, at this stage we are working with a concrete project and already have enough components to manipulate. In these cases, we can create a set of dedicated scripts that know with which components to work and how to do it.

### 4. Use scripts <a name="practice-4"></a>

Scripts allow implementing reusable automation logic for an entire application. They can be written in any language, but most popular choices are Bash and Python. In which cases to prefer Bash? If you are interacting with the system, e.g. installing packages, copying files, etc. Python gets more handy when you need to implement some logic, e.g. parse a JSON file, make a decision based on the result, etc. The concrete application you work with also makes a difference. If you are working with Kubernetes that has an awesome CLI SDK, you will most likely use Bash, while for AWS with its boto3 APIs you are most likely stick to Python.

Let's have a look at the following script that I used to collect the results of a benchmarking experiment.

```bash
#!/bin/bash

# check if the global variable 'run_number' is defined
if [ -z "$run_number" ]
then
      echo "\$run_number is not defined"
      exit 1
fi


# define common variables first to avoid duplication
export base_dir="$( dirname -- "$0"; )/results"

# step 1. fetch the results stored on one of the compute nodes
export MEASURE_NODE_NAME=$(kubectl get nodes -o wide | grep 'measure-a' | awk '{print $1}')
gcloud compute scp --recurse user@"$MEASURE_NODE_NAME":"~/result_$run_number.txt" $base_dir/perf_$run_number.txt --zone europe-west3-a

# step 2.1. fetch all events with errors of specific type from the Kubernetes cluster
kubectl get events --all-namespaces | grep -i "(OOMKilled)|(BackoffLimitExceeded)" > $base_dir/job_errors_in_exp_$run_number.txt
# step 2.2. fetch pod stats 
kubectl get pods -o json > $base_dir/pods_$run_number.json

# step 3. parse some of the results
python3 get_exec_time.py $base_dir/pods_$run_number.json

# step 4. visually inspect structure of the output directory
tree $base_dir/
```

This script file is called "collect_results.sh" and it is used after an experiment is finished to automatically assemble results of the experiment. A time-consuming repetitive procedure out of several steps is now reduced to a single command `bash ./collect_results.sh`.

---

Note the 'run_number' variable that is used to distinguish between different runs of the same experiment. It is defined externally, and we need to keep track of it to ensure reproducibility of the results. But what about managing several of such scripts each containing bits of logic that depend on dynamic variables? How to ensure that these scripts are executed in the right order, with the right arguments, and correct environment variables?

Apart from encapsulating repetitive tasks in aliases, functions, and scripts, an important consideration is to execute the commands **consistently**. We need to ensure the same arguments, environment variables, working directory, etc. This is where Makefiles can come in handy.

### 5. Use Makefiles <a name="practice-5"></a>

Say, we develop a machine learning project where we train a model, evaluate its performance, and deploy it if the evaluation metrics are good enough. We can implement these steps as Makefile targets:

```makefile

model_save_path:=model.pt
train_args:=--epochs 10 --batch-size 32 --lr 0.001 --save-path $(model_save_path)
metric_args:=--metrics accuracy --threshold 0.9
eval_args:=$(metric_args) --model-path $(model_save_path)
deploy_args:=--model-path $(model_save_path)

# the first target is called 'train' 
train:
    python train.py $(train_args)

eval:
    python eval.py $(eval_args)

check_metrics:
    python check_metrics.py $(metric_args)

# the 'deploy' target depends on the success (zero exit-code) of 'check_metrics'
deploy: check_metrics
    python deploy.py $(deploy_args)

run-pipeline: train eval deploy

# in general, target denotes a file that needs to be created. For example, 'build' target will execute the command only if the file or directory called 'build' does not exist. To change this behavior, we can use the '.PHONY' directive and specify targets that should be treated as commands.
# targets declared after '.PHONY:' are treated as commands, not files
.PHONY: run-pipeline train eval check_metrics deploy
```

To run the full pipeline, we can execute `make run-pipeline`, or we can run individual steps also changing their arguments, e.g., `make train model_save_path="model_v2.pt"`.

Makefiles are a great way to manage projects with multiple independent components. A single Makefile could be a control point for the entire pipeline and allow to run the steps consistently, also promoting flexibility and ease of use. It is the tool of choice for the majority of the workloads one does locally. However, when things get more complex than running a bunch of commands, the syntax of Makefiles is not the most intuitive, and it is not the best choice for elaborate pipelining logic. In these cases, we can use Taskfiles.

### 6. Use Taskfiles <a name="practice-6"></a>

Consider a scenario where we have a backend and a frontend of a web application. We routinely build Docker images for both and want to automate this process. We can use a Makefile for it:

```makefile

include .devenv

GIT_COMMIT_ID:=$(shell git log -n 1 --format=%h)

build-backend:
    @echo "Build docker image for backend"
    docker build -t backend maven/target --build-arg GIT_COMMIT_ID=$(GIT_COMMIT_ID)
build-frontend:
    @echo "Build docker image for frontend"
    docker build -t frontend maven/target --build-arg GIT_COMMIT_ID=$(GIT_COMMIT_ID)
```

Note that we have to duplicate logic for the same action - building a Docker image, reminding us of what is happening via `echo` statements.
Now let's take a look at the Taskfile alternative:

```yaml

version: '3'

dotenv: [".devenv"]

tasks:
  docker:
    cmds:
      - docker build -t {{.IMAGE_NAME}} {{.BUILD_CONTEXT}} --build-arg GIT_COMMIT_ID={{.GIT_COMMIT_ID}}
    vars:
        GIT_COMMIT_ID:
            sh: git log -n 1 --format=%h

  build-backend:
    desc: Build docker image for backend
    cmds:
      - task: docker
        vars: {
          IMAGE_NAME: "backend",
          BUILD_CONTEXT: "maven/target"
        }
  build-frontend:
    desc: Build docker image for frontend
    cmds:
      - task: docker
        vars: {
          IMAGE_NAME: "frontend",
          BUILD_CONTEXT: "maven/target"
        }

  default:
    desc: Default task runs all docker builds
    cmds:
      - task: build-backend
      - task: build-frontend
```

And `task --list` will show us what the Taskfile can do:

```bash
task: Available tasks for this project:
* build-backend:        Build docker image for backend
* build-frontend:       Build docker image for frontend
* default:              Default task runs all docker builds 
```

Though more verbose, the Taskfile is more readable and feature-rich. It allows defining variables within tasks, pass them to other tasks, put dependencies between tasks, and more.

Key Taskfile features are concisely illustrated in [this article](https://www.sobyte.net/post/2022-04/taskfile/).
And a comprehensive overview of Makefiles vs Taskfiles you can find [here](https://tsh.io/blog/taskfile-or-gnu-make-for-automation/).

### Summary

In this article, we have discussed the tools that can help us automate our daily work. We have started with a clipboard manager, which is a great way to save time on typing repetitive commands in short-term. Then we have discussed aliases, functions, and scripts, which allow encapsulating complex logic to speed up programming routines throughout a project. Finally, we have looked at Makefiles and Taskfiles, which are the tools of choice for managing projects with multiple independent components.

While being a great way to save time, automation is also a great way to make our work more consistent and less error-prone. It is a good practice to automate everything that can be automated which is "done more than twice". However, this aspect of development shouldn't be overdone, taking the time from the actual project work. By practicing, you will find the right balance between the two. Happy coding!
