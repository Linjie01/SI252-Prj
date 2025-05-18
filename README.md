
# Vista- FedPeft



<div style="text-align: center;">
    <img src="docs/vista_logo1.png" width="150" >
</div>
The code repo for the project "pretrain model selection in FL"

## Requirements
- python >= 3.9
- transformers == 4.47.1
- peft == 0.14.0
- torch == 2.2.1

## Getting Started

```bash
git clone https://github.com/Linjie01/VistaFL.git
cd VistaFL
git checkout dev
git submodule add https://github.com/Linjie01/Hermit.git hermit-repo
cd hermit-repo
git checkout vista_dev
```

## Introduction to Key Components
### PipelineV2
This class enables you to add different "Task" to the pipeline, such as "client local training", "global_aggreation", and "selection". These tasks have two modes: "parallel" and "single". For "parallel" tasks, you can customize selection tasks for workers to execute distributed computation tasks. We use a data structure called "FLContext" to manage all necessary information and variables in the FL pipeline. It may be refreshed in each round, but you can keep some attributes via "keep_by_role()" or "keep()".

### Important Components in Pipeline
1. **NodeScheduler**: Manages the process of running (simulating) each worker. Supports automatic task execution arrangement and GPU memory consumption control.
2. **EventManager**: Contains eventloop and job monitor. Each task dispatched to a specific worker is viewed as a job with a unique ID. The eventloop is responsible for task function registration and emission. The job monitor is responsible for state monitoring of job execution states in one parallel task group.
3. **event_pipeline**: A linked list recording all action steps for executing the experiment pipeline, including actions not defined by users. Some functions not relying on distributed execution are also directly recorded in event_pipeline.
4. **ExpRecorder**: Records experiment results from "FLContext" (not yet completed).

### Communication Mechanism Between Components
The communication between the pipeline and nodescheduler relies on TaskProtocal-based task messages. There is an extra communication level between the nodescheduler and its managed processes (for worker running).

## Running the Pipeline
1. You need a JSON file for whole system variable configuration. Refer to `vista/naive_fedpeft_test_config.json`.
2. Define the pipeline runner function for specific task execution. Refer to `vista/infra/fedpeft_exp_driver.py` for the default pipeline runner function.
3. You can implement specific classes of workers (clients and aggregators), as well as their data structures.

## Running FedPEFT Instructions
Our implementation of FedNLP part refers to two repos:
- [GitHub - UbiquitousLearning/FedAdapter](https://github.com/UbiquitousLearning/FedAdapter)
- [GitHub - FedML-AI/FedNLP](https://github.com/FedML-AI/FedNLP)

We mainly utilized their data split implementation. Based on their data split scheme, we further implemented Huggingface peft package-based FedPEFT workflow.

### What You Need for Running FedPEFT
1. Download dataset files via:
   ```bash
   hermit-repo/hermit/lang/data_tools/data_downloader/download_data.sh
   hermit-repo/hermit/lang/data_tools/data_downloader/download_partition.sh
   ```
2. Download pretrained models from Huggingface and add them to the appointed dir:
   ```bash
   hermit-repo/hermit/lang/model_loader/model_hub/base_model
   ```
