# VIGIL System Research Paper Plan

## Paper Overview
**Title (Draft):** "VIGIL: A Modular AI-Powered Assistive Technology System for Medical Ultrasound Procedures"

**Target Journal:** Frontiers in AI-Powered Assistive Technologies
**Research Topic:** Future Directions in AI-Powered Assistive Technologies: Addressing Customization, Usability, and Adaptability

## Key Themes to Address (from Frontiers call)
1. **Customization** - How VIGIL addresses evolving needs of individuals with disabilities
2. **Usability** - HCI improvements and accessibility features
3. **Adaptability** - AI/ML algorithms that adjust to changing individual needs
4. **Novel Applications** - Medical ultrasound guidance as assistive technology
5. **Security and Usability** - Privacy considerations and system design

## Paper Structure

### 1. Abstract (~250 words)
- Brief overview of VIGIL system
- Problem statement: challenges in medical ultrasound procedures for generalist operators
- Key contributions: modular architecture, multi-modal AI integration, real-time guidance
- Main findings/outcomes

### 2. Introduction (~800-1000 words)
- **Context**: Assistive technologies in healthcare, particularly for medical procedures
- **Problem Statement**: 
  - Challenges in medical ultrasound procedures (cardiac LVEF, lower limb DVT)
  - Need for real-time guidance and feedback
  - Accessibility and usability concerns
- **VIGIL System Overview**: 
  - Purpose: Assist generalist operators in performing medical ultrasound procedures
  - Two primary use cases: Cardiac (LVEF) and Lower Limb (DVT) procedures
- **Contributions**: 
  - Modular ROS2-based architecture enabling customization
  - Integration of multiple AI/ML systems from different institutions
  - Real-time multi-modal feedback (visual, audio, haptic guidance)
  - Operator console and display systems for usability

### 3. Related Work (~600-800 words)
- AI-powered assistive technologies in healthcare
- Medical ultrasound guidance systems
- Modular robotics/ROS architectures
- Multi-modal human-computer interaction in medical contexts
- Real-time AI feedback systems

### 4. System Architecture (~1000-1200 words)
- **Overall Design Philosophy**
  - Distributed ROS2 architecture
  - Modular component design
  - External system integration framework
  
- **Core Components**
  - **Cardiac Module**: LVEF ultrasound guidance
    - UPenn system integration
    - Real-time image analysis
    - Anatomical landmark detection
    - Report generation
  - **Lower Limb Module**: DVT ultrasound guidance
    - CSU system integration
    - Patient pose estimation (Stevens SMPL)
    - Probe tracking (Cornell/SAM/Hand tracking)
    - Progress tracking (Northeastern)
    - Projection guidance (UMich)
  
- **Supporting Systems**
  - Speech-to-text (Rochester)
  - Text-to-speech
  - Visual Question Answering (UMich VQA)
  - Medical Agent
  - State management
  - Display/visualization system
  - Command and control (vigil_c2)

- **Communication Framework**
  - Custom ROS2 messages (vigil_msgs)
  - Topic-based pub/sub architecture
  - Service-based input handling
  - Temporal synchronization

### 5. Customization Framework (~800-1000 words)
- **Modular Design Principles**
  - UniversityAPI abstraction for external systems
  - Plugin architecture for adding new components
  - Configuration-based system behavior
  
- **External System Integration**
  - How different university systems are integrated
  - API standardization approach
  - Git subtree management for external code
  
- **Configuration Management**
  - config.ini for system parameters
  - JSON-based menu configuration
  - Runtime parameter adjustment
  
- **Use Case Adaptability**
  - Cardiac vs. Lower Limb configurations
  - Launch scripts for different scenarios
  - Component selection and activation

### 6. Usability Features (~800-1000 words)
- **Operator Console**
  - Curses-based terminal interface
  - Qt6 widget interface
  - Menu-driven command system
  - Component-specific inputs
  
- **Display System**
  - Real-time visualization
  - Annotated ultrasound images
  - Patient pose rendering
  - Guidance overlays
  - Debug displays
  
- **Multi-Modal Feedback**
  - Visual guidance (projection system)
  - Audio feedback (text-to-speech)
  - Speech input (speech-to-text)
  - Visual question answering
  
- **Human-Computer Interaction**
  - State-based workflow management
  - Progress tracking and feedback
  - Error handling and recovery

### 7. Adaptability and AI/ML Integration (~800-1000 words)
- **Real-Time Adaptation**
  - Dynamic state management
  - Temporal stream alignment
  - Real-time pose estimation and tracking
  
- **AI/ML Components**
  - Computer vision for ultrasound analysis
  - Pose estimation (SMPL-based)
  - Probe tracking algorithms
  - Visual question answering
  - Medical report generation
  
- **Learning and Personalization**
  - Progress tracking over time
  - Adaptive guidance based on operator performance
  - State transitions based on procedure progress

### 8. User Studies and Evaluation (~600-800 words)
- **Study Design**
  - Comparative study: VIGIL system vs. expert guidance
  - Participant demographics: medical students and novices (0 experience)
  - Tasks: DVT and cardiac ultrasound procedures
  - Within-subjects or between-subjects design (to be specified)
  
- **Evaluation Metrics** (if available)
  - Procedure completion rates
  - Time to completion
  - Quality of ultrasound images acquired
  - User satisfaction and usability metrics
  - Learning curve analysis
  
- **Preliminary Findings** (if available)
  - Performance comparisons
  - User feedback
  - System effectiveness for different experience levels

### 9. Implementation Details (~600-800 words)
- **Technical Specifications**
  - ROS2 Humble
  - Python 3.10
  - Hardware requirements (REACH system, sensors)
  - Software dependencies
  
- **System Deployment**
  - Installation process
  - Build system (colcon)
  - Launch configurations
  - Weight/model file management
  
- **Performance Considerations**
  - Real-time processing requirements
  - Multi-threading and async operations
  - Resource management

### 10. Discussion (~600-800 words)
- **Addressing Frontiers Themes**
  - How VIGIL addresses customization needs
  - Usability improvements over traditional systems
  - Adaptability to different procedures and operators
  
- **Limitations**
  - Current system limitations
  - Areas for improvement
  - Scalability considerations
  
- **Privacy and Security**
  - Data handling in medical context
  - Privacy considerations for patient data
  - System security measures

### 11. Future Work (~400-600 words)
- Planned enhancements
- Additional use cases
- Integration opportunities
- Research directions

### 12. Conclusion (~200-300 words)
- Summary of contributions
- Impact on assistive technology field
- Key takeaways

### 13. References
- Relevant papers on assistive technologies
- Medical ultrasound guidance systems
- ROS2 and robotics frameworks
- AI/ML in healthcare
- HCI in medical contexts

## Key Technical Details to Include

### System Components
1. **Cardiac Module**
   - UPenn cardiac analysis system
   - LVEF prediction
   - Anatomical landmark detection
   - Real-time image processing

2. **Lower Limb Module**
   - CSU system for DVT procedures
   - Stevens SMPL pose estimation
   - Cornell probe tracking
   - Northeastern progress tracking
   - UMich projection guidance

3. **Supporting Infrastructure**
   - Speech processing (Rochester)
   - VQA system (UMich)
   - Display and visualization
   - State management
   - Command and control

### Technical Architecture
- ROS2 distributed system
- Custom message types (20+ message definitions)
- Service-based input handling
- Topic-based data distribution
- Temporal synchronization utilities

## Questions/Information Needed

1. **Evaluation/Results**: 
   - ✅ User studies in progress with medical students and novices
   - ✅ Comparative study: VIGIL vs. expert guidance
   - ✅ Tasks: DVT and cardiac ultrasound procedures
   - Performance metrics (to be collected)

2. **Specific AI/ML Details**:
   - Model architectures used?
   - Training data information?
   - Performance benchmarks?

3. **User Feedback**:
   - Any feedback from operators who used the system?
   - Usability testing results?

4. **Deployment Context**:
   - Where has the system been deployed?
   - Real-world usage scenarios?

5. **Contributions**:
   - What are the novel contributions beyond system integration?
   - Any algorithmic innovations?

6. **Figures/Diagrams**:
   - System architecture diagram?
   - Component interaction diagrams?
   - Screenshots of the interface?