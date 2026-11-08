Evaluating WavLM and EAT for Audio Deepfake Detection under Ultra-Short Spoofing Conditions
Summary

The development of generative audio models has increased the risk of deepfake audio, triggering significant challenges for automatic detection systems. While Self-Supervised Learning (SSL) models have proven effective for detection, almost all current experiments test relatively long spoof conditions and fail to evaluate robustness under hostile conditions with shorter spoof durations. This study examines the robustness of two SSL-based models, WavLM and EAT, under ultra-short spoof durations using a publicly accessible FakeSound train dataset. The experiment is designed under two conditions: a baseline spoof duration over one second, and a short spoof duration under 50 ms. Performance is measured using Equal Error Rate (EER) and F1 score at multiple temporal tolerances to examine localization precision. Both models show degradation in their ability to discriminate temporal localization when the spoof duration drops below 50 ms, highlighting minimal temporal spoofing as an underexplored challenge for current detection systems. EAT consistently achieves lower EER and higher temporal localization than WavLM for both conditions, demonstrating that general SSL models are more powerful for localizing spoofing segments. Although the absolute degradation of EAT from long to short is larger than that of WavLM, it underscores that a more robust model is not immune to ultra-short manipulations. These findings demonstrate that long spoof segments may overestimate real-world robustness.
Overview

Voice-based security systems utilizing Automatic Speaker Verification (ASV) are primary targets for audio spoofing and deepfake threats. While competitive benchmarks like ASVspoof and ADD have advanced anti-spoofing countermeasures, existing evaluations are conducted under controlled, idealized conditions, usually with balanced real-to-fake ratios and long spoof segments. Real-world spoofing content may diverge significantly, particularly when spoofed content is temporally minimal and injected as an extremely short segment lasting less than 50 milliseconds.

This project is a branch of the FakeSound benchmark and evaluates the robustness of WavLM and EAT across two test set conditions:

    A baseline condition with spoof duration over one second.

    A short spoof condition with a spoofing duration under 50 ms.

This repository provides the code, dataset reconstruction methodologies, and results for this evaluation.
Dataset Reconstruction

The original FakeSound dataset was not publicly released, necessitating an independent reconstruction using two parallel collection strategies:

    Filtering AudioCaps entries from clips of approximately ten seconds in duration.

    Reverse-engineering spoofed filenames from the FakeSound metadata to recover original YouTube sources, splitting full-length files into unique ten-second windows.

Synthetic audio segments were injected using AudioLDM2, a latent diffusion-based text-to-audio model. The reconstructed dataset exhibits a naturally skewed class distribution in the training set, with 502 bonafide files (24.1 percent) and 1,583 spoofed files (75.9 percent). Both test splits (long and short) were fully recovered with no missing files, preserving a near-balanced class distribution.
Dataset Split Statistics
Statistic Train Test Long Test Short
Total Metadata Entries 3,166 244 251
Files Found 2,085 244 251
Missing Files 1,081 0 0
Bonafide (Real) 502 122 125
Spoofed (Fake) 1,583 122 126
Spoof Ratio 75.9% 50.0% 50.2%
Models and Methodology

Two architecturally and domain-distinct SSL models were selected for evaluation:

    WavLM: A speech-domain SSL model built on a transformer backbone with a seven-layer CNN feature encoder. It utilizes a masked speech denoising and prediction framework.

    EAT (Efficient Audio Transformer): A general-purpose audio SSL model built on a Vision Transformer (ViT-B) backbone that operates on Mel spectrogram patches.

Both models follow a single-task detection pipeline. The pre-trained SSL backbone produces frame-level audio representations, which are aggregated into an utterance-level embedding and passed through a classification head trained end-to-end with Binary Cross-Entropy (BCE) loss.
Training Configuration
Model Learning Rate Batch Size Epochs Training Time
EAT 1e-4 64 40 ~30 min
WavLM 1e-4 64 40 >30 min
Evaluation Conditions
Test Set Description Spoof Duration
Long (baseline) Standard spoof segment > 1 second
Short Ultra-short spoof segment < 50 ms
Results

Performance is reported using Equal Error Rate (EER) as the primary metric, following ASVspoof standard conventions. F1 scores at multiple temporal tolerance thresholds are included to assess temporal localization granularity.
Combined Detection Results
Model Set Accuracy Precision Recall F1 EER
EAT Long 0.807 0.7329 0.9672 0.8339 0.1230
EAT Short 0.590 0.6139 0.4921 0.5463 0.3810
WavLM Long 0.643 0.5862 0.9754 0.7323 0.2787
WavLM Short 0.530 0.5238 0.6984 0.5986 0.4720
Threshold-Specific F1 Comparison
Model Set F1 at 1s F1 at 0.02s F1 at 0.01s
EAT Long 0.7457 0.5303 0.5290
EAT Short 0.2099 0.0240 0.0202
WavLM Long 0.5601 0.3494 0.3485
WavLM Short 0.1202 0.0087 0.0070
Effect of Class Balancing

To isolate the effect of the skewed training distribution, both models were retrained on an oversampled (balanced) training set. Results showed only marginal changes in accuracy and precision. EAT's recall under the short condition dropped from 0.4921 to 0.3651. This indicates that simple oversampling is not a sufficient remedy for ultra-short spoofing degradation, and the underlying difficulty of localizing sub-50ms manipulations persists regardless of training class balance.
Environment Setup

The project utilizes a custom Python 3.10 environment to avoid dependency conflicts. The setup script configures the necessary repositories and libraries (PyTorch, Fairseq, Librosa, etc.) using subprocesses.
bash

# Add deadsnakes PPA for Python 3.10

add-apt-repository ppa:deadsnakes/ppa -y
apt-get install -y python3.10 python3.10-distutils

# Install dependencies

curl -sS https://bootstrap.pypa.io/get-pip.py | python3.10
/usr/bin/python3.10 -m pip install --upgrade pip
/usr/bin/python3.10 -m pip install torch torchaudio torchvision
/usr/bin/python3.10 -m pip install librosa timm transformers hydra-core omegaconf sacrebleu tensorboardX datasets

To run the code:

    Clone this repository.

    Open FakeSound.ipynb in Google Colab or a Jupyter environment.

    Mount your Google Drive to access datasets if necessary.

    Execute the notebook cells to download data, run spoof injection via AudioLDM2, and train/evaluate the models.

Citation

If you use this code or findings in your research, please cite the authors:
bibtex

@article{zaini2024evaluating,
title={Evaluating WavLM and EAT for Audio Deepfake Detection under Ultra-Short Spoofing Conditions},
author={Zaini, Muhammad and Widodo, Zharif Aziz Zulkarnain and Akmal, Muhammad Fauzan and Paninggalih, Ramadhan},
journal={International Journal of Artificial Intelligence and Robotics (IIAIR)},
year={2024},
volume={1},
number={1},
pages={1--12}
}

References

    Original Benchmark: FakeSound

    Processed Dataset: Deepfake-Dataset

    Affiliation: Informatics, Kalimantan Institute of Technology, Balikpapan, Indonesia.
