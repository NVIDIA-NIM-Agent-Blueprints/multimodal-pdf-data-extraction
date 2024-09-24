<h2><img align="center" src="https://github.com/user-attachments/assets/cbe0d62f-c856-4e0b-b3ee-6184b7c4d96f">NIM Agent Blueprint: Multimodal PDF Data Extraction</h2>
<p align="center">
  
![Multimodal PDF Data Extraction for Enterprise RAG-r2](https://github.com/user-attachments/assets/3f33a00b-0d72-4221-a250-04771cb703cc)

</p>

Rapidly ingest massive volumes of PDF documents. Extract text, graphs, charts, and tables for highly accurate retrieval.
<hr>

## Introduction

This blueprint is based on [NVIDIA-Ingest](https://github.com/NVIDIA/nv-ingest) -- a scalable, performance-oriented document content and metadata extraction microservice. It includes support for parsing PDFs, Word and PowerPoint documents, using specialized NVIDIA NIM microservices to find, contextualize, and extract text, tables, charts and images for use in downstream generative applications.

NVIDIA Ingest enables parallel document splitting to rapidly extract data from many documents at the same time.

## Get Started

1. Apply for [Early Access](https://developer.nvidia.com/nemo-microservices).
2. Follow the getting started documentation [here](https://github.com/NVIDIA/nv-ingest).

**NOTE** -- the downloadable blueprint deploys the document ingestion pipeline. It does not include a retrieval pipeline.
