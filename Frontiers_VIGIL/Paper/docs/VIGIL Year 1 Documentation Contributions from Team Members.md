# Instructions

# VIGIL Year 1 Documentation Contributions from Team Members

**ACTION: Each of you will need to provide documentation about your components of the VIGIL system by the end of Sunday 11/23.  This deadline is firm.  Mike and I need to compile everything into a common set of documents for submission by 12/1; it is no easy task.**  

**What needs to be done.**  
In the google doc here, duplicate the Template Document Tab  and rename it with your name and component (e.g., “Jason Medical VQA”, and flesh it out (with text, results tables, figures, images, etc) that address various documentation needs.  Do so along each point below.  Noting that all of this is [described in the plan doc.](https://docs.google.com/document/d/17GyIhSzf8Rw6QdEByRMFlvr5Um5_d3Ud/edit)

We need to provide documentation around the following TRL points

- TRL 3.2: Documents indicate that system components ought to work together  
- TRL 3.3: Performance metrics for system components are established  
- TRL 3.4: Risk Areas identified in general terms  
- TRL 3.5: Risk mitigation strategies identified  
- TRL 3.6: Inspection of Test Data  
- TRL 3.8: Data collection and processing conducted to support component

Some nuances

- Some of this asks for data. So, we will need to collect it and provide it to MITLL.  Not all of the data, but some of it.    
- Some of the documentation might already be in one of the working docs.  IT IS OK TO POINT (HYPERLINK) to that main location.  But, ensure it fully addresses the need there.  
- If you did experiments to justify parameters choice, model size, UX, other things, DEFINITELY INCLUDE THESE.   

For MITLL, our team needs to address these needs individually according to each of the following CTE (core technology element; read: component). 

- CTE 1 Guidance Engine  
  - VIGIL Agent  
  - Medical VQA  
  - Reporter  
  - Patient Alignment  
- CTE 2 User Sensing (User receiving the guidance)  
  - Cardiac LVEF  
  - Lower-Limb DVT  
  - Speech Recognition and Transcription  
- CTE 3 Environment Sensing (includes patient)  
  - Pose Estimation (Patient)  
  - 3D Object Tracking (Probe)  
  - 3D Environment  
- CTE 4 User Interface  
  - Frontend (Displays/Projection)  
  - Backend (Data Flow)  
  - Audio


# Template

# NAME / COMPONENT

Flesh each subsection out (with text, results tables, figures, images, etc) to address various documentation needs.

# **TRL 3.2: Documents indicate that system components ought to work together**

# **TRL 3.3: Performance metrics for system components are established**

# 

# **TRL 3.4: Risk Areas identified in general terms**

# 

# **TRL 3.5: Risk mitigation strategies identified**

# 

# **TRL 3.6: Inspection of Test Data**

# 

# **TRL 3.8: Data collection and processing conducted to support component**

# 

# BBN / TTS

# BBN / TTS

# **TRL 3.2: Documents indicate that system components ought to work together**

The Text-to-Speech (TTS) system is a ROS2 process that converts text input into synthetic speech audio. It uses the Python-based **Piper** library, selected for its easy Python integration, fast audio generation, and high-quality voice options. For testing and demonstrations, the **en-US-kusal-medium** voice configuration was used.

The TTS pipeline receives text strings from the University of Michigan’s Visual Question Answering (VQA) system. Each incoming text string is placed into a queue for speech generation, which is handled by a dedicated thread. Once Piper produces the corresponding audio bytes, they are transferred to a second queue managed by an audio playback thread. This thread plays the synthesized speech and then safely discards the processed audio. The dual-thread design enables the system to generate new speech while simultaneously playing previously generated audio, improving overall responsiveness.

A variety of configuration options are available through the system’s .ini file. Users can adjust the selected synthetic voice, playback volume, and speech rate to suit different applications.

# **TRL 3.3: Performance metrics for system components are established**

There are two primary performance metrics for the TTS system. The first is speech accuracy, which requires the generated audio to perfectly match the provided text. This was evaluated by manually supplying text during testing and verifying that the spoken output matched the expected content. Throughout both the controlled tests and the live demonstrations, the TTS system consistently produced perfect speech accuracy, no errors or deviations were observed.

The second metric is total execution time, which measures how long it takes for the system to receive a text string, generate the corresponding audio, and complete playback. In practice, the system performed well: for typical sentence-length inputs from the VQA system, the total execution time averaged roughly 0.5 seconds, providing fast and natural interaction.

# **TRL 3.4/3.5: Risk Areas identified in general terms and Risk mitigation strategies identified**

# 

| Risk | Mitigation |
| ----- | ----- |
| **Queue Backlog** \- High frequency text inputs could overload the audio-generation and playback queues, causing delayed replies and seemingly mismatched responses | Queue sizes in the TTS node and the Medical Agent are capped at 10\. The Medical Agent filters user queries before they are sent to the VQA system (reducing the number of overall queries) |
| **Dependency on the VQA Node** \- If the VQA system isn’t running or is non-functioning the TTS node will not receive text and output audio. TTS will remain idle. | TTS node remains stable while idle and will resume immediately when VQA becomes available. |
| **Missing Voice Files** \- the synthetic voice configuration files are not stored with the rest of the system files in GitHub and must be manually downloaded. Incorrect placement of the files or missing files prevents the TTS node from accessing voice weights, making speech generation impossible. | Clear installation instructions provided in the README System checks for missing voice files and logs warnings if files are missing. Logs also provide directions for fixing the problem.  |

# 

# **TRL 3.6: Inspection of Test Data**

N/A

# **TRL 3.8: Data collection and processing conducted to support component**

N/A


# BBN / ROS Network Infrastructure

# BBN / ROS Network Infrastructure

# **TRL 3.2: Documents indicate that system components ought to work together**

VIGIL components originate from a variety of sources, some of which have been under independent development for some time prior to VIGIL, while others were developed exclusively for VIGIL. The Robot Operating System (ROS) provides a robust and flexible communications infrastructure that enables all these components to function together as a single, coherent system. General information on ROS can be found on their website, [https://ros.org/](https://ros.org/). VIGIL currently uses the stable long term support distro, ROS Humble.

ROS system components in VIGIL communicate mostly via publish-subscribe mechanisms, with some use of request-response for command and control operations. Each component is represented by a ROS node. Nodes contain publishers and subscribers for topics they produce or consume, respectively, and also contain services and clients to facilitate the request-response exchanges supported by the VIGIL component.

The ROS middleware automatically registers each node so that it becomes part of the ROS graph, enabling the node to send and receive messages with all other registered nodes. The nodes in the ROS graph of the VIGIL system are presented in the [Vigil Systems – ROS2 Internode Messaging](https://drive.google.com/file/d/1TS1Pp6Bl96i0D9CV3Mw5RR_2pxGrOqs0/view?usp=drive_link) diagram. Connectors in this diagram indicate the flow of published data at the source node where the connector originates, to the destination nodes where the connector points.

Any software providing message delivery services will have some amount of latency from the time that data becomes available to send and the arrival time of that data at its destinations; ROS is no exception to this rule, but online research suggested latencies for uses cases like VIGIL’s would be on the order of no more than 3-5 milliseconds, and that optimizations like using shared memory transport could likely lower that delay to 10s of microseconds. At this time, there has not yet been a compelling reason to prioritize optimization of overall ROS delivery latency over and above its default settings, but we expect to experiment with shared memory transport layers in the future to capture what extra performance may be possible and to determine if there may be unidentified issues that would prevent its use if the extra efficiency was to become a requirement rather than simply being nice to have.

While no concerns with absolute latency were identified, there were concerns with the variances between the times of arrival of the data for different sensors in the subscribers of VIGIL components which naively use the most recently received data from each device as “aligned” data. If not handled, these variances can cause issues when the analysis requires the data from multiple devices to be as perfectly synchronized as possible, e.g., as when using cameras at different view points to locate objects in 3-dimensional space. As of the PARADIGM Year 1 demonstration and evaluation events, no VIGIL components are yet using data from multiple sensors in a way that requires such accurate temporal alignment, but as this is known to be a future requirement, a solution has been developed, which will be described in the performance metrics section below.

# **TRL 3.3: Performance metrics for system components are established**

# In the simplest terms, the key performance metric for the VIGIL ROS network is the usability of the user interfaces – they need to be responsive to changes in the environment and system inputs in a timely and smooth manner. Designed for real-time control of robotic devices, ROS is an ideal tool for achieving the best possible performance for message delivery under different conditions.

# Smooth updates will not be possible unless the communications infrastructure delivers all required messages without excessive queuing or dropping of messages. Many VIGIL messages carry sequence numbers, which enables the detection of dropped messages; sequences are not currently checked, but when sensor data is recorded, the values are observable in recorded data.

# Timely updates are greatly influenced not only by the communications infrastructure, but by the latencies introduced by the components that consume and analyze sensor data or its derivatives; these latencies are beyond the control of the ROS infrastructure, but its quality of service parameters can be tuned to help with issues caused by analysis algorithms which fall behind the rate of incoming data.

# VIGIL currently uses mostly default ROS quality of service settings for all messaging; some topics are configured for shorter queue depths to ensure processing utilizes the most recent data available for the trade-off of dropping some data completely. Specific settings of note:

* # “reliable” delivery; message delivery is retried on failures.

* # “keep last” history; retains most recent messages if delivery queues overflow.

* # “depth”; typically configured for queue length maximum of 10 on both publish and subscribe queues, but if algorithms are routinely falling behind, subscriber queue lengths can be decreased so stale data is discarded instead of queued.

# As mentioned in the previous section, some VIGIL analysis routines can experience issues if they do not have temporally-aligned data from multiple sensor sources. To better understand the variances occurring in the arrival times of sensor data, we recorded a ROS bag of VIGIL sensor data that included three cameras: a GoPro HERO 12 connected via Wi-Fi 6, a GoPro HERO 12 connected via USB 3.2 Gen 2, and an Intel RealSense D435 also connected via USB 3.2 Gen 2\. These cameras were all arranged so that a tablet computer displaying a timer accurate to 1/100 second was within their field of view. The GoPros produced images at 30 fps, while the RealSense produced at 15 fps. The data stream was collected for approximately 80 minutes. We played back the ROS bag file while running viewer apps subscribed to each of the three camera topics, and by pausing the playback could see what time was displayed in the most recent image from each camera, and compiled those times into a spreadsheet for further analysis. As the below graph shows, though the difference in latency between cameras is not large in human-observable terms, with a maximum difference of less than half a second, there is no consistent trend over time, and no doubt the actual differences between pairs of cameras was greatly affected by processor scheduling of the publish-subscribe mechanisms.

# **![][image19]**

# The table below shows the minimum, maximum, average and standard deviation, in seconds, of the absolute value of the time deltas between the pairs of cameras:

|   | *Wired \- Wireless* | *RealSense \- Wireless* | *RealSense \- Wired* |
| ----: | :---: | :---: | :---: |
| ***Min*** | 00:00:00.02 | 00:00:00.05 | 00:00:00.03 |
| ***Max*** | 00:00:00.25 | 00:00:00.44 | 00:00:00.27 |
| ***Avg*** | 00:00:00.11 | 00:00:00.25 | 00:00:00.14 |
| ***Std Dev*** | 00:00:00.07 | 00:00:00.11 | 00:00:00.07 |

# 

# These values indicate that, if being observed naively by any algorithm or analysis routine, data from different cameras could be aligned with image frames which could be nearly a half-second different in the “real world” time at which they really occurred. To address this issue, we have implemented a small library to perform temporal alignment of any number of data streams, using the time of arrival of the original sensor data from before when it is first published, this being the most accurate time in a common frame of reference available in the VIGIL system.

# **TRL 3.4/3.5: Risk Areas identified in general terms and Risk mitigation strategies identified**

# The primary risk for VIGIL with regards to the ROS network is if it was unable to process the volume of message content that needs to move between all the producer and consumer nodes in the VIGIL system. There are several ways to mitigate a potential lack of overall bandwidth in the ROS network:

* # ROS quality of service settings can be applied to improve performance or increase reliability.

* # ROS supports multiple different vendors of the Data Distribution Service (DDS) middleware used for data transfer.

* # DDS can use different transport mechanisms, such as shared memory, which in a single-system hardware configuration like that of VIGIL, could replace the network-based default implementation for faster communication.

* # Changes to the amount of data transmitted, or improving the efficiency and reuse of transmitted data could also help to mitigate if there are demonstrated bottlenecks in data flow. E.g., decrease the frame rates of sensors, or publish image overlay data instead of repeated image data with overlays applied.

# Another risk with a system like VIGIL is that it can be extremely difficult to test system correctness and performance in a repeatable fashion. ROS, as a system for the development of robot systems, which have similar constraints and characteristics in responding to a highly-dynamic environment, provides high-quality tools to address this with the “rosbag” data recording and playback system. Using this capability of ROS, it is possible to replicate with as high a precision as possible, the sequence of messages generated during a recorded session.

# 

# **TRL 3.6: Inspection of Test Data**

# See the ***Sensors: TRL 3.6*** section for a detailed explanation.

# **TRL 3.8: Data collection and processing conducted to support component**

N/A

# 

# Cornell / 3D PROBE TRACKING

# Cornell University / 3D Probe Tracker

# **TRL 3.2: Documents indicate that system components ought to work together**

**Overview.** The 3D probe tracking system predicts, for each frame of video recorded by the overhead camera, the 3D position of the tip of the ultrasound probe. The system uses only an RGB stream of video; it does not directly use the depth maps from the RGB-D camera. We show this in the figure below, which shows the 3D prediction of the probe (green), the bounding box (red) and the image crop that was used for point regression (yellow).

![][image20]

The 3D probe tracking system is based on several submodules. Each one makes use of open source computer vision and machine learning modules, which are finetuned. These are used to perform the following sequence of steps, described below:

**System components:** 

1. **2D object detection.** First, we detect the 2D bounding boxes of the ultrasound probe, thereby allowing us to attend to a smaller region of the image more intently, avoid processing visually similar objects, and take advantage of existing pretraining. To do this, we finetune the YOLO object detector ([https://arxiv.org/abs/1506.02640](https://arxiv.org/abs/1506.02640)). For each input frame, the model regresses the bounding box of the ultrasound probe. Since the input frame is very likely to contain an ultrasound probe, we set the detection threshold lower, such that the model is likely to retrieve at least one bounding box. This avoids a situation where the model returns no detections, e.g., due to an out-of-distribution scene yielding detections with lower probability than those the model was trained on.  
     
   To train this component of the system, we hand-label video frames from recordings (shared by the CSU team) of trial lower limb demo experiments (see below)

2. **Probe 3D point estimator**. Given an input frame and a 2D bounding box detection, we crop the image (centered on the box) and feed the resulting image to a second neural network that regresses the depth of the probe tip. We perform this crop by extracting a subimage of size 3-7x the width and height of the original bounding box (varied randomly during training), which we resize to be a 224x224 image. The first step of detection and cropping is important since it provides a higher resolution input and allows us to separate the detection and point regression tasks. We finetune a vision transformer (ViT) model pretrained with weights from DINOv2 ([https://arxiv.org/abs/2304.07193](https://arxiv.org/abs/2304.07193)). The model regresses the coordinates of the probe via a linear layer that takes a learned query token as input. The model optimizes the following loss:  
   ![][image21]  
     
   where (x\_gt, y\_gt) is the ground truth pixel location of the probe tip and (x\_pred, y\_pred) is the position predicted by the model, and Z\_gt and Z\_pred are the ground truth and predicted depth (in meters) respectively.   
     
   When training, we freeze the DINOv2 backbone and create a learnable query token. Cross-attention is applied between this query and the 196 spatial patch tokens (14×14 grid, excluding \[CLS\] token) from DINOv2's output. The attended feature of query token is then passed through an MLP to regress the position. We normalize the input to be in the range \[0, 1\] for each dimension, to approximately equalize the contribution of each coordinate.  
     
   This module is trained using hand-labeled 2D probe pixel locations plus pseudo ground-truth depth from a monocular depth estimator, DepthPro (see below).

# **TRL 3.3: Performance metrics for system components are established**

We will evaluate the effectiveness of the model using the following metrics, using videos from held out probe recordings:

* **Average precision (AP) of the object detector:** Following standard practice in object detection evaluation, we determine if a predicted bounding box is correct using intersection over union (IoU) threshold of 0.5. For this, we use the ground truth label.  
* **2D probe tip error:** we will use the mean squared error (MSE) of the 2D pixel location of the probe, using the ground truth label. Note that the probe tip is not always visible. We therefore only evaluate the accuracy using only frames in which it is visible, and rely on 2D bounding box prediction accuracy as a proxy for these cases.   
* **Probe depth error:** Following common practice in monocular depth estimation, we will measure the depth error in log space, to model the size of typical errors. Specifically, we will measure Z\_err \= |log(Z\_pred) \- log(Z\_gt)|, where Z\_gt is the pseudo ground-truth depth and Z\_pred is the prediction by the model.

# **TRL 3.4: Risk Areas identified in general terms**

# There are three major sources of possible errors:

* **Generalization across different scenes and subjects.** Our approach involves finetuning recognition systems on visual data (e.g., rather than using an off-the-shelf pretrained system). As a result, the model may struggle to generalize if the training data significantly differs from the test data, such as when different subjects or CDP scenes (e.g., due to lighting, presence of different object instances, camera and body poses, etc.).  
* **Lack of representative disoccluded training data.** Since the probe tip location is not directly observed in the training data, the model only receives labels from images where it is available. This may introduce a bias in the 3D point predictor  
* **Depth label accuracy.** The depth labels come from a monocular depth estimator that may introduce errors.

# **TRL 3.5: Risk mitigation strategies identified**

We provide the following risk mitigation strategies for the risks above.

* **Generalization across different scenes and subjects.** We will mitigate this by using pretrained features that have been trained on large amounts of data, and training the model on a variety of different CDP scenes and test subjects.  
* **Lack of representative disoccluded training data**. If we observe that this happens, we will mitigate it by adding extra data augmentation to simulate occlusion, such as adding synthetic occluders to the scene.  
* **Depth label accuracy.** We will compare the predictions of our model to those produced by other systems, such as the Stevens 3D body pose estimation. If we detect inconsistencies, this suggests that there may be an error. If so, we will investigate using the RGB-D camera’s depth signal to provide more accurate observations. We are currently avoiding this to avoid a dependency on an extra sensor.

# 

# **TRL 3.6: Inspection of Test Data**

We validate the quality of the data as follows:

* **Bounding boxes:** We inspect the predictions of the model to ensure that: 1\) the system predicts a bounding box for scenes in which the probe is visible, 2\) the bounding box approximately covers the probe.  
* **3D point:** We validate that the predicted position of the probe approximately matches that of that of the observer.

# **TRL 3.8: Data collection and processing conducted to support component**

* **2D object detector**. We (one of the authors) train the 2D object detector by hand labeling video frames from recordings (shared by the CSU team) of trial lower limb demo experiments. We do this labeling in an iterative fashion (inspired by active learning). We repeatedly ran the detector then manually relabeled frames that (from visual inspection) had incorrect results.   
* **3D probe predictor**. We manually labeled the 2D position of the probe tip in images in which it is visible. We then obtained pseudo ground-truth depth by running a monocular depth estimator on the input image (specifically, DepthPro: [https://github.com/apple/ml-depth-pro](https://github.com/apple/ml-depth-pro)) and sampling the depth in the tip pixel position from its depth prediction. Similarly to the above, we run the model on the training videos then relabel errors.

# CSU / FINE-GRAINED GUIDANCE

# CSU / FINE-GRAINED GUIDANCE

Flesh each subsection out (with text, results tables, figures, images, etc) to address various documentation needs.

# **TRL 3.2: Documents indicate that system components ought to work together**

The input to our fine-grained guidance method is a pair involving a current ultrasound image and a target ultrasound image. The current ultrasound images are provided in real-time by the ROS node responsible for publishing ultrasound images from the Clarius probe. The target ultrasound images are pre-collected ultrasound images from a DVT scan of a specific patient and are chosen to be the ultrasound images that indicate an ideal spot to perform a compression on (since these are the locations that we want to guide the generalist practitioner towards). Both the current ultrasound image and target ultrasound image are 512 x 512 grayscale images. We need pairs of \<current ultrasound image, target ultrasound image\> because our guidance method makes use of the Farneback optical flow algorithm.

The output of our guidance method is a floating-point 2D vector, which represents the direction in which you should move the ultrasound probe (and roughly how much based on the magnitude of the vector) to have the current ultrasound image match the target ultrasound image. This 2D vector is calculated by negating the average flow of the pixels between the current ultrasound image and the target ultrasound image (it’s negated because to move in the direction of the average pixel flow you need to move the probe, which acts like a camera, in the opposite direction\!). The Farneback optical flow algorithm is used to calculate the flow for each pixel in the current ultrasound image.

# **TRL 3.3: Performance metrics for system components are established**

The performance metrics we are currently using to evaluate success of our fine-grained guidance involve determining if the suggested direction and step size (based on the average flow vector) that the probe should be translated or rotated ultimately reduces the distance or angle respectively towards the ground truth.

**Rotation**

For rotation, we check to see if the guidance reduces the angle between the current orientation and the desired orientation of the probe. We use the metric introduced in the Droste et al. paper titled “*Automatic Probe Movement Guidance for Freehand Obstetric Ultrasound*”:

![][image22]

Where ![][image23] and ![][image24] are quaternion representations of the current probe orientation and the desired probe orientation respectively (both of which are collected from the IMU on the Clarius probe), and ![][image25] is the quaternion representation of the rotation guidance from our model. The left side of the inequality is calculating the resulting orientation after applying the predicted guidance rotation ![][image26] to the current orientation of the probe ![][image27], and then determining the angle between this new orientation and the desired orientation ![][image28]. If this angle is less than the angle between the current orientation and the desired orientation (which is what the right hand of the inequality is calculating), then we know that the predicted rotation has guided us closer to the desired orientation. To obtain the quaternion ![][image29] which represents our predicted rotation for guidance we obtain an axis of rotation, and an angle to determine how much to rotate around that axis. Figure 2 can serve as an example of how we obtain the axis (the line orthogonal to the average flow vector), and then we determine the angle based on the magnitude of the average flow vector. We can then use this axis, which is in the probe’s YZ plane (we are not accounting for rotation around the probe’s X axis), and the angle to calculate the quaternion ![][image30].

**Translation**

We use a similar metric to what we used for rotation to measure the performance of our guidance model in the translation context by comparing the Euclidean distances between the probe’s current 3D location before and after a predicted translation, and its desired 3D location. If we use the Euclidean distance formula:

![][image31]

where *d* is the Euclidean distance function, and **a** and **b** are *n*\-dimensional vectors (in our case, these are just 3-dimensional vectors). Then, we can use the following inequality to evaluate translation performance, which determines if our predicted translation moves us closer to our desired 3D location:

![][image32]

where **c** is a 3D vector containing the coordinates of the probe’s current location, **g** is a 3D vector containing the coordinates of the probe’s desired location, and ![][image33] is the predicted translation needed to move the probe to the desired location (this translation is provided by our guidance model).

# **TRL 3.4: Risk Areas identified in general terms**

The fine-grained guidance optical flow mechanism may communicate inaccurate information if no vein is in view on the ultrasound image. We plan to trade off guidance between coarse- and fine-grained mechanisms depending on if a vein is visible, and weight fine-grained guidance toward detected vein features.

The UI and display of fine-grained guidance has yet to be verified for optimal usability. It remains to be tested if the current display provides guidance that is intuitive and usable while maintaining accuracy.

Similarly, the optical flow-based approach (see TRL 3.8 below) may be overfit to specific human subjects. 

# **TRL 3.5: Risk mitigation strategies identified**

The current UI renders annotations directly on top of the ultrasound image: one arrow in the direction of probe motion and a green dot in the center if the current ultrasound image is on target. 

User studies will be conducted to gather feedback on the UI and conduct iterative updates. Rotational guidance will be rendered as a 3D rotating probe image, similar to the current guidance mechanism used in the VIGIL cardiac ultrasound use case.

To reduce overfitting, a neural estimator approach will be trained to function in concert with optical flow signals. This approach will be trained on a broader subject sample, covering various ages, genders, and body types.

# **TRL 3.6: Inspection of Test Data**

# [https://www.dropbox.com/scl/fo/gm8yalnxctvskjg8ja89a/AHMI27aKRf6AA857k3CO-3s?rlkey=l26rdarpyzkf79giv8o4c0c27\&st=i9l3hwl0\&dl=0](https://www.dropbox.com/scl/fo/gm8yalnxctvskjg8ja89a/AHMI27aKRf6AA857k3CO-3s?rlkey=l26rdarpyzkf79giv8o4c0c27&st=i9l3hwl0&dl=0)

\[TODO: insert link to public data\]

# **TRL 3.8: Data collection and processing conducted to support component**

See description below:

**Algorithms Used for the DVT Fine-Grained Guidance System**

The core algorithm we use in our current fine-grained guidance system is the Farneback dense optical flow algorithm described in Gunnar Farneback’s paper titled “*Two-Frame Motion Estimation Based on Polynomial Expansion*”. Under the hood, we use OpenCV’s implementation of Farneback optical flow. This algorithm works by taking in a frame and its subsequent frame (usually this is the immediate next frame, but logically it could be any frame later in time) and then calculates a 2D vector for each pixel in the frame which roughly describes where that pixel moved to in the subsequent frame. For our guidance system, we take advantage of the Farneback algorithm by averaging all the flow vectors for each pixel to provide a signal of the general direction in which the pixels are moving. More specifically, we postulate that this average flow vector between a current ultrasound image and a target ultrasound image provides us with an adequate fine-grained guidance signal. A small, but important, implementation detail is that we negate the average flow vector, since the opposite direction of average pixel flow is the direction that the probe should be moved in. The short explanation is that the ultrasound probe can be treated like a camera, and the anatomy of the patient is like a static object in a scene. For example, let’s say we have a simple scene that contains only a square object. If our target image is the square in the center of the frame, but our current image has the square somewhere on the left side of the frame, in this case the Farneback optical flow would produce pixel flow vectors that are pointing to the right. However, if we want the current image to match the target image (remember the square is static in our scene, and we are only moving the camera around), then we must move the camera to the left.

We use the average flow vector to perform both fine-grained translation and rotation guidance. Because the ultrasound image is only 2D, our guidance signal essentially loses a degree of freedom, and we have chosen to sacrifice any translational or rotational guidance in and around the X-axis based on the probe’s coordinate frame in Figure 1\. In the translational sense of guidance, this means we sacrifice up and down movement signals, and in the rotational sense we are sacrificing “roll” rotations.

![][image34]

**Figure 1: The Clarius Ultrasound Probe’s coordinate frame, from the *motion* repository in the *clariusdev* GitHub Organization:** [https://github.com/clariusdev/motion](https://github.com/clariusdev/motion)

For translation, we define the vertical or height part of the ultrasound image to align with ultrasound probes Z axis, and the horizontal or width part of the ultrasound image to align with the Y axis of the probe. This allows us to correlate an average flow vector in the ultrasound image plane with a translational movement in the probe’s YZ plane. For example, if an average flow vector is pointing straight down in the ultrasound image plane, then this corresponds to a movement in the probe’s positive Z axis direction. For rotation guidance, we do something similar except we determine the axis of rotation in the probe’s YZ plane and then rotate towards the direction the average flow vector is pointing. A visual description of this is provided in Figure 2\. In both cases, the magnitude of the average flow vector indicates how much you should translate or rotate by (higher magnitudes indicate a larger distance away from the target).

![][image35]

**Figure 2: A visualization of how to interpret fine-grained rotational guidance from a 2D average flow vector. The dotted line represents the line in the probe’s YZ plane that is orthogonal to the average flow vector. This indicates that the system expects the user to rotate around this orthogonal line towards the direction the average flow vector is pointing.**

To recap, the Farneback optical flow algorithm ultimately gives us an average flow vector between a current ultrasound image and a target ultrasound image, and we have some heuristics for corresponding this average flow vector with a translational or rotational probe movement. But how do we get the target ultrasound images that serve as the points on the leg that we should guide the practitioner towards? Unsurprisingly, we just take some ultrasound images from a previous DVT scan of a patient, specifically we choose the ultrasound images just before a compression begins from a particular scan. We then preload these target images into our system and make use of the progression tracking component of our system to indicate which target ultrasound image should currently be used by our fine-grained guidance component. For example, if the progress is between 0% \- 25% then pick target image 1, between 26% – 50% pick target image 2, etc.

An obvious limitation is that the primary anatomy in the target images, the artery and veins (and perhaps some of the surrounding tissue/structures), should also be present somewhere in the current image. If they are not, then claiming that the average flow vector is a viable guidance signal is unreasonable. Therefore, we do not intend for our current system to be used in a “coarse-grained” sense, and we rely on other components of our system (like the projector and mesh systems) to assist with roughly guiding the probe to the correct locations on the leg. Once the anatomy is in view, our fine-grained guidance is intended to lead the practitioner to the ideal compression spot to produce a more high-quality sequence of ultrasound images by attempting to closely match one of the target images that we have preloaded into our system.

**Model Training**

One of the benefits of using the Farneback optical flow algorithm as our underlying guidance method is that it does not require any model training whatsoever, as there are no learnable parameters or neural networks involved. It only requires that the two images are the same height and width.

**Hyperparameter Choices**

The hyperparameters for the Farneback algorithm were chosen based on those provided in the OpenCV tutorial: [https://docs.opencv.org/3.4/d4/dee/tutorial\_optical\_flow.html](https://docs.opencv.org/3.4/d4/dee/tutorial_optical_flow.html)

We have not performed any experiments or hyperparameter searches to determine which combination of hyperparameters are best for our purposes and have qualitatively found the “defaults” to be sensible for now. Explicitly we set:

·         Pyramid scale \= 0.5

·         Pyramid levels \= 3

·         Averaging window size \= 15

·         Iterations at each pyramid level \= 3

·         Pixel neighborhood \= 5

·         Gaussian filter std. dev. \= 1.2

# CSU / HAND TRACKING

# CSU / HAND TRACKING

Flesh each subsection out (with text, results tables, figures, images, etc) to address various documentation needs.

# **TRL 3.2: Documents indicate that system components ought to work together**

Hand tracking uses RGB imagery from either the RealSense or GoPro cameras. The current demo as of November 2025 uses the RealSense only, but as hand tracking uses the MediaPipe package ([https://mediapipe.readthedocs.io/en/latest/solutions/hands.html](https://mediapipe.readthedocs.io/en/latest/solutions/hands.html)), it is adaptable to any RGB video. Hand tracking produces an array of point data including 21 3D coordinates per hand (X, Y, and Z) indicating key joints (“landmarks”) on the wrist, hand, and fingers. We use the X and Y coordinates that are returned from MediaPipe and return a Z coordinate based on the depth values from the RealSense camera (see TRL 3.8 below for more details). Tracked hands then go into processes like probe tracking and guidance rendering.

**TRL 3.3: Performance metrics for system components are established**

# We use a minimum detection confidence of 0.3 (of a range between 0 and 1\) for detection to be considered successful. A relatively low value was selected to better detect hands when partially obfuscated by the probe or patient’s leg.

# **TRL 3.4: Risk Areas identified in general terms**

# The primary risks to robust hand tracking are challenges by variant light conditions, hand poses, occlusions by the body of the patient or practitioner, or hands being covered by medical equipment, such as gloves.

# **TRL 3.5: Risk mitigation strategies identified**

Light condition effects can be mitigated by ensuring appropriate lighting conditions in the CDP/demo setting. However, a more robust solution can be achieved through fine-tuning MediaPipe or building a custom MediaPipe-based model to ensure robustness to low-light conditions. Similarly, common medical equipment such as blue or black latex gloves can disrupt hand tracking, as can the projected guidance being projected on the hand. To mitigate these effects, we are exploring fine-tuning solutions and collecting supplementary data in these conditions.

To collect supplementary data and avoid the intensive work of manually labeling joints and landmarks on all hands, we will collect a sample of data from the perspective of the RealSense and GoPro cameras with uncovered hands being held in fixed pre-specified positions in the mock CDP constructed in our labs. Since the camera frame is fixed, we can use OTS MediaPipe to extract the hand landmarks from those images, then collect further data of hands being held in the same pre-specified positions in the CDP setup but under variant conditions, such as lower lighting, covered by globes, under the projected guidance, etc., and then reapplying the ground truth landmarks to those new frames to collect the augmented sample. Training over this data may result in a somewhat overfit solution, but this should be acceptable because we need a hand detector specific to the PARADIGM and CDP context rather than a general “in-the wild” hand detector.

# **TRL 3.6: Inspection of Test Data**

\[TODO: insert link to public data\]

# **TRL 3.8: Data collection and processing conducted to support component**

# MediaPipe is an off the shelf package that is trained over \~30K real-world images manually annotated with 21 3D coordinates. To better cover possible hand poses and provide additional supervision on the nature of hand geometry, the developers also rendered a high-quality synthetic hand model over various backgrounds and mapped it to the corresponding 3D coordinates.

To detect hand locations, MediaPipe uses a single-shot detector optimized for mobile realtime use. After palm detection, hand landmark detection is run over the palm detection bounding box to extract precise localization of 21 hand and joint coordinates in 3D via direct coordinate regression. From the MediaPipe documentation: *The model learns a consistent internal hand pose representation and is robust even to partially visible hands and self-occlusions”*

If `static_image_mode` is set to true, *“hand detection runs on every input image, ideal for processing a batch of static, possibly unrelated, images.”*  We set this mode to `true` because the RGB frame was relatively small and didn’t negatively affect the efficiency. Additionally, we wanted to detect the hands in new locations if it was obfuscated by the probe or patient’s leg for a period of time, without relying on MediaPipe to try to track that movement. 

The Z coordinates from MediaPipe are relative to the wrist joint of the hand and depend heavily on the size of the hand, and thus provide only a “guesstimate” of how far from the camera the hand truly is. Therefore, we convert the returned X and Y values to a depth value using the RealSense depth data, and return this as the Z coordinate, which provides a higher fidelity measurement than using the MediaPipe value.

 

# CSU / LOWER LIMB DEMO INTEGRATION

# CSU / LOWER LIMB DEMO INTEGRATION

Flesh each subsection out (with text, results tables, figures, images, etc) to address various documentation needs.

# **TRL 3.2: Documents indicate that system components ought to work together**

The VIGIL Lower Limb guidance system consumes inputs from multiple components, specifically a **pose estimator** that estimates and renders the current patient pose, a **hand tracker** that tracks the position of the hand holding the ultrasound probe, a **progress tracker** that estimates the percent of the task (lower limb ultrasound scan) that has been successfully completed, and a **fine-grained guidance system** that presents microadjustments needed to probe position and orientation to acquire a high-quality image of the vein. Guidance is partially rendered via **projection** on the patient’s body, which updates based on the patient’s estimated pose. The figure below shows information flow between components.

![][image36]

# **TRL 3.3: Performance metrics for system components are established**

The below description contains metrics and discussion of runtime performance. Performance metrics such as accuracy for the individual components are described in the relevant component document where necessary.

**Performance and Rendering/Throughput metrics**

**Pose estimation and mesh rendering**  
	The pose estimator requires approximately 13 GB of GPU VRAM. The mesh rendering is updated at 30FPS (this can be lowered to increase performance).

**Hand tracking**  
	Hand tracking can be calibrated to track one or many hands, and left or right hands only. Hand tracking currently uses aligned RGB and depth images. The modalities are aligned using a maximum offset window of 0.5 seconds (the default value within MediaPipe). 

**Progress estimator**  
	The progress estimator itself is low-resource, requiring under 1 GB of GPU VRAM. It functions off the output of the Clarius probe and the RealSense RGB stream. The latest progress update is always saved, meaning that progress can in principle go down (and frequently does if, e.g., the practitioner departs from the vein.

**Fine-grained guidance**  
	The fine-grained guidance currently has a throughput synced to that of the Clarius probe (which is variable but sends multiple updates per second).

**Projection system**  
	The projection system receives the pose estimate and projects a) patient alignment and b) coarse-grained guidance (approximate compression points) onto the patient’s leg.

# **TRL 3.4: Risk Areas identified in general terms**

The most substantial risk to the overall lower limb use case system is one of latency. As there are multiple components running simultaneously and exchanging information, significant computational resources are expended at any given time. This may cause latency in the processing of patient pose or communication of information like coarse- and fine-grained task guidance. If latency becomes too much or too obtrusive, users may abandon use of the system, or guidance may be communicated too late to be useful or helpful, or may even be misleading, increasing the risk of task failure. Additional risks include lighting sensitivity of many components. Mitigation strategies are described below.

# **TRL 3.5: Risk mitigation strategies identified**

**Pose estimation and mesh rendering**  
	Consumes \~970% CPU resources (hardware agnostic for 2024-25 CPUs), which is a large cause of the latency. The rendering code is being optimized. Currently, the queue for pose estimate subscription has a queue size of 10\. Decreasing the queue to 1 may improve latency and reduce CPU consumption. The rendered mesh image is published without any compression. Adding compression may reduce/mitigate any latency during transport between the lower limb system node and display manager node. The mesh texture has a resolution of 1024x1024. Reducing this to 512x512 will reduce CPU computation.

**Hand tracking**  
	Hand tracking can be affected by lighting conditions. This can be mitigated by ensuring appropriate lighting conditions in the CDP/demo setting, but also through fine-tuning MediaPipe or building a custom MediaPipe-based model to ensure robustness to low-light conditions. Similarly, common medical equipment such as blue or black latex gloves can disrupt hand tracking, as can the projected guidance being projected on the hand. To mitigate these effects, we are exploring fine-tuning solutions and collecting supplementary data in these conditions.

**Progress estimator**  
	The progress estimator may have trouble rising above 0% if the common femoral vein is not found or not recognized. This may be due to differences in lighting conditions from the conditions in which the training data was collected. Like hand tracking, this can be mitigated by ensuring appropriate lighting conditions in the CDP/demo setting, and also by retraining with variations in lighting. The updated progress estimator also now tracks progress broken down by the specific relevant veins in the lower limb use case and the lower limb guidance system will utilize this information to better calibrate overall progress (e.g., if the CFV is poorly detected by a lower portion of the femoral vein is successfully found, progress can be incremented while the guidance still prompts the generalist to go back and find the CFV).

**Fine-grained guidance**  
	The fine-grained guidance optical flow mechanism may communicate inaccurate information if no vein is in view on the ultrasound image. We plan to trade off guidance between coarse- and fine-grained mechanisms depending on if a vein is visible, and weight fine-grained guidance toward detected vein features.

**Projection system**  
The projection system also consumes high CPU resources (\~600%). We will optimize projection either through GPU offloading or CPU saving workarounds.

# **TRL 3.6: Inspection of Test Data**

\[TODO: insert link to public data\]

# **TRL 3.8: Data collection and processing conducted to support component**

## **Data collection**

Ultrasound data of lower limb scans was collected from 3 subjects, all healthy males between 25 and 38 years old. Each sample consists of a scan “trajectory” that started at a randomized location on the subject’s proximal thigh, moved superior to the common femoral vein (CFV) and then followed the femoral vein down the thigh, concluding with a scan of the popliteal vein behind the knee. Scans were performed by 1 of 4 trained ultrasound technicians using a Clarius PAL HD3 Wireless Ultrasound Scanner from Clarius Mobile Health.  During each scan, variables included speed of the procedure, number of compressions performed during the scan, and whether the sonographer performed an “optimal” scan—tracking the vein all the way through the procedure—or deliberately introduced some mistakes. Modalities captured during each scan included the raw POCUS image, live feed from the ceiling-mounted RealSense camera, live feed from the GoPro camera, and IMU data captured from the Clarius probe. A total of 306 individual scans were collected, each lasting approximately 1-2 minutes. Due to technical issues, some modalities may have not been recorded for certain scans but all scans contain data from the ultrasound image and RealSense camera channels. Below we provide a sample synchronized frame from the RealSense camera and Clarius probe:![][image37]![][image38]

## **Annotations**

Annotations of veins were conducted by members of the VIGIL team from Central Michigan University. Annotations consist of frame ranges in the recorded ultrasound video at which relevant veins appeared.  E.g., an annotation of \[3206–3516\], “Common Femoral Vein” indicates that the CFV was visible from frame 3206 to frame 3516 in the given video. An example is below:

\[{"ranges":\[{"start":2908,"end":3025}\],"timelinelabels":\["Femoral vein"\]},{"ranges":\[{"start":3026,"end":3516}\],"timelinelabels":\["Common Femoral vein \- close to inguinal ligament"\]},{"ranges":\[{"start":3519,"end":4063}\],"timelinelabels":\["Femoral vein"\]},{"ranges":\[{"start":4116,"end":4686}\],"timelinelabels":\["Femoral vein"\]},{"ranges":\[{"start":4847,"end":5080}\],"timelinelabels":\["Popliteal vein"\]}\]  
**Processing**

The different channels may be captured at different frame rates, and so a synchronization procedure was used to ensure that every image frame capture from the ultrasound probe could be linked to the nearest frame captured from every other channel using shortest temporal distance or maximum length of overlap.

The procedures used to train individual components for the lower-limb demo are described in further the associated document for those components.

**Overall Architecture**

\*\*Archive below  
![][image39]

# Michigan / Projector

# Michigan \- Projection-based visualization

# **TRL 3.2: Documents indicate that system components ought to work together**

The projection-based visualization component aims to show the guidance most suited to be in-situ, directly on the bed surface. Such visualizations have the benefit of directly augmenting the physical environment, reducing context switching, and facilitating generalist-patient shared understanding. The projection visualizations is a unified layer to support all use cases, and all stages of the use cases.

We set up our hardware and software by following this: [Projection Setup Documentation](https://docs.google.com/document/d/1BOgasiMH_Bx-clsoBlg0NEM6lL-LI_bMcYxwtJO4W94/edit?tab=t.0#heading=h.os5c4vwhk90n)

Our system has three major modules: **Calibration**, **Visualization** and **Flow Control** for the whole process.

### **1\. Calibration**

This module aligns the projection with the physical world using two methods:

* **Projection Calibration (Marker-based)**: Four physical ArUco markers are first placed at the corners of the desired projection region on the patient table. By detecting these markers in the camera feed, the system estimates the inner rectangle that defines where the projected content should appear in the real world. During calibration, the projector then displays a calibration image that contains virtual ArUco markers at known pixel locations in projector space. The system detects these markers and uses cv2.findHomography to compute a homography matrix mapping 2D points from the camera coordinate system to the projector coordinate system. Hence, using this homography together with the detected positions of the four physical markers, the visualization image is transformed to fill that region, so the projection appears at the correct physical location.  
  ![][image40]  
    
* **Patient Alignment (Joint-based)**: The system uses real-time 2D joint coordinates from the STEVENS team to dynamically calculate the area of some projection components. Specific landmarks (e.g., left\_clavicle, left\_hip, left\_knee) are used as anchor points. Target areas (e.g., the target area for scanning in the lower-limb case) are dynamically calculated as offsets from these joints. Thus the projection can follow the patient's body movement in real-time.  
  ![][image41]![][image42]


### **2\. Visualization**

This module generates the visual interface using OpenCV. The interface aims to provide guidance and real-time information for both the patient and the generalist.

Specifically, we generate items as listed below.

* **Body contour image**: a pre-drawn image to introduce the expected posture and position of the patient.  
  ![][image43]![][image44]  
* **Progress and step guidance** (cardiac only): a progress bar above the patient's head with descriptions of the prev/current/next step.  
  ![][image45]  
* **Target area indicators**: a rectangular box over the patient’s body, labeled with text description to indicate the initial probe placement location. We generate this box according to the patient’s pose in real-time.  
  * Cardiac: show the position of the “5th Intercostal Space” with the text description below, update its color according to the LVEF result later.  
  * Lower limb: show the box around the location of the left hip. 

  ![][image46]![][image47]

* **Reference and guidance lines**: a line over the patient’s body, labeled with text description or rectangles as references. We generate this line according to the patient’s pose in real-time.  
  * Cardiac: show the “midclavicular line”.  
  * Lower limb: show the guidance line from the left hip to the left knee, with the rectangle boxes in different colors. The extended line above each box can guide the generalist even when the box is covered by the hand and probe.  
    * Green: scanned area & the result seems good  
    * White: next area to be scanned  
    * Yellow: unscanned area  
    * Red: scanned area & the result seems bad

  ![][image48]![][image49]

* **Probe location indicator**: a red dot that represents the location of the probe in the projection.  
  ![][image50]  
* **LVEF indicators** (cardiac only): a rectangle with numeric percentage inside that can update its color based on the LVEF result. This should be displayed to the right of the patient’s head.  
  ![][image51]

### **3\. Flow Control**

This module manages the application flow using a step-based structure. Once we get the StepInfo message, we align our internal task state machine to the corresponding step and update the visualization accordingly. 

**TRL 3.3: Performance metrics for system components are established**

In general, our projection system is responsible for displaying the data we received at some specific locations, so the evaluation will be majorly based on the accuracy. 

### **1\. Calibration**

Our calibration pipeline relies on an independent detection algorithm for identifying ArUco markers and computing the projected display region. The detection results should guarantee that the calculated projection area **precisely matches the marker-defined physical region**. All projected content should appear at the expected location on the table.

### **2\. Visualization**

Some data streams can be directly displayed without modification (e.g., LVEF results, step information), and the system should ensure that these values are **shown** **accurately at the expected location and updated in real-time**. Other data streams must be transformed internally before projection and updated in real-time (e.g., probe location, pose estimation). For these transformed signals, the system must ensure that the resulting visual elements are **assistive, reasonable, dynamic, and correctly aligned** with the probe/patient’s body.

* **Body contour image**: occupy the entire projection area, use the specific image based on the case.  
* **Progress and step guidance** (cardiac only): displayed above the patient’s head, with the text oriented downward toward the patient.  
* **Target area indicators**: dynamically update the location based on the pose estimation; accurately show the text description as described in *TRL 3.2 \- visualization*.  
* **Reference and guidance lines**: dynamically update the location based on the pose estimation; accurately show the text description or visual guidance as described in *TRL 3.2 \- visualization*.  
* **Probe location indicator**: dynamically update the location based on the probe tracking data.  
* **LVEF indicator** (cardiac only): displayed to the right of the patient’s head, with the text oriented toward the generalist; update the color and the numeric percentage based on the LVEF result we received.

# **TRL 3.4: Risk Areas identified in general terms**

### **1\. Projector**

Projector’s performance can influence both the generalist and the patient. We need to find a solution to guarantee the effectiveness of guidance, while considering the potential negative impact on the patient’s comfort and safety.

* **To the generalist: the effectiveness of guidance.** Our projection aims to provide real-time guidance to the generalist, and the quality of such guidance depends on the projector’s core specifications, including brightness, resolution and throw ratio. In bright environments, limited brightness or resolution may reduce image clarity and weaken guidance’s effectiveness. The mounting height and projection angle also introduce risks. Insufficient height can reduce the usable projection area, while different angles can increase the likelihood and extent of occlusion.

* **To the patient: comfort and safety.** In the cardiac case, the projection area overlaps with the patient’s facial region, and the projected light will inevitably reach the patient’s eyes. This can negatively affect the patient's comfort and potentially raise concerns about visual safety.

### **2\. Accuracy and Stability**

* **Balance between dynamic and stable projection.** Our system continuously adjusts the projected content based on the patient’s pose. However, the reliability of these adjustments can be influenced by the accuracy of patient detection and the tolerance built into our projection logic. Even when the patient remains still, small recognition errors or sensor noise may cause the projection to make slight, continuous shifts. Such shifts can distract the generalist, reduce confidence in the guidance, and undermine the perceived stability of the system.

# **TRL 3.5: Risk mitigation strategies identified**

### **1\. Projector**

* **Hardware: choose a better projector.** Modern models provide substantially improved brightness, resolution, throw ratio, and even optical zoom and lens shift. Such improvements ensure that the visual guidance remains visible and clear under brighter ambient conditions. It also allows more flexible installation and larger projection areas without introducing occlusion risks.  
    
* **Software: implement facial-region avoidance.** The system can implement facial-region avoidance by detecting the patient’s head and excluding that area from projection.

### **2\. Accuracy and Stability**

* **Apply smoothing to projection.** We can implement stability constraints in the projection logic to prevent updates unless the pose change exceeds a threshold. More broadly, we can implement and improve algorithms and pipelines that work with the model’s output.

# **TRL 3.6: Inspection of Test Data**

We inspect the test data produced during evaluation to confirm that each component behaves consistently with the performance criteria defined in TRL 3.3. This includes 

* Check calibration outputs such as detected marker coordinates and homography results.  
* Check the generated projection image for each step.  
* Verify that joint-based projection elements match the patient’s posture.   
* Ensure that flow-control transitions appear in the logs as expected. 

# **TRL 3.8: Data collection and processing conducted to support component**

We collect and process the location of markers with the camera in the calibration. Please refer to *TRL 3.2 \- calibration* for more technical details.

We directly utilize the frame data from the camera, step information from the system and the LVEF results from the Penn team.

Other than these, we process the pose estimation data from the STEVENS team and the probe tracking data from the Cornell team by transforming the camera coordinates into our projection coordinates.

# Rochester / Reporter

# Rochester / Reporter

Flesh each subsection out (with text, results tables, figures, images, etc) to address various documentation needs.

# **TRL 3.2: Documents indicate that system components ought to work together**

The proposed Multimodal Medical Report Generation System integrates three state-of-the-art components to synthesize patient-generalist audio and echocardiogram video into a standardized medical report. The pipeline consists of an Automatic Speech Recognition (ASR) module, a Cardiac Vision-Language Interpreter, and a Large Language Model (LLM) acting as the reasoning engine.

### **1\. Component Descriptions**

* **ASR Module (OpenAI Whisper Large v3 Turbo):** The system utilizes the whisper-large-v3-turbo model for transcribing doctor-patient conversations. This model is a distilled version of Whisper v3, featuring a reduced decoding layer count (from 32 to 4\) while retaining the same encoder architecture. This configuration allows for near-real-time transcription latency with minimal degradation in Word Error Rate (WER), ensuring that long clinical conversations are processed efficiently.  
* **Cardiac Interpreter (EchoPrime):** Visual interpretation is handled by **EchoPrime**, a foundation model specifically designed for echocardiography. Trained on over 12 million video-report pairs using contrastive learning, EchoPrime utilizes a view-informed anatomic attention mechanism. For this pipeline, it processes the ultrasound video input (specifically the Apical 4-Chamber view) and retrieves relevant semantic interpretations and clinical findings to be fed into the LLM.  
* **Reasoning Engine (Qwen2.5-7B):** The synthesis and cross-validation logic are powered by **Qwen2.5-7B**, a 7-billion parameter instruction-tuned model. Selected for its strong reasoning capabilities and support for a 128k token context window, this model is capable of ingesting the raw transcripts and visual interpretations to generate structured medical reports.

### **2\. Integration Workflow**

![][image52]

The components are integrated into a sequential pipeline:

1. **Ingestion:** The system accepts raw audio and DICOM/video ultrasound files.  
2. **Parallel Processing:**  
   * The ASR module converts audio to `transcript_content`.  
   * The EchoPrime interpreter analyzes the video and retrieves `interpretation_content`.  
3. **Synthesis:** These outputs are injected into a predefined prompt template which enforces cross-validation rules. The LLM synthesizes the final report.

### **3\. Integration Logic (Prompt Template)**

The following prompt template serves as the integration interface, ensuring the LLM receives inputs from both upstream components in a structured format:

"You are a helpful assistant that generates a radiology, heart ultrasound, TTE report based on the doctor–patient conversation, the imaging interpretation, and the images.

This is the doctor–patient conversation: {transcript\_content}

This is the imaging interpretation: {interpretation\_content}

The report template is as follows:

### **Conversation Summary**

(The conversation summary will be presented in this section.)

### **Imaging Interpretation Summary**

(The imaging interpretation summary will be presented in this section.)

### **Medical Report**

(The medical report will be presented in this section.)

Tasks:

* Task1: Summarize the doctor–patient conversation in a few sentences, including the patient information, the diagnosis, and the imaging findings if available. Put the summary in the "Conversation Summary" section.  
* Task2: Summarize the imaging interpretation in a few sentences. Put the summary in the "Imaging Interpretation Summary" section.  
* Task3: Based on the summary of task1 and task2, draft a complete radiology, heart ultrasound, TTE report, include patient information (Name, Age, Gender, Medical History, Medication, Symptoms, etc.), imaging findings, and recommendations. If no relevant information is provided, use N/A to enter the entry. Put the report in the "Medical Report" section.

Rules:

* It is possible that the conversation does not match the imaging interpretation, in this case, you should try to reason the correct diagnosis by cross-referencing the conversation and the imaging interpretation.  
* Provide your response for each task in a separate section, with the headers: Conversation Summary, Imaging Interpretation Summary, and Medical Report.  
* Use markdown formatting for the report.  
* Do NOT include "LVEF" or "Ejection Fraction" anywhere (including the "Conversation Summary", the "Imaging Interpretation Summary", and the "Medical Report" sections)."

# **TRL 3.3: Performance metrics for system components are established**

To validate the effectiveness of the visual interpretation component (EchoPrime) within this specific pipeline, we have established a **Recall Rate** metric.

* **Metric Definition:** The probability that the model correctly retrieves the anatomical interpretation of "Left Ventricle" when presented with a video input of the "Apical 4-Chamber Cardiac View."  
* **Established Baseline:** Preliminary testing indicates the system achieves a 90% recall rate on this task. This metric ensures that the foundational visual data required for the LLM to generate a report is present and accurate before synthesis occurs.

# **TRL 3.4: Risk Areas identified in general terms**

Two primary risks have been identified regarding the reliability of the generated content:

1. **Quantitative Inconsistency (LVEF Reliability):** There is a significant risk that the system will generate inaccurate Left Ventricular Ejection Fraction (LVEF) values. Discrepancies have been observed between the reporter’s generated LVEF, and external measurement (specifically results from the UPenn team). This conflict in quantitative data renders the report less reliable if specific LVEF numbers are hallucinated or miscalculated by the model.  
2. **Visual Hallucination (Out-of-Distribution Retrieval):** EchoPrime employs a retrieval-based mechanism that may occasionally retrieve cardiac interpretations for heart components not visible in the input video. Specifically, when provided with an "Apical 4-Chamber Cardiac View," the model risks retrieving interpretations for anatomical parts not covered in this standard plane (e.g., aortic valve views not visible in A4C), leading to hallucinations in the final report.  
3. **ASR Degradation:** The system relies on accurate transcripts of the generalist-patient conversation. However, clinical environments often contain background noise (machinery, ambient chatter). Background noise may be misinterpreted as phonetic input, causing the model to hallucinate text or fail to capture critical medical terms. Moreover, the ASR model itself may fail to detect speech because it is not robust enough for arbitrary conversation recognition. Therefore, there is a risk of inaccurate speech recognition. 

# **TRL 3.5: Risk mitigation strategies identified**

To address the risks identified in TRL 3.4, the following mitigation strategies have been implemented:

1. **Prompt Engineering (Negative Constraints):** To mitigate the risk of inaccurate LVEF reporting, we have implemented strict prompt engineering constraints. The system prompt explicitly instructs the LLM: *"Do NOT include 'LVEF' or 'Ejection Fraction' anywhere."* This forces the model to focus on qualitative findings and structural descriptions rather than unreliable quantitative metrics.  
2. **Retrieval Mechanism Modification:** To mitigate the risk of out-of-distribution retrieval by EchoPrime, we have modified the retrieval logic. The system now enforces a view-dependency check: when the input video is classified as "Apical 4-Chamber View," the retrieval mechanism is restricted to only access interpretations related to the **Left Ventricle**. This prevents the injection of irrelevant or hallucinated anatomical findings into the LLM context.  
3. **Valid Speech Detection (FastRTC):** To mitigate noise-induced ASR errors, we utilize `fastrtc`'s "reply on pause" mechanism. This functions as a Voice Activity Detector (VAD) that filters out non-speech audio segments. By ensuring the ASR model is only triggered by valid speech patterns and ignores periods of background noise, we reduce hallucinated transcripts.  
4. **LLM Semantic Filtering:** As a secondary layer of defense against ASR errors, we leverage the reasoning capabilities of the Qwen2.5-7B LLM. The model is implicitly instructed to analyze the transcript for semantic coherence. It grabs only meaningful information related to patient history and diagnosis, effectively filtering out any residual nonsense text generated by noisy audio inputs.

# **TRL 3.6: Inspection of Test Data**

Pre-test data inspection protocols have been established to ensure the validity of component testing:

* **Audio Quality Assurance:** For the Speech-to-Text (STT) component, test samples are inspected to ensure the patient-generalist conversation exists, is audible for Whisper v3 Turbo to transcribe accurately.  
* **Video Quality Assurance:** For the cardiac interpretation component, video samples are manually inspected to verify they represent a clear, standard "Apical 4-Chamber Cardiac View."

# **TRL 3.8: Data collection and processing conducted to support component**

The data used to support the development and validation of these components relies primarily on established external datasets. Our testing and validation data are derived from the **University of Michigan (UMich), and University of Pennsylvania (UPenn)** teams.

# Rochester / Speech Recognition

# Rochester / Speech Recognition and Transcription

Flesh each subsection out (with text, results tables, figures, images, etc) to address various documentation needs.

# **TRL 3.2: Documents indicate that system components ought to work together**

The Speech Recognition component is designed to provide real-time transcription and state-trigger detection for the VIGIL agent. This subsystem integrates a streaming voice activity detector with an optimized OpenAI Whisper model, customized via prompt engineering to recognize domain-specific nomenclature.

**1\. Architecture & valid Speech Detection (`fastrtc`)** To enable low-latency interaction, the system utilizes the `fastrtc` Python library to handle real-time audio streams. Specifically, we employ the `ReplyOnPause` function. This mechanism serves as a Voice Activity Detector (VAD) that buffers incoming audio chunks and detects natural pauses in the user's speech. Once a valid pause is detected, the accumulated audio buffer is committed and dispatched to the transcription model. This ensures that the Automatic Speech Recognition (ASR) model only processes complete semantic units rather than fragmented audio streams.

**2\. Transcription Model (OpenAI Whisper)** The core transcription engine is OpenAI’s Whisper, a Transformer-based sequence-to-sequence model. For this component, the model is tasked not only with general transcription but also with the detection of the specialized trigger keyword "VIGIL," which initiates state transitions within the agent.

**3\. Vocabulary Adaptation via Prompt Engineering** Since "VIGIL" is an out-of-vocabulary (OOV) term for the standard Whisper model (often mistranscribed as "visual" or "vigilance"), we utilize the model's context-injection capabilities. We prepend a specific `initial_prompt` to the decoder to condition the model on the expected keyword.

**Prompt Configuration:**

`initial_prompt = "We use a specific phrase 'Hi VIGIL' to indicate important information. Please detect 'Hi VIGIL' and transcribe it as 'Hi VIGIL' when you hear it or when you hear something similar to it."`

This prompt successfully forces the model to bias its probability distribution towards the token sequence "VIGIL" when phonetically similar acoustic features are detected.

# **TRL 3.3: Performance metrics for system components are established**

To validate the reliability of the transcription and keyword spotting, the following metrics have been established:

* **Word Error Rate (WER):** Used to measure the general accuracy of the medical transcription against a ground-truth reference.  
* **Mean Opinion Score (MOS):** A subjective quality metric (scale 0–10) used specifically to evaluate the "VIGIL" keyword detection stability. This metric aggregates scores on how accurately and quickly the model transcribes the trigger phrase without introducing artifacts. A higher score indicates high detection accuracy and low hallucination frequency.

# **TRL 3.4: Risk Areas identified in general terms**

A significant risk area has been identified regarding **Prompt-Induced Hallucination**.

While the injection of the `initial_prompt` (described in TRL 3.2) successfully enables the detection of the "VIGIL" keyword, it introduces a side effect: the model becomes over-sensitive. In the presence of background noise or silence, the strong bias provided in the prompt leads the model to "hallucinate" text—generating words that do not exist in the audio or repeating the trigger phrase ("Hi VIGIL") recursively when no speech is present. This poses a reliability risk, as false triggers could inadvertently activate the agent or corrupt the medical record.

# **TRL 3.5: Risk mitigation strategies identified**

![][image53]

To mitigate the hallucination risk, we conducted a comparative study on model size and speaker variation. We hypothesized that larger models (Large v3/Turbo) might be overfitting to the prompt bias, while smaller models might offer more stability for this specific keyword task.

**Experiment:** We tested five variations of the Whisper model (Tiny, Base, Small, Medium, Large v3 Turbo, Large v3) with a cohort of 5 speakers (3 Native, 2 Non-Native). We measured performance using the MOS (0-10) metric focused on hallucination resistance and trigger accuracy.

**Results Summary:** As shown in our internal benchmarking (refer to TRL 3.8 data), the **Large v3 Turbo** and **Large v3** models achieved an average MOS of **6.8**, showing a higher tendency for hallucination. Conversely, the **Small** (Avg 7.8) and **Medium** (Avg 7.4) models demonstrated significantly higher stability.

**Strategy Implementation:** Based on these findings, the system will not use the `large-v3-turbo` for the specific module handling the "VIGIL" wake-word detection. Instead, we have selected **Whisper Medium**. This model offers the optimal trade-off, providing high-fidelity transcription with a significantly lower rate of prompt-induced hallucination compared to the Large variants.

# **TRL 3.6: Inspection of Test Data**

Pre-test inspection protocols were enforced to ensure data validity:

* **Audio Clarity:** All test recordings were inspected to ensure the speaker's voice was distinct from the background.  
* **Noise Level:** Samples with significant background chatter (simulated hospital noise) were flagged to ensure the Signal-to-Noise Ratio (SNR) was acceptable for the `ReplyOnPause` VAD to function correctly.  
* **Audibility:** Every clip used in the benchmark was verified to be audible by a human annotator without amplification.

# **TRL 3.8: Data collection and processing conducted to support component**

To support the validation of the "VIGIL" trigger, we curated a specific dataset comprised of command scripts read by a diverse group of speakers.

**1\. Script Design:** We designed a transcript specifically to test various invocations of the agent. The script includes the following utterances:

* "Hi VIGIL, Hey VIGIL"  
* "VIGIL Start, Start VIGIL"  
* "VIGIL Stop, Stop VIGIL"  
* "Hi VIGIL system"  
* "Hi VIGIL agent"  
* "What's up, VIGIL?"  
* "VIGIL, where is the probe?"

**2\. Speaker Recruitment:** To ensure robustness against accents—a critical requirement in diverse medical settings—we recruited:

* **3 Native Speakers:** To establish baseline performance.  
* **2 Non-Native Speakers:** To stress-test the acoustic model's ability to recognize the "VIGIL" phonemes under accent variation.

**3\. Processing:** These speakers recorded the scripts listed above. The audio was then processed through the varying model sizes (Tiny through Large v3) to generate the MOS comparison table used in TRL 3.5.

# NU / PROGRESS TRACKING

# NAME / COMPONENT

Flesh each subsection out (with text, results tables, figures, images, etc) to address various documentation needs.

# **TRL 3.2: Documents indicate that system components ought to work together**

# **TRL 3.3: Performance metrics for system components are established**

# 

# **TRL 3.4: Risk Areas identified in general terms**

# 

# **TRL 3.5: Risk mitigation strategies identified**

# 

# **TRL 3.6: Inspection of Test Data**

# 

# **TRL 3.8: Data collection and processing conducted to support component**

 

**1\. Overview** 

Given a sequence of RealSense and ultrasound images from a lower-limb procedure, the VideoProgress model, a causal Temporal Convolutional Network (TCN), aims to identify the vein type currently being scanned and estimate the procedure’s progress. The model takes frame-level feature vectors extracted by a ResNet-34 encoder, refines them with a small MLP, concatenates the RealSense and ultrasound features, and processes the sequence using a multi-stage causal TCN. It outputs both frame-wise vein type predictions and a continuous progress estimate for each vein type. The architecture is fully causal and designed for real-time operation. 

![][image54]Figure 1\. The architecture of VideoProgress model. It illustrates the process of predicting the vein type and progress of the frame at time t. 

**2\. Architecture** 

**2.1 Inputs** 

We first extract frame-wise features for both realsense and ultrasound inputs with ResNet-34 (the top region in Figure 1). In full-video mode (during training), the model expects an input tensor of shape \[B, T, D\], where B is the batch size, T is the number of frames, and D is the feature dimension. In streaming mode (during test), the model receives a tensor of shape \[B, D\], where B is the number of newly arrived time steps for a single ongoing sequence. In both cases, the model assumes fixed-dimensional per-frame feature embeddings computed by an upstream backbone. Therefore, we concatenate the features of realsense and ultrasound to produce the input tensor. 

**2.2  Multi-Stage Causal Temporal Convolution Network (MSCTCN)** 

The middle region of Figure 1 illustrates the process for generating the feature of input at time t. Each frame-level feature is first processed by a two-layer MLP with hidden size 256, ReLU activations, dropout, and a LayerNorm applied over the hidden dimension. A linear projection then maps the encoded features to a 256-dimensional vector, which serves as the input to the Multi-Stage Causal TCN (MSCTCN). The MSCTCN consists of three sequential causal TCN modules, each composed of multiple causal residual blocks. Each residual block includes a causal convolution with left-only padding, followed by GroupNorm, ReLU, dropout, and a residual skip connection, ensuring that all temporal operations remain strictly causal. 

**2.3 Output Heads and Signals** 

The bottom region of Figure 1 illustrates the final model outputs at time (t), including both the predicted vein type and its associated progress value. The MSCTCN produces features of shape \[B, C, T\], which are passed to three separate 1D convolutional heads with kernel size 1\. The cls\_head outputs per-frame logits of shape \[B, T, NUM\_VEIN\_TYPES\], which are converted into class probabilities using a softmax over the vein-type dimension. The delta\_head outputs per-frame progress increments for all vein types except background (which corresponds to the absence of any visible vein). It produces logits of shape \[B, T, NUM\_VEIN\_TYPES \- 1\], followed by a softplus activation to ensure positive values. The remain\_head has the same dimensionality and also applies softplus to produce positive estimates of the remaining progress. A cumulative sum over time of the delta outputs is then combined with the remain outputs to form a normalized progress tensor of shape \[B, T, NUM\_VEIN\_TYPES}- 1\]. Each entry represents a scalar progress value in (0, 1\) for each non- background vein type at every frame.  

**2.4 Online / Streaming Operation** 

During streaming inference, we maintain an internal feature buffer that stores incoming frame features as they arrive. The model processes the entire buffered sequence as a single video and returns only the predictions for the newest frames: the vein type of shape \[B, NUM\_VEIN\_TYPES\] and the progress values for each vein type of shape \[B, NUM\_VEIN\_TYPES \- 1\]. Because all temporal convolutions are strictly causal, the streaming predictions are identical to those that would be obtained by running the model offline on the same sequence up to the current time step.


**3\. Training Procedure** 

**3.1 Overview** 

The training pipeline optimizes the VideoProgress model on pre-extracted per-frame feature sequences stored in NPZ files. Each training sample is a single video represented as a sequence of 1024-dimensional feature vectors with aligned per-frame vein type labels and progress targets. Training is performed per video with a full-sequence forward pass, using the same temporal computation path as will be used for online inference. 

**3.2 Feature Normalization and Forward Pass** 

Before training, the pipeline estimates a global per-dimension mean and standard deviation over the training set and uses these to normalize all feature vectors. For each video, the normalized feature sequence is passed through MSCTCN to generate per-frame vein types and progress values.  

**3.3 Losses, Optimization, and Checkpoints** 

The main supervision consists of three terms: a per-frame classification loss computed as cross-entropy on the one-hot vein type labels, a temporal smoothness loss on the logits that penalizes changes between consecutive frames within the same vein segment, and an L1 loss between the predicted progress channels and the ground-truth progress targets. The total loss is a weighted sum of these components and is optimized with AdamW and a cosine annealing learning-rate schedule. Training runs for multiple epochs over the dataset, and after each epoch the script logs average losses, updates the scheduler, and saves checkpoints containing the model and optimizer state. The checkpoint with the lowest observed training loss is also stored separately as the “best” model for later evaluation or deployment. 

**4\. Data, Labels, and Multimodal Features** 

**4.1 Vein Segmentation Labels** 

Each video frame is annotated with a discrete vein type label from a small, fixed set: background (sil), common femoral vein close to the inguinal ligament, femoral vein, and popliteal or deep femoral vein. Annotation time ranges are converted into a per-frame integer label sequence and then into a per-frame one-hot encoding over this vein type set. These one-hot labels are provided to the training loop and used directly in the classification loss and the smoothness loss. 

**4.2 Progress Targets** 

From the one-hot vein type labels, the pipeline derives continuous per-frame progress targets. A cumulative mask based on the annotated veins is applied to the one-hot labels, and a normalized cumulative sum is taken over time for the non-background channels. This produces a progress tensor with one value per frame and per non-background vein, in the range \[0, 1\]. These progress targets are aligned with the same temporal resolution as the segmentation labels and are used as supervision for the model’s progress output. 

**4.3 Multimodal Feature Representation** 

Each input frame feature is a 1024-dimensional concatenation of two 512-dimensional embeddings: one derived from ultrasound data and one derived from RealSense camera data. In the simplest multimodal configuration, these two modality-specific feature vectors are stacked along the feature dimension and passed to the model as a single 1024-dimensional vector. The training code also supports an ultrasound-only mode by zeroing out the second 512 dimensions, but in the standard setting both modalities are present. The model’s MLP encoder and temporal backbone operate on this fused representation, so all learning, including vein segmentation and progress estimation, is performed jointly over the combined ultrasound and RealSense feature space. 

**5\. Results and tables** 

**Quantitative Results:** First, we show the vein segmentation quantitative results for our model using standard metrics.  

| Split  | Accuracy  | Edit  | F1@10  | F1@25  | F1@50  |
| :---: | :---: | :---: | :---: | :---: | :---: |
| Train  | 92.9  | 59.0  | 78.4  | 73.4  | 59.0  |
| Validation  | 67.6  | 28.3  | 51.7  | 44.1  | 28.3  |

 

Next, we show the MAE for progress predictions.  

| Split  | Progress MAE  |
| :---: | :---: |
| Train  | 4.4  |
| Validation  | 14.1  |

 

**Quantitative Discussion:** We note that the evaluation results on our validation set is quite low. This is because we purposefully used many degenerate videos (bad annotations, improper task execution) in the validation set to test the robustness of our model. Each video in the training set has good annotations, meaning the model could learn better.  

Next, we show our implementation details: 

| Number Epochs  | 200  |
| :---: | :---: |
| Batch Size (\# of videos per update)  | 1  |
| Learning rate  | 0.0001  |
| Random seed  | 1337  |
| VideoProgress input args  | feat\_in\_dim=1024, mlp\_hidden=256, gru\_hidden=256, dropout=0.2, num\_veins=4  |

**Qualitative Results**: Below, we give qualitative results showing the frame-wise vein type predictions (vein segmentation)in barcodes (Ground truth on top, predicted below) and channel-wise progress predictions plus global progress.   

![][image55]![][image56] 

**Qualitative Discussion**: We observe very high-quality vein type and progress prediction. Each channel (vein progress) nicely tracks the \[0-1\] curve within the vein segments. We obtain global progress with a cumulative sum of vein channels divided by the number of vein types. This way we can better understand the global progress estimation (because it comes from the progress channel for each vein type).


# Penn / Cardiac LVEF

# Penn / Cardiac LVEF

# **TRL 3.2: Documents indicate that system components ought to work together**

**Component Overview**

The Cardiac LVEF component of VIGIL assists the generalist to acquire a good quality apical 4-chamber (A4CH) view of the heart with transthoracic echocardiography (TTE). An overview of the multi-task network for A4CH view guidance and LVEF estimation is shown in Fig. 1A. Briefly, the network cascades an ***uncertainty-aware landmark detector*** with a ***transducer pose scoring module***. The intuition is that the transducer position and orientation are optimal when the target anatomy (specified by landmarks) is within the field of view. Images that are scored as diagnostic-quality A4CH views are subsequently passed to an ***LVEF estimator***. A separate ***transducer guidance network*** provides cues for how to rotate the transducer to optimize the view. The scoring system and each network component are described in the following sections. The input and output of the network are summarized as followed:

**Input:** Stream of TTE images (PNG format)

**Output:**

* Color-coded anatomical landmarks of the four cardiac chambers, visually displayed  
* Transducer pose score, visually displayed as one of three “traffic light” categories (green: on target, yellow: close to target, red: far off target)  
* Guidance signal: The current transducer pose and the predicted transducer pose for the A4CH view, visually displayed as two sets of 3D axes overlaid  
* LVEF estimation: an integer value (%), defined as the percentage of blood flow ejected from the heart with each beat

![][image57]  
**Figure 1\. A.** Overview of the multi-task network. TTE video frames first pass through the landmark detector. The images and predicted landmarks then pass through the pose scoring module to be classified as green, yellow, or red. Optimal ”green” clips then pass to an automated LVEF estimator. **B.** Each TTE sweep starts with the transducer in the optimal A4CH pose and drifts away from the optimal pose. Ground truth pose categories (green, yellow, red) are manually assigned and mapped to a numeric pose score.

**Uncertainty Aware Landmark Detection**

The landmark detection module leverages the EchoNet Dynamic dataset \[D. Ouyang et al., 2020\], which comprises 10,000 video clips, each with manual annotations on one diastolic and one systolic frame, totaling 20,000 annotated frames. Each frame includes 42 landmarks that define the LV contour. In addition, we annotate a subset of approximately 3,000 frames with 5 additional landmarks: the right ventricle (RV), right atrium (RA), left atrium (LA), mid-plane of the tricuspid valve (TV), and the tricuspid valve annulus (TVA). For each frame, we specify a landmark visibility score: 1 \- high confidence, 2 \- moderate confidence, 3 \- low confidence. In total, the module identifies 47 landmarks capturing key structures in the A4CH view (Fig.2A,C).   
We formulate the landmark detection problem as a semantic segmentation task. Specifically, we employ a modified ResNet34 architecture that uses pre-trained weights on ImageNet \[K. He et al., 2016\]. We utilize the initial ResNet34 layers as our encoder, followed by a progressively larger decoder designed to capture fine-grained landmark information \[J. McCouat and I. Voiculescu, 2022\]. The decoder contains five upsampling layers (channel dimension: 512, 256, 128, 64, and 64), each block contains a batch normalization layer and a ReLU activation. Following the final decoder layer, we use a 1 × 1 convolution layer to map feature maps into 47 channels. To train the network to handle partially available landmarks and varying visibility levels, we use a weighted negative log likelihood loss.   
To compute this loss, we reshape logits from (B, L, H, W ) to (B, L, H × W ) and apply log softmax over the spatial dimension. We convert each ground-truth landmark coordinate (x, y) into a 1D index (y × W \+ x), clamp it to valid bounds, and extract the corresponding log-probabilities. A boolean mask (B, L) identifies annotated landmarks, and we weight their log-probabilities by this mask and the sample-wise visibility weights vis w ∈ RB , thereby emphasizing highly visible landmarks \[A. Kumar et al., 2020\]. The batch loss function for batch size B is:  
![][image58]  
Finally, to extract uncertainty, we apply a 2D softmax to each landmark channel, yielding spatial probability distributions. The uncertainty is reported as the radius of non-zero regions  
in these distributions \[J. McCouat and I. Voiculescu, 2022\].   
The EchoNet Dynamic dataset is partitioned with an 80%/10%/10% train/validation/test split. We fine-tune the pre-trained ResNet34 encoder for 20 epochs using the Adam optimizer at a learning rate of 0.001, with a batch size of 512\.

**Transducer Pose Scoring Module**

We use a “traffic light” metaphor to capture transducer pose quality scoring: on target (green), close to target (yellow), or far from target (red) relative to the ideal A4CH view. Training the module involves systematic image acquisition illustrated in Fig. 1B. TTE from each patient is acquired in a series of approximately 40 “sweeps.” Each sweep is a 10-second video clip wherein the expert sonographer starts with the transducer in the optimal A4CH pose and gradually moves away in a random trajectory. Altogether, the sweeps apply random pose transformations relative to the optimal A4CH view that are confined to and capture the entire anatomical region illustrated in Fig. 1B. Transducer pose scores (green, yellow, red) are collaboratively assigned to image frames within each sweep by two individuals based on the point-based criteria described in Table 1\.  
We investigate two approaches for pose scoring. The first approach uses ResNet18 and formulates the problem as a regression task to capture the continuous nature of pose changes. Green, yellow, and red classes are each mapped to a continuous pose score as shown in Fig.1B. Green scores range from 1 to 0, yellow from 0 to \-1, and red from \-1 to \-2. The score assigned to each frame is subsequently used as the ground truth score for the regression task. We conduct subject-level 5-fold cross validation in which each fold uses all frames from two subjects as the test set, one subject as the validation, and the rest as training set. This module is trained for 100 epochs with the Adam optimizer and a class-weighted mean squared error loss function, and the weights with the lowest average validation loss of the last 5 epochs are saved. Augmentation including brightness and contrast, uniform scaling, horizontal and vertical translation are applied during training.  
The second approach explores a multi-modal LLM architecture (LLaMA \[H. Touvron et al., 2023\]) conditioned on both image data and task instructions for transducer pose scoring. Following LLaMA-Adapter \[R. Zhang et al., 2023\], we integrate visual information from TTE frames into the LLaMA model by combining encoded image features with adaption prompts. The frames are first processed through a ResNet18 encoder to extract visual features, which are then projected into the LLM embedding space. Task instructions are tokenized and mapped to text embeddings via the frozen LLaMA embedding layer before being passed as input to the language model. To generate the final prediction, a projection layer takes the embeddings from the last hidden layer of the LLM and maps them to the three pose categories. This approach maintains the LLaMA model parameters frozen while only training the ResNet encoder, the projection layers, and the adaption prompts. This approach is trained for 100 epochs using the AdamW optimizer, and a cyclical learning rate scheduler. The same augmentations are applied.  
![][image59]  
**Table 1\.** Pose scoring criteria for TTE frames.

**Transducer Guidance Network**

We curated a rotation dataset of ≈100K frames from 8 subjects, acquired with a protocol that mirrors the imaging workflow (Fig. 1B): for each patient we record \~50 sweeps, each a 10-second video in which an expert sonographer begins in the optimal A4CH pose and then slowly moves away. The ultrasound transducer has an internal 9‑DoF IMU, providing a per‑frame orientation quaternion. For each sweep we designate the initial frame as the reference (anchor) and compute, for every subsequent frame, a relative rotation via the quaternion delta:  
  qrel=qref-1⊗qframe​. 

We convert qref​ to a 3×3 rotation matrix *R*, and also store its continuous 6‑D parameterization which we regress during training and map back to SO(3) with a differentiable Gram–Schmidt step for continuity and validity \[Zhou et al., CVPR 2019\]. This representation avoids the discontinuities of Euler angles and the unit‑norm constraint of quaternions while preserving orthonormality.  
We formulate rotation estimation as regressing the relative rotation to the sweep’s anchor directly from images. The model uses a ConvNeXt‑Base backbone pretrained with DINOv3 \[Siméoni et al., 2025\]. We remove the classifier, apply global average pooling (≈1024‑D features), and add a dual‑head predictor: a solo head that maps the current frame features to a 6‑D rotation code, and a pair head that maps the concatenation of \[current, anchor\] features to the 6‑D code.   
We train on 6 subjects and hold out 2 subjects for testing (subject‑wise generalization). Within the training pool, we reserve \~12% of sweeps for validation at the sweep level to prevent frame leakage. The loss is a weighted dual objective:

L=λpair ∥Rpair−Rgt∥2F  +λsolo ∥Rsolo−Rgt∥2F​ 

with defaults λpair=λsolo=1; ∥⋅∥2F​  denotes the Frobenius error on rotation matrices. We monitor accuracy using the SO(3) geodesic angle  θ=cos-1 ⁣tr(Rgt⊤Rpred)−12 reported as mean/median/p90/max (degrees). Optimization employs AdamW (LR 1×10−4, weight decay 1×10−4), a 5‑epoch warm‑up followed by cosine annealing, batch size 32, and mixed precision (AMP) for 60 epochs. The best checkpoint is selected by validation solo mean geodesic error, ensuring that the model trained with anchor context also performs strongly when relying on the current frame alone.

**Fully Automated LVEF Estimation Module**

We fine-tune a deep R(2+1)D model \[D. Tran et al., 2018\] with EchoNet Dynamic training data and code \[D. Ouyang et al., 2020\] for 100 epochs with default train-test split in EchoNet. Video segments that have at least 26 frames or 1 second duration are passed to the model. 

# **TRL 3.3: Performance metrics for system components are established**

Verification of each component of the multi-task network is carried out as follows:

**Landmark Verification in EchoNet** 

We verify the performance of our landmark detection module on the testing subset of EchoNet Dynamic by examining its ability to localize clinically significant anatomical landmarks. Figure 2A shows qualitative results, and Figure 2B demonstrates the uncertainty in each predicted landmark. Across all visible landmarks, our method achieves an ***average mean Euclidean distance error (pixels)*** of 2.47±1.29. Notably, for key landmarks used in the subsequent pose scoring module, we achieve error as follows: LV apex exhibits a mean distance of 3.18 ± 5.84, MV 3.60 ± 9.81, RV 2.33 ± 1.28, TV 2.37 ± 2.30, RA 2.79 ± 2.00, and LA 3.06 ± 3.69.

![][image60]  
**Figure 2\. A.** Predicted and ground truth landmarks **B.** Predicted landmarks displayed with prediction uncertainty **C.** Key landmark output passed on to pose scoring module

**Pose Scoring Evaluation** 

Overall ***accuracy*** of automated pose scoring relative to manual pose scoring is shown in Table 2\. The ResNet18 regression backbone generally has the best performance when trained with both images and landmarks. Fig. 3 shows the overall confusion matrix of the ResNet18 regression backbone with examples of each type of misclassification.  
![][image61]  
**Table 2\.** Test accuracy of the transducer pose scoring module

![][image62]

**Figure 3\.** Overall confusion matrix and corresponding examples of misclassified TTE frames using images and landmarks.

**Transducer Guidance Evaluation**

As described above,  we evaluate ***rotation prediction accuracy*** using the SO(3) geodesic angle θ=cos-1 ⁣tr(Rgt⊤Rpred)−12 reported as mean/median/p90/max (degrees). On validation, p90 ≈ 10° indicates strong alignment for most frames; on held‑out subjects, p90 ≈ 18° despite a low median (\~4.5°) shows a heavier tail—most frames are accurate, but a subset incurs large errors. Conditioning on the anchor (pair head) yields small, consistent mean gains (\~2–3%) without shrinking the tail, suggesting the hardest cases arise from out‑of‑manifold probe poses or weak acoustic windows. The mean gap from validation to test (\~+1.8°) and the larger tail gap (p90 \+7.6°) reflect subject‑level shift; this likely benefits from augmentation near sweep‑trajectory limits, curricula emphasizing large rotations, and stronger cross‑frame fusion (e.g., attention to the anchor). Overall, the model reaches \~5.3–5.5° mean error on validation and \~7.0–7.2° on held‑out subjects, with low medians but heavy test tails; anchor conditioning modestly improves the mean but not the extremes. Current results point to future work on tail‑focused training and domain‑robustness.

![][image63]  
**Table 3\.** Validation/Test accuracy of the transducer guidance module

**LVEF Evaluation**

Automated LVEF predictions were compared against expert-estimated LVEF predictions. The average LVEF predicted by EchoNet across 10 subjects was 50.7% ± 4.8%. The average ground truth LVEF estimated by the anesthesiologist was 59.5% ± 6.0%. When running real-time landmark detection and pose scoring with the ResNet18 backbone, an average of ***14 frames per second*** was achieved with 36 GB of system memory on an Apple M3 Pro chip.

# **TRL 3.4: Risk Areas identified in general terms**

# **Accuracy of the transducer IMU:** IMU drifting may impact the reliability and accuracy of the prediction of the probe guidance signal, especially for probe translations and during long-duration scanning. 

**System latency:** Latency in the display of the current annotated ultrasound image and guidance signal could negatively impact performance, particularly if network predictions significantly lower frame rate with respect to the input video stream.

**Bias in LVEF estimation**: The LVEF estimator is trained with the publicly available EchoNet Dynamic dataset, which has a large proportion of patients with normal cardiac function. This could potentially bias LVEF estimations to favor “normal” LVEF results.

**TRL 3.5: Risk mitigation strategies identified**

# **Accuracy of the transducer IMU:** Since translational drifting seems to be more frequent and pronounced than rotational drifting, we can separate the guidance signal into translation and rotation components and emphasize rotational guidance. Secondly, the transducer pose scoring module is intended to mitigate the risk of IMU drifting as well. The “traffic light” feedback is based on the images and anatomical landmarks alone (rather than IMU information), so it provides an independent form of feedback that indicates the “closeness” of the transducer pose to the desired orientation. Third, we can leverage the camera system to periodically recalibrate the transducer pose estimation. Finally, we are investigating an exploratory approach that trains the guidance network with synthetic TTE data generated from cardiac computed tomography (CT) images that are sliced at known planes with respect to the optimal A4CH plane. This would provide a TTE data source associated with high-confidence estimates of transducer pose that are IMU-independent.

**System latency:** We can investigate performance tuning options and consider alternative network architectures or programming languages to improve algorithm efficiency. We can explore the option of a license with the transducer vendor to directly stream data to our processing device in order to reduce the network communication and extra processing time associated with connecting through a mobile device (the tablet or smartphone).

**Bias in LVEF estimation**: We can carry out validation of LVEF estimation in retrospective TTE data that have expert LVEF assessment (manually evaluated ground truth). We will likewise incorporate patients with cardiac pathology in our prospective TTE studies with the point-of-care imaging platform. If we find a bias in the automated LVEF estimator, we can increase data from patients with low LVEF in the training population.

# **TRL 3.6: Inspection of Test Data**

You may find test data and a brief description here: https://upenn.box.com/s/gxi64jcbn8wlughsj4x8lmfae2upgnwt

# **TRL 3.8: Data collection and processing conducted to support component**

**Data Collection**  
The TTE acquisition protocol (Figure 1B, described in the Transducer Pose Scoring Module) was carried out on nine individuals, three male and six female, using the Clarius PAL HD3 Wireless Ultrasound Scanner (Clarius Mobile Health) and the Lumify handheld ultrasound imaging system (Philips North America). We collected 88,524 images with 430 sweeps. All images were manually scored according to the criteria described in Table 1\. In total, there were 36,238 green frames, 28,017 yellow frames, and 24,269 red frames. The ground truth LVEF of each subject was calculated as the mean LVEF visually assessed by a cardiac anesthesiologist.  
As described above, the landmark detection module leverages the publicly available EchoNet Dynamic dataset \[D. Ouyang et al., 2020\], which comprises 10,000 video clips, each with manual annotations on one diastolic and one systolic frame, totaling 20,000 annotated frames. 

**Processing**  
Please refer to the above description of the multi-task network components and their training. Verification experiments are explained in “Performance Metrics for System Components”. Figure 4 provides a screen capture of TTE fine-tuned guidance.

![][image64]  
**Figure 4\.** Screen capture of fine-tuned guidance for A4CH view acquisition with TTE. In each row, the left-side panel shows the current ultrasound image annotated with anatomical landmarks of the four cardiac chambers. Target anatomy in the A4CH view  is displayed in the upper left-hand corner for visual reference. The colored border of the annotated image indicates if the current image is on target (green), close to target (yellow), or off target (red) with respect to the desired A4CH view. The right-side panel illustrates the guidance signal. The opaque axes indicate the current transducer pose, and the transparent axes indicate the recommended transducer pose. **A.** An example where the current image captures part of the left ventricle, but is off-target with respect to an ideal A4CH view. **B.** An example where the current image captures a good-quality A4CH view with all chambers adequately visualized. The automated LVEF estimator reports the LVEF from this clip.

**References**

K. He, X. Zhang, S. Ren, and J. Sun, “Deep Residual Learning for Image Recognition,” en, in 2016 IEEE Conference on Computer Vision and Pattern Recognition (CVPR), Las Vegas, NV, USA: IEEE, Jun. 2016, pp. 770–778.

A. Kumar et al., LUVLi Face Alignment: Estimating Landmarks’ Location, Uncertainty, and Visibility Likelihood, en, arXiv:2004.02980 \[cs\], Apr. 2020\.

J. McCouat and I. Voiculescu, “Contour-Hugging Heatmaps for Landmark Detection,” en, in 2022 IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), New Orleans, LA, USA: IEEE, Jun. 2022, pp. 20 565–20 573\.

D. Ouyang et al., “Video-based AI for beat-to-beat assessment of cardiac function,” en, Nature, vol. 580, no. 7802, pp. 252–256, Apr. 2020, Publisher: Nature Publishing Group.

O. Siméoni, et al.,“DINOv3,” arXiv preprint arXiv:2508.10104, 2025\.

H. Touvron et al., “Llama 2: Open Foundation and Fine-Tuned Chat Models,” ArXiv, Jul. 2023\.

D. Tran, H. Wang, L. Torresani, J. Ray, Y. LeCun, and M. Paluri, A Closer Look at Spatiotemporal Convolutions for Action Recognition, arXiv:1711.11248 \[cs\], Apr. 2018\.

R. Zhang et al., “LLaMA-Adapter: Efficient Fine-tuning of Language Models with Zero-init Attention,” 2023, Publisher: arXiv Version Number: 3\.

Y. Zhou, C. Barnes, J. Lu, J. Yang, and H. Li, “On the continuity of rotation representations in neural networks,” in Proc. IEEE/CVF Conf. Comput. Vis. Pattern Recognit. (CVPR), 2019, pp. 5738–5746, doi: 10.1109/CVPR.2019.00589.

# STEVENS \- Patient Pose

STEVENS \- Patient 3D Pose

**TRL 3.2: Documents indicate that system components ought to work together**

**What the task is？**

The task of my module is to reconstruct a patient-specific human body from monocular RGB image frames extracted from a video. Given these frames, the module estimates a full 3D body mesh together with the 2D and 3D coordinates of all body joints, and then attaches a texture to the surface of the patient’s mesh to obtain a realistic textured 3D model.

**What are the input and outputs data and formats？**

Input data and formats：

The input to the body-mesh, joint-estimation tasks is:

1. Monocular RGB image frames extracted from a top-view Intel RealSense video recorded in the ROS system. The frames are read from ROS bag topics and stored as standard image files (e.g., .jpg / .png).  
2. Intrinsic of the camera(input from the user)

The input to texturing task is:

1. RGB images from the GoPro global-view camera(e.g., .jpg / .png)

Output data and formats

For the body-mesh and joint-estimation task:

1. 3D human body mesh  
   Format: .obj file.  
2. SMPL parameters  
   Contents: global orientation, body pose, and beta shape parameters.  
   Format: .json file.  
3. 2D and 3D joint coordinates  
   Contents: pixel-space 2D joint locations and camera-space 3D joint locations.  
   Format: Python lists.  
4. Camera parameters  
   Contents: camera\_translation, focal\_length, and camera intrinsics.  
   Format: stored in a JSON file or returned as a Python dictionary.

For the texture attachment task:

1. UV texture image  
   Format: JPEG byte stream.  
2. Texture shape information  
   Contents: \[height, width, channels\].  
   Format: Python list.  
3. Bounding-box information used by the texturing module  
   Format: nested Python dictionary.

**What are the dependencies?**

We build our module for body-mesh and joint-estimation tasks on CameraHMR implemented in PyTorch 2.3.1 with CUDA 12.1, using SMPL-X for parametric body modeling and PyTorch Lightning for training. Person regions are obtained either from Detectron2 / YOLO11 or from a fixed pre-defined bounding box when the camera is static. For feature extraction we adopt ViT backbones from timm, and use trimesh and pyrender for 3D mesh processing and rendering.

For texture generation, we employ TexDreamer , a zero-shot 3D human texture model built on Stable Diffusion . Our implementation uses the diffusers library (v0.18.2) with LoRA adapters via peft for efficient fine-tuning, relies on CLIP vision encoders from transformers for image understanding, and uses accelerate for optimized inference. Person crops used for texture generation are obtained either from the same person detectors as above or from a fixed pre-defined bounding box when the camera is static.

**What are the related ROS modules (consumers and producers)？**

For the body-mesh and joint-estimation task:

Producer：

1. ROS node / bag topic that publishes RGB images from the Intel RealSense camera.

Consumers: 

1. CSU system for the lower-limb analysis task.  
2. UMich system for the cardiac’s probe-detection task.  
3. Stevens system for the texture-attachment and environment-reconstruction task.

For the texture-attachment task:

Producer:

1. ROS node / bag topic that publishes RGB images from the Intel GoPro camera.

Consumers:

1. CSU system’s lower-limb task.  
2. Stevens system for the texture-rendering task.

**TRL 3.3: Performance metrics for system components are established**

**What is the current performance/latency?**

For the body-mesh and joint-estimation task:

CameraHMR with Detectron2 as the bounding-box detector runs at \~1.4 FPS, i.e., about 0.71 s per frame for SMPL parameter inference.

CameraHMR with YOLO11m as the bounding-box detector runs at \~9 FPS, i.e., about 0.11 s per frame.

CameraHMR with a fixed bounding box (no detector) runs at \~10.5 FPS, i.e., about 0.10 s per frame.

For the texture-attachment task:

The TexDreamer model runs at approximately 0.33 FPS, corresponding to about 3.0 s per frame for texture generation.

**How do we evaluate offline correctness during development/debugging?**

For the body-mesh and joint-estimation task:

During development, we evaluate offline correctness by qualitatively inspecting the rendered results. For each recorded frame, we render the predicted 3D human mesh from the camera viewpoint and overlay it on the original RGB image, and then check whether the mesh matches the person in the image in terms of body pose and spatial position.

For the texture-attachment task:

For the texture-attachment task, we evaluate offline correctness by visually inspecting the textured meshes. We apply the UV texture maps generated by the model to the reconstructed human mesh and compare the rendered results with the original RGB frames, checking whether the overall appearance (e.g., clothing, skin tone, and major visual patterns) closely matches the patient in the image.

**How do we evaluate online correctness during deployment?**

During deployment, we evaluate online correctness by monitoring the rendered results in real time. We overlay the predicted 3D mesh on the live video frames and visually check whether the body shape and pose are consistent with the person in the scene. For the texture-attachment module, we further verify that the generated texture on the mesh resembles the patient’s real appearance, including clothing patterns and overall surface details.

**TRL 3.4: Risk Areas identified in general terms.**

**What are the failures mode we have encountered and that we consider to potential problems in the future?**

For the body-mesh and joint-estimation task, we have observed the following failure modes and potential issues:

1. CLIFF model limitations. When using the CLIFF model for 3D human mesh recovery, the predicted body mesh and pose often do not match the person in the input image, especially under our top-view clinical camera setup. In addition, the inference speed of CLIFF on our hardware is very low (sub-real-time), so it cannot satisfy the online processing requirement of the system.

2. CameraHMR latency bottleneck. Although CameraHMR provides more stable predictions, the SMPL parameter regression in CameraHMR is still computationally expensive. The current inference speed of the model is not sufficient to meet the real-time requirements of the overall system, and it may become a latency bottleneck when integrated with other modules.

**TRL 3.5: Risk mitigation strategies identified.**

**What have been (or will be) our solutions to these (potential) failure modes?**

For the body-mesh and joint-estimation task, our current and planned solutions are as follows:

1. Replacing CLIFF with CameraHMR. To address the poor accuracy of the CLIFF model, we have switched to the CameraHMR model for 3D human mesh recovery, which provides more accurate meshes and poses under our clinical camera setup.

2. Mitigating the latency bottleneck in CameraHMR. By profiling the pipeline, we found that the main bottleneck comes from the bounding-box detection stage rather than from SMPL regression itself. To reduce this overhead, we adopt two strategies:  
   (a) replacing Detectron2 with a faster YOLO11m detector, and  
   (b) exploiting our fixed camera and treatment-bed setup to use a configurable fixed bounding box for the patient, which completely removes the per-frame detector cost when the scene is static.

**TRL 3.6: Inspection of Test Data**

**Example data for all metrics in TRL 3.3**

Dataset:

1. The RealSense camera dataset consists of 690 RGB images in PNG format, each with a resolution of 640 × 480 pixels and 8-bit per channel color depth.  
2. The GoPro camera dataset consists of a single RGB video with a resolution of 1920 × 1080 (Full HD) at 29.95 FPS (30000/1001), containing 1,986 frames with a total duration of approximately 66.3 seconds and a file size of 48 MB.

For the body-mesh and joint-estimation task：

**Table1: Inference speed of CameraHMR**

| Model | Speed |
| :---: | :---: |
| CameraHMR \+ Detectron2 | 1.4 FPS |
| CameraHMR \+ YOLO11m | 9 FPS |
| CameraHMR \+ fixed bbox | 10.5 FPS |

**Image1: Visualization results of CameraHMR**

| ![][image65] | ![][image66] |
| :---: | :---: |

For the texture-attachment task:

**Table2: Inference speed of TexDreamer**

| Model | Speed |
| :---: | :---: |
| TexDreamer | 0.33 FPS |

**Image2: Visualization results of TexDreamer**

![][image67]

**TRL 3.8: Data collection and processing conducted to support component**

Data collection. The data used for the experiments in TRL 3.6 are collected by the CSU team. An Intel RealSense camera mounted above the treatment bed provides a top-view of the patient, and a GoPro camera records a global view of the room. Both RGB streams are recorded into ROS bag files.

Data processing. During our experiments, we replay these ROS bags and subscribe to the RGB image topics from the two cameras. The frames are then passed through the Stevens System API to the body-pose estimation module, which predicts the 3D human mesh, the associated SMPL(-X) parameters, and the texture. The resulting meshes, parameters, and textures are republished as ROS topics in real time so that other nodes in the system can subscribe to and consume these processed results.

**• • Describe data collection process of content described/presented in TRL 3.6:** 

# STEVENS \- Patient Rendering

**STEVENS- Patient Rendering** 

The Rendering Component enables real-time visualization of the patient as a 3D mesh with support for programmatically displaying mesh annotations to facilitate guidance. The component is provided as a standalone pure Python library with Offscreen rendering.

![A person sitting on a couchAI-generated content may be incorrect.][image68]![][image69]

**Figure 1\.** Patient 3D mesh visualization with “default” texture.

The SMPL (Skinned Multi-Person Linear Model) representation is used for the 3D model of the patient. The SMPL model consists of a set of 3D vertices and a set of faces. The faces correspond to the triangles of the mesh, as indices of triplets of vertices, and are known a priori, while the vertices are decoded from the SMPL parameters provided by the Pose Estimation ROS Node (either CLIFF or CameraHMR) for the video frames captured by the color camera (i.e., vertices may change across frames as the patient pose changes).

Once the SMPL vertices have been decoded, the Rendering Component takes the vertices, faces, and a 2D image texture to render the textured 3D mesh of the patient (Figure 1). The 2D image texture can be read from a file (as in Figure 1\) or acquired from the Texture Generation ROS Node. Support for viewpoint control is implemented so the rendered mesh does not necessarily have to match the camera image. We summarize the technical details pertaining to the inputs in Table 1 and Python package dependencies in Table 2\. The output of the Rendering Component is a single 2D image array in uint8 RGBA format.

The Rendering Component provides functionality for real-time mesh annotations produced by either drawing on the mesh texture or adding simple 3D mesh indicators. For example, in Figure 2 the probe is displayed as a 3D sphere alongside the patient mesh, in a common coordinate system, while the nearest point to the probe is highlighted with a red circle drawn over the mesh texture. The probe position is provided by a ROS node. Another example is shown in Figure 3\. The Rendering Component provides a segmentation of the SMPL model which is used to color the model leg based on estimated task progress provided by the Progress Tracker ROS node.

**Table 1\.** Rendering Component inputs.

| Inputs | Sources | Format | Shapes | Notes |
| :---- | :---- | :---- | :---- | :---- |
| SMPL Parameters | Pose Estimation ROS Node | float32 | \[1, 1, 3, 3\] \[1, 23, 3, 3\] \[1, 10\] \[1, 3\] | Consists of 4 arrays |
| 2D Texture Image | Image File Texture Generation ROS Node | uint8 RGBA | \[height, width, 4\] | Should be square and Power of Two |
| SMPL Model Files | SMPL\_NEUTRAL.pkl | Binary | \- | From SMPL website |
| SMPL Texture UV Map | smpl\_uv.obj | OBJ Mesh File | \- | From SMPL website |

**Table 2\.** Rendering Component Python dependencies. Python version is 3.10.

| Package | Tested Version |
| :---- | :---- |
| numpy | 1.26.4 |
| opencv-python | 4.12.0.88 |
| torch | 2.7.1+cu118 |
| pyrender | 0.1.45 |
| trimesh | 4.8.3 |
| smplx | 0.1.28 |
| roma | 1.5.4 |
| embreex | 2.17.7.post6 |
| rtree | 1.4.1 |
| chumpy | 0.70 |

![A person in a grey shirtAI-generated content may be incorrect.][image70]![A person lying on a couchAI-generated content may be incorrect.][image71]

**Figure 2\.** Patient 3D mesh and probe (sphere) rendering with nearest point highlight (red).

![A person wearing black pantsAI-generated content may be incorrect.][image72]![A person wearing black and green pantsAI-generated content may be incorrect.][image73]![A person wearing a green and black leggingsAI-generated content may be incorrect.][image74]

**Figure 3\.** Leg coloring based on task progress. From left to right: 0%, 50%, 100%.

The supported annotation features are:

1. SMPL mesh alignment to external objects (e.g., probe). Requires camera intrinsics.  
2. SMPL mesh segmentation.  
3. Viewpoint control with solver for focusing individual SMPL regions.  
4. Piece-wise (limbs, head, and torso) parameterization of the SMPL model using cylindrical coordinates.  
5. Get mesh closest point to given coordinates.  
6. Get mesh raycasting given ray origin and direction.  
7. Brush painting (solid or two-color gradient) and Text painting (Figure 4\) from either cylindrical coordinates or mesh closest/raycast. Supports erasing and multiple layers.

![A person waving in a poseAI-generated content may be incorrect.][image75]

**Figure 4\.** Text painting from cylindrical coordinates (cone) and two-color gradient painting from ray casting (camera principal axis).

The Rendering Component has been integrated into the Lower Limb demo running at \~8 FPS, using mainly annotation features 1, 2, 3, 5, and 7 as shown in Figure 2 and Figure 3, and running alongside other components. When running independently, the Rendering Component can achieve the framerates shown in Table 3 on a Laptop with a 13th Gen Intel(R) Core(TM) i9-13980HX 2.20 GHz CPU, 128 GB RAM, and NVIDIA RTX 5000 Ada Generation Laptop GPU (different machine than the one used for the demo), using a rendering resolution of 1280x720 and a texture size of 1024x1024.

**Table 3\.** Rendering Component FPS for various annotation use cases.

| Rendering Features Used | FPS |
| :---- | :---- |
| Textured Patient Mesh Only (baseline) | 22 |
| Textured Patient Mesh \+ Probe \+ Nearest Painting | 10 |
| Textured Patient Mesh \+ Leg Painting | 12 |
| Textured Patient Mesh \+ Cylindrical Text \+ Raycast Painting | 14 |

SMPL and probe alignment are validated by reprojecting the SMPL mesh vertices and the probe position into the original color image. Given that the probe has non-negligible height, an approximate heuristic adjustment of the probe position is performed by the rendering code, so the indicator is closer to the body.

Failure modes encountered during the development of the Rendering component are summarized in Table 4 while potential failure modes are summarized in Table 5\.

**Table 4\.** Addressed failure modes of the Rendering Component.

| Problem | Proposed Solutions |
| :---- | :---- |
| Variability of SMPL parameter estimation over time results in jittery patient model rendering | Exponential filtering of SMPL parameters (smoothing) on the rendering side |
| Incorrect detection of patient pose (e.g., sometimes detection switches to caregiver) | Filter detections by bounding box (patient is in the center of the image) Filter detections that are not facing the camera (patient always faces the camera, see Figure 1 and Figure 2\) |
| Patient body joints not observed in the color image tend to not have the correct orientation in the SMPL parameters (e.g., arms are not observed in Figure 2 so arms of rendered model are not right and jitter across frames) | Implemented functionality to hide unobserved limbs from the 3D mesh using SMPL segmentation Support for overwriting orientation of unobserved joints in SMPL parameters to a fixed value (e.g., identity) so unobserved limbs stay fixed |

**Table 5\.** Possible failure modes of the Rendering Component.

| Problem | Proposed Solutions |
| :---- | :---- |
| Using many painted annotations (brush/text) may result in slowdown since texture painting is done on CPU | Consider a GPU shader or CUDA implementation of internal texture painter |
| Filtering of SMPL parameters may introduce unacceptable lag | Implement a double exponential filter for SMPL parameters to enable extrapolation and reduce perceived lag |
| Since decoded SMPL vertices can change across frames, the patient mesh must be recreated every time, which may introduce unnecessary CPU usage for the current mesh library we are using since it always recomputes intermediate values | Consider implementing a lightweight mesh library for the Rendering Component, since it only uses a small subset of functions of the current mesh library, where constant intermediate value are computed only once |

The Rendering Component was tested and profiled (Table 3\) using two sequences of data provided by CSU. The data sequences contain RealSense RGB images, SMPL parameters, probe position, and task progress. The first sequence contains 1644 samples, and the second sequence contains 914 samples. Figure 1 shows an image from the first sequence and Figure 2 shows an image from the second sequence.

# STEVENS \- 3D Environment

**STEVENS \- 3D Environment**

**TRL 3.2: Documents indicate that system components ought to work together**

***What the task is***

This module provides 3D information about the scene, including camera parameters, 3D scene geometry, and 3D scene texture.

***What are the input and output data and formats***

Input: 

* RGBD images from the RealSense camera, in *FrameMessage* format  
* RGB images from the GoPro camera, in *FrameMessage* format

Output:

* Extrinsic and intrinsic parameters of the RealSense and GoPro cameras  
* Textured point clouds predicted by VGGT  
* Textured point clouds reconstructed from RealSense depth maps

All outputs are serialized in JSON with the following structure:

{"stamp": stamp\_time,

"extrinsic": list,

"intrinsic": list,

"point\_cloud": list,

"point\_cloud\_rgb": list,

"point\_cloud\_from\_depth": list,

"point\_cloud\_from\_depth\_rgb": list}

***What are the dependencies***

All dependencies can be installed by following the official [VGGT](https://github.com/facebookresearch/vggt) installation instructions.

***What are the related ROS modules (consumers and producers)***

* Producers: Data streams from CSU  
* Consumers: Downstream modules that require 3D scene geometry 

**TRL 3.3: Performance metrics for system components are established**

***What is the current performance/latency***

VGGT is able to generate a dense and accurate point cloud of the scene, as illustrated in the figure below. The end-to-end reconstruction for a single scene currently takes approximately **3 seconds**.

![][image76]

***How do we evaluate offline correctness during development/debugging***

During development, correctness is evaluated **qualitatively** by visually inspecting the reconstructed point clouds of the scene (e.g., checking completeness, alignment, and obvious artifacts).

***How do we evaluate online correctness during deployment***

During deployment, online correctness will be assessed **indirectly via downstream modules**. In particular, feedback from downstream tasks that consume the 3D geometry will be used to indicate whether the reconstructed 3D scene is sufficiently accurate for the application.

**TRL 3.4: Risk Areas identified in general terms.**

***What are the failure modes we have encountered and that we consider potential problems in the future***

* Current issue: Scale misalignment between the SMPL human body mesh produced by CameraHMR and the 3D scene reconstructed by VGGT.  
* Potential future issue: Temporarily inconsistent inputs between the GoPro and RealSense cameras.


**TRL 3.5: Risk mitigation strategies identified.**

***What have been (or will be) our solutions to these (potential) failure modes***

* Scale misalignment: Align the SMPL mesh and VGGT reconstruction via their depth maps to enforce consistent scale.  
  ![][image77]  
* Camera inconsistency: More accurate timestamps should be provided from the capture system to improve temporal synchronization between GoPro and RealSense streams.

![][image78]                  ![][image79]

![][image80] 

![][image81]![][image82]

![][image83]

![][image84]![][image85]

![][image86]

**TRL 3.6: Inspection of Test Data**

***Example data***

The data is from the CSU data capture system, containing 11 video sequences in total. Each sequence consists of:

* One RGB image from the RealSense camera

* One RGB image from the GoPro camera

* One depth map from the RealSense depth sensor

An example of such input triplets is shown in the figure below.

![][image87]

TRL 3.8: Data collection and processing conducted to support component

The data is collected and published as separate streams by CSU. To integrate these streams, we use the *TemporalStreamAligner* developed by the BBN group to select image (and depth) pairs with closely matched timestamps, enabling synchronized multi-sensor input for the module.

# Michigan / Medical VQA

# Michigan / Medical VQA

# **TRL 3.2: Documents indicate that system components ought to work together**

The Medical VQA module is implemented as a ROS node within the VIGIL system. It receives an ultrasound frame and a natural-language question from the VIGIL Agent and returns a textual answer. The component does not control any guidance logic; its sole function is to generate VQA responses based on the incoming image-question pair.

Medical VQA operates as a standalone inference node and communicates only with the VIGIL Agent. All upstream inputs to the system, as well as all downstream distribution of the VQA answer, are handled by the Agent. The Medical VQA node subscribes to the request topic used by the Agent and publishes its responses on a separate topic that the Agent subscribes to.

**Input (Agent → Medical VQA):**  
 The Agent sends a ROS message that contains (1) an ultrasound frame and (2) a question string. The Agent constructs this message by combining the frame from the ultrasound node with a question spoken by the generalist via ASR.

**Output (Medical VQA → Agent):**  
 Medical VQA publishes a ROS message containing the answer text. The Agent subscribes to this response topic and receives the VQA output from it.

**Downstream Use:**  
 The Agent forwards the VQA answer to exactly two components:

1. **TTS node**, which speaks the answer to the generalist, and  
2. **Display/UI node**, which renders the answer on screen.

These are the only consumers of Medical VQA output. All communication occurs through the Agent, and Medical VQA does not interact directly with any other component.

# **TRL 3.3: Performance metrics for system components are established**

The Medical VQA model is evaluated on a held-out test set of 1k ultrasound images from a different subject, ensuring subject-level separation and preventing train-test leakage. On this set, the model achieves an Exact Match (EM) of 48.18, F1 of 44.95, Precision of 50.03, Recall of 42.86, and BLEU of 19.60. These text-similarity scores are affected by our deliberate use of paraphrased questions and answers during training: because the model outputs clinically correct but lexically different explanations, exact-string and n-gram metrics underestimate semantic correctness. For structured binary decisions which are most of the questions—questions about chamber visibility, EF measurability, overall image quality, and related yes/no assessments—the model achieves a Yes/No accuracy of 90.59%, computed by comparing only the predicted binary decision (“Yes” or “No”) to ground truth and not the accompanying explanation text, whose phrasing can vary significantly across valid answers. 

This gap illustrates how lexical metrics can misrepresent actual clinical correctness: for example, the following pair would score poorly under our metrics despite being semantically equivalent.

**Ground truth:** *“Yes, the visualization of the left ventricle allows for a reliable ejection fraction measurement. The endocardial borders are well-defined.”*

**Model answer:** *“Yes, the image quality is adequate for measuring the ejection fraction. It's important to ensure the probe is held steady throughout the cardiac cycle for accurate measurements.”*

Both statements convey the same clinical judgment (“EF can be assessed”), but differ lexically, lowering metrics while still being correct in practice.

The metrics are defined below:  
![][image88]

**Exact Match (EM):**  
 Measures the proportion of overlapping tokens between the model answer and the reference answer, normalized by the length of the model output. Higher EM means the model used more of the same words as the reference.

**Precision:**  
 Of the tokens the model produced, the fraction that also appear in the reference answer.

**Recall:**  
 Of the tokens in the reference answer, the fraction that the model correctly produced.

**F1 Score:**  
 The harmonic mean of precision and recall, capturing how well the model balances including the right tokens without adding irrelevant ones.

**BLEU:**  
 An n-gram–based similarity score comparing the model’s text to the reference, penalizing mismatched wording and short outputs.

**Yes/No Accuracy:**  
 For binary clinical questions, evaluates only whether the model correctly predicted “Yes” or “No,” ignoring the explanation text.

# **TRL 3.4: Risk Areas identified in general terms**

# **1\. Language-Prior / Shortcut Learning Risk**

VQA and VLM literature documents that models often learn shortcut correlations between question templates and answers (“language priors”) rather than using the image. Without sufficient linguistic variation, the model may minimize loss by memorizing high-frequency text patterns instead of performing visual grounding.

**2\. Latency** 

Real-time use imposes strict latency requirements. 

**3\. Irrelevant or Misrouted Questions**

If the Agent forwards speech-to-text output that is not an ultrasound-related query, the model may produce nonsensical or clinically risky answers.

**4\. Lack of Expert Validation for Clarius QA Generation**

GPT-4o-generated QAs for the \~10k Clarius samples have not yet been reviewed by experts, increasing the risk of incorrect or unsafe training pairs.

**TRL 3.5: Risk mitigation strategies identified**

**1\. Mitigating Language-Bias / Shortcut Learning**

GPT-4o is prompted to generate diverse paraphrases of questions and answers. This increases visual dependence and reduces shortcut learning. Few-shot prompting further prevents the model from minimizing loss by memorizing fixed text templates.

**2\. Meeting Latency Constraints**

We use the smaller 7B Mistral model to maintain near real-time inference.

**3\. Preventing Irrelevant Query Routing**

The Agent performs tool selection: only questions containing ultrasound-related terms are routed to the Medical VQA node. Non-ultrasound questions never reach the model.

**4\. Mitigating Lack of Expert Validation for Clarius QA Pairs**

Although the \~10k Clarius QA pairs have not yet been expert-validated, the large dataset size reduces the impact of occasional GPT-4o errors, since individual incorrect pairs contribute only minimally to the training signal.

# **TRL 3.6: Inspection of Test Data**

You can find test data (images and question-answer pairs) here:

[https://www.dropbox.com/scl/fo/gcqxj17vyp0my87v0jgim/ADkBfAt65LJ43T-FoVkeh6E?rlkey=kl1g9ofcc5lt26cnjokgjosth\&st=moavwb2c\&dl=0](https://www.dropbox.com/scl/fo/gcqxj17vyp0my87v0jgim/ADkBfAt65LJ43T-FoVkeh6E?rlkey=kl1g9ofcc5lt26cnjokgjosth&st=moavwb2c&dl=0)

# **TRL 3.8: Data collection and processing conducted to support component**

The Medical VQA component is trained on a combined dataset of ultrasound images and question–answer pairs created specifically for cardiac ultrasound VQA. The dataset consists of two primary sources:

**1\. Public Ultrasound Datasets \+ Lumify Scans (\~5,000 samples)**

This portion includes publicly available cardiac ultrasound frames together with UPenn-collected Lumify scans.  
 These images were manually filtered and categorized by experts into view/quality groups such as:

* High-quality A2C  
* High-quality A4C  
* Low-quality A4C  
* Missing atria  
* Missing RV  
* Missing lateral wall  
* Artifact  
* Lung artifact

![][image89]

Figure 1\. Dataset categories

This set formed the foundation for establishing initial view categories, defining QA templates, and constructing early expert-written and GPT-4o-expanded question–answer pairs.

**2\. Penn Clarius Dataset (\~10,000 samples)**

These are newly collected and annotated ultrasound frames acquired using Clarius handheld devices.  
 Unlike the public/Lumify data, the Clarius images are classified by the UPenn team into three categories:

* **Green** (good quality)

* **Yellow** (moderate quality / usable)

* **Red** (poor / non-diagnostic)

These green/yellow/red labels are used as conditioning signals for generating VQA pairs.

**Data Processing Pipeline**

Both datasets flow through the same four-stage QA-generation process, with the only difference being the category labels:

**1\. Categorization**

* Public/Lumify (\~5k): manually categorized by experts into detailed anatomical/quality categories (as shown in the screenshot).

* Clarius (\~10k): manually labeled as green/yellow/red by the UPenn team.

These labels are attached to each image and used during GPT-4o conditioning.

**2\. Category-Conditioned QA Generation (Expert \+ GPT-4o)**

For each category (anatomical categories or green/yellow/red), GPT-4o is instructed using:

* The assigned label

* A small set of few-shot example QAs written by experts

* Constraints on EF-related questions and image-quality statements

GPT-4o then generates multiple QA variations for each image, ensuring linguistic diversity while staying aligned with the assigned category.

**3\. Fine-Grained QA Generation (GPT-4o, image-conditioned)**

A second GPT-4o pass uses:

* The image itself

* Its category label

* A few representative QAs from the category

to produce fine-grained QAs tied to specific features of the image (e.g., missing chamber, visible artifact, EF suitability, structure visibility).  
 This step yields more clinically specific image–question–answer triples.

**4\. Expert Validation (Partial)**

Validation status is as follows:

* Public/Lumify (\~5k):  
   Only a portion of the GPT-4o-generated QA pairs from this set has been reviewed and validated by experts.  
* Clarius (\~10k):  
   No expert validation has been performed yet.  
   The QAs are currently based solely on GPT-4o generation conditioned on the green/yellow/red labels and few-shot examples.

![][image90]

Figure 2\. Dataset processing pipeline

# Michigan / VIGIL Agent

# Michigan / VIGIL Agent

# **TRL 3.2: Documents indicate that system components ought to work together**

The VIGIL Agent is the central coordinator for real-time multimodal perception and action selection. It receives ultrasound frames, speech-to-text transcriptions, and task-state messages, and routes these signals to downstream components including the Medical VQA module, the TTS system, and the display subsystem. The Agent also manages state transitions for task progression. The Agent runs as a ROS 2 node (MedicalAgentNode) and integrates tightly with the Python-based MedicalAgentAPI, which manages queueing, prompt handling, frame dispatching, and tool selection.

**1\. Input Modalities and Component Integration**

Image Stream (Ultrasound Frames)  
The VIGIL Agent subscribes to FrameMessage messages on the defined IMAGE\_TOPIC. Every frame is decompressed and then enqueued into the MedicalAgentAPI using api.enqueue\_frame(...). These frames serve as the visual grounding for both VQA and downstream guidance generation.

Speech-to-Text Stream (ASR Output)  
The Agent subscribes to processed speech messages from the ASR module on ASR\_TOPIC. Each transcription is inserted into the API’s prompt queue with api.enqueue\_prompt(text). This provides the linguistic grounding needed for both natural language interaction and VQA-question formation.

System State Stream (Task State)  
The Agent subscribes to StateMessage updates on STATE\_TOPIC. This keeps the Agent’s local current\_state variable in sync with the global State Node. This state determines routing logic, whether to generate a VQA query, provide autonomous guidance, or return general responses.

**2\. Outgoing Communication**

The Agent publishes four types of outputs, each routed to different subsystems:

Question Publication (to Medical VQA)  
The Agent publishes VQAMessage instances through the QUESTION\_TOPIC. These include the question text, full-resolution frame bytes, shape metadata (height, width, channels), and placeholders for downstream VQA results.

VLM State Broadcasting  
Two ROS publishers drive the display and UI:  
• /vlm/state \= "thinking" is emitted during inference periods.  
• /vlm/state \= "inactive" indicates that no large-model computation is running.  
These cues prevent user confusion and help synchronize UI responsiveness.

Agent Response Stream (General Text Output)  
Textual responses are published as std\_msgs/String on the RESPONSE\_TOPIC. The TTS module and display system subscribe to this topic.

Agent-Initiated State Changes  
The Agent can initiate task transitions by publishing AgentStateMessage on AGENT\_STATE\_TOPIC. This is triggered when the API identifies verbal commands mapped to task-level actions.

**3\. Threading, Execution, and Real-Time Coordination**

The Agent runs using a SingleThreadedExecutor, while the API runs in a separate Python thread. This separation avoids deadlocks during high-frequency frame ingestion and ensures non-blocking prompt dispatching. The architecture uses bounded queues inside the API to maintain real-time behavior and avoid memory growth during heavy data inflow.

# **TRL 3.4: Risk Areas identified in general terms**

**ASR Noise Sensitivity and Misrouting**  
If the ASR system generates erroneous text or hallucinations, the Agent could send malformed or irrelevant VQA queries, leading to confusing guidance.

**State Desynchronization Risk**  
The Agent mirrors the StateNode’s task\_id locally. Delayed or dropped state messages may cause the Agent to route questions or responses based on outdated state.

# **TRL 3.5: Risk mitigation strategies identified**

# **ASR Filtering and Tool Selection**  The API enforces domain-specific filtering. Only transcriptions containing ultrasound-related vocabulary or question patterns are routed to VQA.

# **State Synchronization Checks**  The Agent includes guard conditions that verify whether a requested operation is consistent with the current state. We plan to implement watchdog timers for stale state messages.

# **TRL 3.8: Data collection and processing conducted to support component**

**1\. High-Rate Frame Streams**

A library of 12 ultrasound video clips (A4C, A2C, PLAX, subcostal) were collected and converted into FrameMessage sequences using a custom ROS publisher. Each clip was replayed at multiple frame rates to stress-test the system.

Processing:  
• Converted frames were JPEG-encoded.  
• Byte arrays were verified to decode correctly using the same codepath as the Agent.  
• Frame timestamps were aligned to ground-truth video time.

**2\. ASR Transcript Dataset**

This dataset was sourced from the Rochester transcription evaluation. It included:  
• Clean speech,  
• Accent-varied speech,  
• Whisper hallucination examples,  
• Keyword-invocation phrases such as “Hey VIGIL”, “Start VIGIL”, “Stop VIGIL”.

Processing:  
Each ASR message was transformed into a ProcessedAudio ROS message. This allowed the Agent to be tested using real ASR outputs without needing the ASR system to run concurrently.

**3\. State Transition Scripts**

A series of synthetic scripts were created to stress-test rapid state changes, including:  
• Rapid oscillation between task states,  
• Commands embedded inside ASR text,  
• Conflicting instructions (e.g. VQA requests during a non-VQA state).

Processing:  
All state messages were published sequentially with timestamps and verified for deterministic playback.

# Michigan / State

# Michigan / State

Flesh each subsection out (with text, results tables, figures, images, etc) to address various documentation needs.

# **TRL 3.2: Documents indicate that system components ought to work together**

The State Feature (State Management Subsystem) is designed as a modular ROS2 node (StateNode) that orchestrates a multi-step clinical workflow by integrating:

1. State specifications (loaded through StateSpec)  
2. External operator inputs (keyboard/console messages via /vigil\_c2/state)  
3. Autonomous agent-driven triggers (AgentStateMessage)  
4. HTML/Visual guidance generation  
5. Text-to-Speech (TTS) cueing  
6. Downstream system modules that consume StateMessage

Documentation and code structure demonstrate the intended integration:

* The system uses ROS2 pub/sub patterns to ensure synchronized state updates.  
* StateSpec ingests structured JSON files containing states, triggers, guidance, and images.  
* StateNode binds these specifications to runtime behavior and publishes HTML guidance packets for the UI.  
* A separate TTS subsystem consumes /state/tts.  
* The operator console inputs map directly to state transitions.

Each workflow step maps to cached resources (images, text, triggers), which are served via the HTML template engine.

**TRL 3.4: Risk Areas identified in general terms**

**1\. JSON Resource Fragility**

Improperly formatted JSON or missing fields can cause runtime exceptions in \_ingest().

**2\. Image File Dependency**

Local absolute file paths (e.g., /home/vigil/Desktop/...) may break when deployed on other machines or containers.

**3\. Timing Variability**

Because state transitions rely on Python and ROS2 Python executors, latency may drift under CPU load.

**4\. Missing Trigger Ambiguity**

Natural language triggers are broad, and speech-to-text systems may produce unexpected variants.

**5\. Concurrency Conflicts**

The system uses a single-threaded executor, but external nodes may run multithreaded and send simultaneous trigger messages.

**6\. UI Rendering Differences**

HTML/CSS template may render differently across downstream operator consoles or browsers.

**TRL 3.5: Risk mitigation strategies identified**

**Mitigation 1 — Schema Validation**

Introduce a JSON schema and jsonschema validator before ingestion.

Prevents malformed state definitions from crashing the node.

**Mitigation 2 — Asset Packaging**

Bundle image assets using ROS2 packages or resource directories (ament\_index\_cpp/get\_package\_share\_directory), eliminating fragile absolute paths.

**Mitigation 3 — Timing Stabilization**

Move heavy computations out of the run loop.

Introduce lightweight performance monitors to detect high-latency conditions.

**Mitigation 4 — Trigger Normalization**

Add:  
Levenshtein distance matching

# Lowercase normalization

# STT statistical priors

# This reduces errors from slight variations in phrasing.

# **Mitigation 5 — Concurrency Protection**

# Add a small debouncing buffer or queue for trigger messages, ensuring deterministic ordering.

# **Mitigation 6 — UI Standardization**

# Export HTML guidance into a unified webview or Electron wrapper to ensure consistent styling.
