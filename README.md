# LLM deployment with Minikube

A Kubernetes deployment project for running a local LLM service using minikube with Open WebUI frontend and vLLM backend.

## Overview

This project provides Kubernetes configuration files to deploy:
- A vLLM server running TinyLlama-1.1B-Chat model
- Open WebUI as a user-friendly interface to interact with the model
- Necessary services and volume configurations

## Components

### Backend
- **vLLM Server**: Serves AI model inference via OpenAI-compatible API
- **Model**: TinyLlama-1.1B-Chat-v1.0
- **Image**: vllm/vllm-openai:latest

### Frontend
- **Open WebUI**: Modern, feature-rich interface for LLM interaction
- **Image**: ghcr.io/open-webui/open-webui:latest

## Configuration Files

- `vllm_deploy.yaml`: Deployment configuration for vLLM server
- `vllm-service.yaml`: ClusterIP service to expose vLLM internally
- `openw_deploy.yaml`: Deployment configuration for Open WebUI
- `openw_service.yaml`: NodePort service to expose Open WebUI externally
- `.gitignore`: Ignores sensitive files (pv-models.yaml, secret.yaml)

## Prerequisites

- Kubernetes cluster (local or remote)
- Persistent Volume for model storage or mount with the path of the host models cache.
- HuggingFace token (stored as a Kubernetes secret)

## Setup

1. Create the required secret for HuggingFace token:

First convert the huggingface token to base64 format because Kubernetes requires that the values in a Secret's data field be base64 encoded. This provides a consistent way to store binary data that might contain special characters or non-printable characters within YAML files, which are text-based.

```bash
echo -n "hf_token" | base64
```

Them create a yaml file to configure the HuggingFace token as a secret
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: huggingface-token
type: Opaque
data:
  token: base64_codified_hf_token
```

And finally create the secret in kubernetes

```bash
kubectl apply -f secret.yaml
```


2. Create a persistent volume claim named 'tinyllama' (you'll need to create a corresponding PV configuration)

3. Deploy all resources:
```bash
kubectl apply -f vllm_deploy.yaml
kubectl apply -f vllm-service.yaml
kubectl apply -f openw_deploy.yaml
kubectl apply -f openw_service.yaml
```
or

```
kubectl apply -f .
```
If all of the files are in the same Folder 
## Accessing the application

Once deployed, if you are using minikube you can access Open WebUI using the command:

```bash
minikube service openwebui-service --url
```
Or if you are using a VM you can acess:

- URL: http://[NODE_IP]:30000
- Where NODE_IP is the IP of any node in your Kubernetes cluster

## Customization

- To use a different model, modify the `--model` argument in `vllm_deploy.yaml`
- Adjust GPU resource allocation with the `--num-gpu-blocks-override` parameter

## Security Notes

- This deployment uses external secrets not included in the repository
- Make sure to protect your HuggingFace token
- Consider network policies for production environments

## License

[Specify license information here]