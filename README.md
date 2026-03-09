# Finding Similar Items: arXiv Dataset Similarity Detector
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Silvia-Guidi/Similarity-project/blob/main/Similairty_project.ipynb)

## Project Overview
This project implements a high-performance pipeline to detect pairs of similar scientific abstracts within the arXiv Dataset. Given the massive scale of the corpus (approximately 2.97 million papers), the system utilizes MinHash and Locality Sensitive Hashing (LSH) to transform a computationally prohibitive $O(n^2)$ problem into a manageable $O(n)$ task.

The implementation focuses on the semantic content of research abstracts, using Jaccard Similarity as the primary metric for document likeness.

## Key Features
- Scalable Architecture: Capable of processing the full arXiv archive in under 2 hours on basic hardware.
- High-Performance Core: Utilizes Numba (@njit) for hardware-native execution speeds and multiprocessing to leverage available CPU cores.
- Memory Efficiency: Employs NumPy memory mapping and aggressive garbage collection to handle 3M signatures without exhausting RAM.
- Optimized Pre-processing: Features a custom normalization pipeline and character-level 9-shingling.

## Methodology 
1. Pre-processing & Shingling
   - Cleaning: Abstracts are normalized by removing punctuation and converting to lowercase using optimized translation tables.
   - Shingling: Documents are converted into sets of k-shingles ($k=9$), hashed into 32-bit integers via MurmurHash3.
2. MinHash & LSH
   - Signature Matrix: Each document is compressed into a signature of 100 integers.
   - LSH Bucketing: The signature matrix is divided into 20 bands of 5 rows ($b=20, r=5$).
   - Threshold: This configuration creates a sharp "S-curve" inflection point at approximately 0.55 similarity, ensuring high recall for relevant papers while maintaining precision
  
## Experimental Results
The system was evaluated using a scalability benchmark (10k to 100k docs) and a full-scale run on 2.97 million documents
| Metric | 10k Docs | 25K Docs | 50K Docs | 100k Docs | 2.97M Docs (Full) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **MinHash Time** |19.20s | 44.12s  | 84.51s  |166.31s (~2.5 mins)  |5213.50s (~1.5 hrs)  |
| **LSH Time** |0.74s | 3.17s |4.0s| 10.88s| 272.88s (~5 mins) |
| **Candidate Pairs** | 60 | 352 |1 151 | 3 274 | 113 982 |
| **Empirical Precision** |68.33% | 67.00% | 64.00%  | 65.00%  | 63.00%|
