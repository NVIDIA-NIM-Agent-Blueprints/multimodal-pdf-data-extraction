<h2><img align="center" src="https://github.com/user-attachments/assets/cbe0d62f-c856-4e0b-b3ee-6184b7c4d96f">NIM Agent Blueprint: Multimodal PDF Data Extraction</h2>
<p align="center">
  
![Multimodal PDF Data Extraction for Enterprise RAG-r2](https://github.com/user-attachments/assets/3f33a00b-0d72-4221-a250-04771cb703cc)

</p>

Rapidly ingest massive volumes of PDF documents. Extract text, graphs, charts, and tables for highly accurate retrieval.
<hr>

## Introduction

This blueprint is based on [NVIDIA-Ingest](https://github.com/NVIDIA/nv-ingest) -- a scalable, performance-oriented document content and metadata extraction microservice. It includes support for parsing PDFs, Word and PowerPoint documents, using specialized NVIDIA NIM microservices to find, contextualize, and extract text, tables, charts and images for use in downstream generative applications.

NVIDIA Ingest enables parallel document splitting to rapidly extract data from many documents at the same time.

## Prerequisites

#### Hardware

| GPU | Family | Memory | # of GPUs (min.) |
| ------ | ------ | ------ | ------ |
| H100 | SXM/NVLink or PCIe | 80GB | 2 |
| A100 | SXM/NVLink or PCIe | 80GB | 2 |

## Get Started

1. Apply for [Early Access](https://developer.nvidia.com/nemo-microservices).
2. Once you have the Early Access, login to NGC and download the `Enterprise RAG - Docker workflow` Resoruce and then Follow the below instructions 

    * Install [Docker Engine and Docker Compose](https://docs.docker.com/engine/install/ubuntu/).

    * Verify NVIDIA GPU driver version 535 or later is installed.

        ```
        $ nvidia-smi --query-gpu=driver_version --format=csv,noheader
        550.90.07

        $ nvidia-smi -q -d compute

        ==============NVSMI LOG==============

        Timestamp                                 : Thus Oct 11 21:17:25 2024
        Driver Version                            : 550.90.07
        CUDA Version                              : 12.4

        Attached GPUs                             : 2
        GPU 00000000:CA:00.0
            Compute Mode                          : Default
        GPU 00000000:CC:00.0
            Compute Mode                          : Default
        ```

        Refer to the [NVIDIA Linux driver installation instructions](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) for more information.

    * Install the [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html).

        Verify the toolkit is installed and configured as the default container runtime.

            
            $ cat /etc/docker/daemon.json
            {
                "default-runtime": "nvidia",
            "runtimes": {
                    "nvidia": {
                        "path": "/usr/bin/nvidia-container-runtime",
                        "runtimeArgs": []
                    }
                }
            }

            $ sudo docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi -L
            GPU 0: NVIDIA A100 80GB PCIe (UUID: GPU-d8ce95c1-12f7-3174-6395-e573163a2ace)
            GPU 1: NVIDIA A100 80GB PCIe (UUID: GPU-49902a43-6199-5249-02c6-19515fc0cc56)
            

    * Create an NGC account and API Key. Refer to the [instructions](https://docs.nvidia.com/ngc/gpu-cloud/ngc-overview/index.html) to create an account and generate an NGC API key.
    
        The key is used to download the containers for the model containers for the models mentioned above, which are required by the NV-Ingest microservice.

            Export the NGC_API_KEY

                ```
                export NGC_API_KEY=<ngc-api-key>
                ```

            Log in to the NVIDIA container registry using the following command:

                ```
                docker login nvcr.io
                ```

    * Run the pipeline 

        `NOTE:` The example requires atleast 4xA100 GPUs to deploy all the required models locally. If you are using a AxA100 system, we need ensure that the NIM LLM microservice runs on a dedicated GPU, Follow the steps below for the same

            
            cd enterprise-rag-docker_v2x.x.x/
            
        a. Uncomment the device_ids key in docker-compose-nim-ms.yaml for nemollm-inference and comment the count key.
            
            services:
                nemollm-inference:
                deploy:
                    resources:
                    reservations:
                        devices:
                        - driver: nvidia
                            # count: ${INFERENCE_GPU_COUNT:-all}
                            device_ids: ['${LLM_MS_GPU_ID:-0}']
                            capabilities: [gpu]

        b. And then set the following environment variable.
                
            export LLM_MS_GPU_ID=3
            
        c. Run the below commands to launch the pipeline

            cd rag-app-multimodal-chatbot-nvingest/
        
            USERID=$(id -u) docker compose --profile local-nim --profile milvus up -d
        
    * Check status of the containers.

        ```
        docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"

        CONTAINER ID   NAMES                                                        STATUS
        3c826374c81e   rag-playground                                               Up 5 hours
        bd49b820e141   rag-application-multimodal-chatbot-nvingest                  Up 5 hours
        33e0dff2dcab   milvus-standalone                                            Up 5 hours
        1cc2502644db   rag-app-multimodal-chatbot-nvingest-cached-1                 Up 5 hours
        674352dbc275   rag-app-multimodal-chatbot-nvingest-yolox-1                  Up 5 hours
        4ccfa1c34489   nemo-retriever-embedding-microservice                        Up 5 hours (healthy)
        c78de130d7cc   rag-app-multimodal-chatbot-nvingest-paddle-1                 Up 5 hours
        1d554a3b3ce1   rag-app-multimodal-chatbot-nvingest-deplot-1                 Up 5 hours
        2bfbb9d78151   nemollm-inference-microservice                               Up 5 hours (healthy)
        b77e00089678   rag-app-multimodal-chatbot-nvingest-redis-1                  Up 5 hours
        d3d590291ccd   rag-app-multimodal-chatbot-nvingest-nv-ingest-ms-runtime-1   Up 5 hours
        17f0f0fedc47   milvus-minio                                                 Up 5 hours (healthy)
        8663f4f9faeb   milvus-etcd                                                  Up 5 hours (healthy)
        ```

    * Open your browser and interact with the RAG Playground at http://localhost:3001.

### Next Steps

Refer to the [Notebooks](./notebooks) to evalute the  MultiModel RAG with LangChain