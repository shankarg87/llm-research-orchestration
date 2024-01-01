# Orchestration for LLM Research by AI Hero

Code used to orchestrate the fine-tune and serving services on top of Kubernetes. 

## Infrastructure Supported
Currently we are only supporting Coreweave.
- `Coreweave` - We assume that you have downloaded the `kubeconfig` file and can see your pods using `kubectl get pods`.
  
## Setup
```sh
pip install -r requirements.txt
pip install -r k8s/requirements.txt
pip install -r poc/requirements.txt
```

## Config for the k8s job
Update [k8s/yamls/config.yaml](k8s/yamls/config.yaml).

## Launching a Fine-Tuning Job
```sh
cd k8s/
python train.py launch rparundekar/fine_tune_research:20231209_01 mmlu_peft.yaml
```
You'll see the name of the job. And instructions to see the logs and delete the job.
NOTE: The container is created in the fine-tuning project.

If launching with a distributed config
```sh
cd k8s/
python train.py launch rparundekar/fine_tune_research:20231209_01 distributed_default.yaml -d fsdp_single_worker.yaml
```

### Deleting a Fine-Tuning Job
Use the same program to delete the job so that all resources are deleted.
```sh
python train.py delete <job-name>
```

## Serving a Model
We'll use the Huggingface TGI library to serve a model. The program will read details of the output model from the training config and serve in Kubernetes.
Then, you can port forward locally and run the PoC
```sh
cd k8s/
python serve.py launch mmlu_peft.yaml
```

### Running the PoC
The PoC assumes that the model server is running at `http://127.0.0.1:8080/`.

```sh
streamlit run poc.py
```

## Code QA
```sh
python3.9 -m venv venv
source venv/bin/activate
python3 -m pip install --upgrade pip
python3 -m pip install -r requirements.txt
pre-commit install
pre-commit autoupdate
pre-commit run --all-files
```