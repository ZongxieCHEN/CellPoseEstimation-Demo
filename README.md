# CellPoseEstimation-Demo

This repository contains demonstration materials accompanying the master's thesis titled **"Cell Pose Estimation"**, completed at the **Technical University of Munich (TUM)** under the supervision of **Prof. Dr.-Ing. Eckehard Steinbach** and **Prof. Dr. Oliver Hayden**.

## Overview

Accurate estimation of cell pose in microscopy images is a challenging task due to the lack of annotated datasets and the complex, deformable nature of biological cells. This work introduces a proxy-based approach by estimating the 5D pose (2D position + 3D rotation) of specially designed coordinate-frame-shaped microparticles co-cultured with cells. These particles serve as visual and geometric proxies, enabling the generation of synthetic training data and supervised learning with RGB-only inputs.

The proposed system consists of a modular, three-stage pipeline:

1. **Detection and Segmentation**: Using either background subtraction or a YOLO + UNet deep learning architecture.
2. **Rotation Estimation**: A ResNet18-based regression model with a 6D rotation representation and a linearized geodesic loss.
3. **Temporal Smoothing**: Pose refinement using complementary or Kalman filters for stability across video frames.

<p align="center">
  <img src="figures/Cell_Pose_Estimation_Framework.png" alt="Cell Pose Estimation Framework" width="700"/>
</p>

## Demonstrations

This repository includes visual demonstrations of the proposed pipeline on both synthetic and real-world microscopy video sequences. These examples illustrate the system’s ability to estimate the 5D pose of coordinate-shaped microparticles, before, after fine-tuning and the effect of smoothing algorithms.

### Evaluation on Synthetic Data

#### Performance of Model with Background Subtraction Input

We first evaluate the pipeline using background subtraction as the input method. The following animation shows successful detection, segmentation, and rotation estimation on synthetic data.

<p align="center">
  <img src="gifs/syn_1_circle_Bg_Sub.gif" width="600"/>
</p>

⚪ White coordinate frame: ground truth rotation (annotation)

🔴 Red bounding box: object detection result

⚫ Blackened area inside the upper red box: segmentation result (background removed)

🔵 Blue coordinate frame: rotation estimated by the model

This example illustrates that the background subtraction pipeline can produce reliable inputs for downstream pose estimation in controlled synthetic environments.

#### Performance of Model with Deep Learning Input
Next, we evaluate the complete pipeline using YOLO for detection and UNet for segmentation.

<p align="center">
  <img src="gifs/syn_2_circle_DL.gif" width="600"/>
</p>

⚪ White coordinate frame: ground truth rotation (annotation)

🔵 Blue bounding box: YOLO detection result with confidence score

⚫ Blackened upper region inside the blue box: UNet segmentation mask

🟢 Green coordinate frame: predicted rotation

The deep learning approach produces semantically rich outputs, including object class labels and confidence scores. Despite slight visual ambiguity, the estimated pose aligns well with the ground truth, demonstrating the effectiveness of YOLO + UNet in synthetic scenarios.

### Evaluation on Real Data

The following example illustrates the performance of the rotation estimation model on real microscopy data, using deep learning-based detection and segmentation as input. The transparent glass-like structure is the real microparticle observed under the microscope, and the overlaid green coordinate frame represents the model's prediction.

### Pre-Fine-Tuning

The video below shows model predictions on real microscopy video sequences before fine-tuning.

<p align="center">
  <img src="gifs/real_002.gif" width="600"/>
</p>

The model accurately estimates the direction of the longest axis but exhibits instability in the two shorter axes. These errors result from inconsistent per-frame predictions rather than axis permutation. The network struggles to resolve fine-grained features in real data without domain adaptation.

### Post-Fine-Tuning

After fine-tuning on a small set of visually validated frames, the model achieves significantly better accuracy.

<p align="center">
  <img src="gifs/real_002_fine_tuned.gif" width="600"/>
</p>

The green rotation estimate aligns well with the ground truth across all axes, including the shorter arms. While slight jitter remains, the improvement highlights the importance of domain adaptation in real-world deployment.

### Prediction Smooth

While the fine-tuned model achieves high accuracy on real microscopy data, its frame-by-frame predictions may still exhibit minor jitter, particularly in the rotational component. This is due to the frame-independent nature of neural network inference, which lacks temporal continuity.

To improve the temporal stability of the estimated poses, we apply prediction smoothing using two independent filtering strategies: a complementary filter (CF) and a Kalman filter (KF). Each method is applied consistently to both position and rotation throughout a sequence.

The video below illustrates the effect of smoothing on a representative frame. The glass-like coordinate frame shows the ground truth, while the green frame represents the raw network prediction. The outputs after smoothing are shown in blue (CF) and red (KF).

<p align="center">
  <img src="gifs/real_002_fine_tuned_smooth.gif" width="600"/>
</p>

Both filtering methods substantially reduce prediction noise and improve visual consistency. The complementary filter (CF) provides faster response with minimal delay, while the Kalman filter (KF) exhibits slightly weaker smoothing, potentially due to suboptimal tuning of the process and measurement noise covariances. In practice, both approaches yield satisfactory results and significantly enhance the temporal stability and practical usability of pose estimates in real microscopy video sequences.

### More Demonstrations

This repository also includes predictions on 10 real microscopy video sequences (5,281 frames). All examples use the fine-tuned model, and each video displays:

- Ground truth rotation (glass-like appearance)

- Smoothed prediction with Complementary Filter (green)

- Quaternion output shown in the top-right corner

These videos provide an in-depth look at the model's practical behavior and its suitability for real-time microscopic tracking. All results can be found in the [videos/](videos) directory.

## Citation

If you find this work useful, please consider citing or referencing the associated thesis.

---

📘 Master's Thesis: *Cell Pose Estimation*  
🎓 Author: Zongxie Chen  
🧑‍🏫 Advisors: Prof. Dr.-Ing. Eckehard Steinbach, Prof. Dr. Oliver Hayden  
🏫 Institution: Technical University of Munich (TUM)
📧 Contact: [zongxie.chen@tum.de](mailto:zongxie.chen@tum.de)

## License

This repository is released under the MIT License. See [LICENSE](LICENSE) for details.
