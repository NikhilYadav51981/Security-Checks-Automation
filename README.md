🛡️ Cap: Intelligent Security and Automation Assistant
Cap is a voice-activated AI assistant designed for smart security and automation tasks. It combines facial recognition, speech processing, and Arduino-based hardware control to create a seamless, interactive experience. Built with Python, OpenCV, and face_recognition, Cap can identify known individuals, respond to voice commands, perform calculations, and control connected devices — all triggered by a secure face-and-voice activation protocol.
🚀 Features
🔐 Secure Activation
Face recognition + voice codeword required to unlock assistant functionality
Prevents unauthorized access to sensitive commands
🧠 Voice-Controlled Assistant
Responds to natural speech using Google Speech Recognition
Supports commands like:
"who is outside" → captures and identifies faces
"what's the time/date/day" → reports current system time
"calculate <expression>" → evaluates math expressions
"turn on/off <device>" → sends commands to Arduino
🧍‍♂️ Face Recognition Surveillance
Captures image from webcam
Matches against known faces (e.g., Nikhil, Hasan, Prabhat, Prakhar)
Alerts if unknown person is detected and opens live camera feed
🗣️ Speech Feedback
Uses pyttsx3 for offline text-to-speech responses
Provides real-time verbal feedback for all actions
🛠️ Arduino Integration
Sends serial commands to control relays, gates, bulbs, etc.
Supports commands like "TURN ON LIGHT" or "TURN OFF GATE"
🧰 Tech Stack
Component	Description
OpenCV	Webcam access and image capture
face_recognition	Facial encoding and comparison
speech_recognition	Voice command input via microphone
pyttsx3	Offline text-to-speech engine
serial (pySerial)	Communication with Arduino over COM port
subprocess & os	Launch and kill Windows camera app
math, re	Expression parsing and safe evaluation
📸 Sample Workflow
Startup: Captures initial image and checks for activation face
Voice Codeword: Waits for correct spoken codeword ("8738")
Command Mode: Accepts voice commands like:
"who is outside" → captures new image, identifies faces
"calculate 5 + 3" → evaluates and speaks result
"turn on gate" → sends command to Arduino
"close the program" → exits gracefully
🧪 Future Enhancements
Add GUI for manual override and status monitoring
Integrate cloud-based face recognition for scalability
Expand hardware control to include sensors and feedback loops
Add logging and alert system for unknown face detection
