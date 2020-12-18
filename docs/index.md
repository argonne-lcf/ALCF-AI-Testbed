# Argonne AI-testbed System Documentation

The purpose of this technical documentation is to provide information on using the Argonne AI-testbed System.

Please contact placeholder_email@anl.gov for questions or feedback.

## [Cerebras (CS-1)](cerebras)
CS-1 is a wafer-scale, deep learning accelerator. Processing, memory, and communication in CS-1 reside in the Cerebras Wafer-Scale Engine (WSE), a 462 square-cm silicon wafer with approximately 400,000 processor cores. Each core has 48 KB of dedicated SRAM memory (for a total of 18GB on-chip), and all cores are connected to one another over a high bandwidth, low latency, two-dimensional interconnect mesh. The software platform integrates popular machine learning frameworks like Tensorflow and PyTorch.


## [SambaNova](sambanova)
SambaNova systems aims to develop and accelerate AI applications at scale with a Reconfigurable Dataflow ArchitectureTM (RDA). At the core of this system is a Reconfigurable Dataflow UnitTM (RDU) which is a next-generation processor that provides dataflow processing and acceleration. The software stack, SambaFlowTM, extracts, optimizes and maps dataflow graphs to RDUs from standard machine learning frameworks such as PyTorch and Tensorflow. SambaNova Systems DatascaleTM is a rack-level accelerated system that includes DataScale nodes with integrated networking. The system deployed at Argonne AI testbed is a half-rack RDA system consisting of two nodes, each with eight RDUs. The RDUs on a node are interconnected via a proprietary interconnect to enable both model parallelism as well as data parallelism. Each node consists two  sockets with 128 cores and 1 TB of memory and are interconnected using an Infiniband-based fabric.

## [Graphcore](https://docs.graphcore.ai/en/latest/){:target="_blank"}
Colossus GC2 Intelligent Processing Unit (IPU) was designed to  provide state-of-the-art performance  for training  and inference  workloads.  It consists of 1216 IPU-Tiles each with an independent core and tightly  coupled  memory.   The  Dell  DSS8440,  the  first  Graphcore  IPUserver, features 8 dual-IPU C2 PCIe cards, all connected with IPU-Link™technology in an industry standard 4U server for AI training and inference workloads.  The server has two sockets, each with twenty cores, and 768GB of memory.

## Groq (Available in 2021)
Groq tensor streaming processor (TSP) provides a scalable and  programmable processing core and memory building block to achieve 250 TFlops in FP16 and  1 PetaOp/s in INT8 performance. The Groq accelerators are PCIe gen4-based and multiple accelerators one a node can be interconnect via a proprietary chip-to-chip interconnect to enable larger models and data parallelism.