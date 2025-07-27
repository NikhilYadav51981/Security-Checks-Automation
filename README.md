# Security-Checks & Automation
import cv2
import face_recognition
import speech_recognition as sr
import pyttsx3
from datetime import datetime
import os
import subprocess
import math
import re
import serial
arduino_port = 'COM5'
baud_rate = 9600
try:
    ser = serial.Serial(arduino_port, baud_rate, timeout=1)
    print(f"Connected to {arduino_port} at {baud_rate} baud.")
except serial.SerialException as e:
    print(f"Error opening serial port {arduino_port}: {e}")
def calculate(expression):
    try:
        result = eval(expression, {"__builtins__": None}, math.__dict__)
        speak(f"The result is {result}")
        print(f"The result is {result}")
    except Exception as e:
        speak("Sorry, I couldn't perform the calculation.")
        print("Sorry, I couldn't perform the calculation:", e)
def open_camera_app(): 
    subprocess.Popen('start microsoft.windows.camera:', shell=True) 
def close_camera_app(): 
    os.system("taskkill /f /im WindowsCamera.exe")

def speak(text):
    engine = pyttsx3.init()
    engine.say(text)
    engine.runAndWait()

def capture_image(filename):
    cam = cv2.VideoCapture(0)
    ret, frame = cam.read()
    if ret:
        cv2.imwrite(filename, frame)
    cam.release()
    return filename

def display_image(filename):
    img = cv2.imread(filename)
    cv2.imshow('Captured Image', img)
    cv2.waitKey(0)
    cv2.destroyAllWindows()

def match_faces(known_images, unknown_image_path):
    unknown_image = face_recognition.load_image_file(unknown_image_path)
    unknown_encodings = face_recognition.face_encodings(unknown_image)

    matched_names = []
    for unknown_encoding in unknown_encodings:
        for photo, message in known_images.items():
            known_image = face_recognition.load_image_file(photo)
            known_encoding = face_recognition.face_encodings(known_image)[0]
            match = face_recognition.compare_faces([known_encoding], unknown_encoding)
            if match[0]:
                matched_names.append(message)
    return matched_names, len(unknown_encodings)

def recognize_speech():
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        speak("Listening for command...")
        print("Listening for command...")
        audio = recognizer.listen(source)
    try:
        command = recognizer.recognize_google(audio)
        return command.lower()
    except sr.UnknownValueError:
        speak("Could not understand the audio")
        print("Could not understand the audio")
    except sr.RequestError as e:
        speak(f"Could not request results; {e}")
        print("Could not request results; {0}".format(e))
    return ""

def main():
    known_images = {
        "C:\\Users\\dell\\Documents\\output\\IMG_20240304_130147.jpg": "Hello sir, it's Nikhil there",
        "C:\\Users\\dell\\Documents\\output\\IMG_20241114_090933.jpg": "Hello sir, it's Hasan there",
        "C:\\Users\\dell\\Documents\\output\\IMG_20241114_144449.jpg": "Hello sir, it's Prabhat there",
        "C:\\Users\\dell\\Documents\\output\\IMG_20241114_144453.jpg": "Hello sir, it's Prakhar there",

    }

    activation_face = "C:\\Users\\dell\\Documents\\output\\IMG_20240304_130147.jpg"
    activation_code_word = "8738"
    while True:
        initial_image = capture_image("initial_image.jpg")
        if match_faces({activation_face: "activation"}, initial_image):
            speak("Face matched. Please provide the code word.")
            print("Face matched. Please provide the code word.")
            command = recognize_speech()
            if command == activation_code_word:
                speak("Voice command activated.")
                print("Voice command activated.")
                while True:
                    command = recognize_speech()
                    if command == "who is outside":
                        new_image = capture_image("new_image.jpg")
                        matched_names, num_faces = match_faces(known_images, new_image)
                        speak(f"{num_faces} face(s) detected.")
                        print(f"{num_faces} face(s) detected.")
                        if matched_names:
                            message = " and ".join(matched_names)
                            speak(message)
                            print(message)
                        else:
                            speak("An unknown person is there. Let me show you")
                            print("An unknown person is there. Let me show you")
                            open_camera_app()  
                            while True: 
                                command = recognize_speech() 
                                if command == "got it": 
                                    close_camera_app() 
                                    print("Camera closed.") 
                                    break
                    elif command == "hello":
                        print("Hello Mr. Nikhil")
                        speak("Hello Mr. Nikhil")
                    elif command == "what's your name":
                        print("My name is Cap, I am a computer assistant")
                        speak("My name is Cap, I am a computer assistant")
                    elif command == "how can you help me":
                        print("I can perform tasks like security checks, automation works, and some normal calculations")
                        speak("I can perform tasks like security checks, automation works, and some normal calculations")
                    elif command == "what's the time":
                        current_time = datetime.now().strftime("%H:%M:%S")
                        speak(f"The current time is {current_time}")
                        print(f"The current time is {current_time}")
                    elif "calculate" in command:
                        expression = re.search(r"calculate (.*)", command).group(1)
                        calculate(expression)
                    elif command == "what's the day": 
                        current_day = datetime.now().strftime("%A") 
                        speak(f"Today is {current_day}") 
                        print(f"Today is {current_day}") 
                    elif command == "what's the date": 
                        current_date = datetime.now().strftime("%B %d, %Y") 
                        speak(f"Today's date is {current_date}") 
                        print(f"Today's date is {current_date}")
                    elif command in ['turn on', 'turn off', 'turn on light', 'turn off light', 'turn on gate', 'turn off gate', 'turn on bulb', 'turn off bulb']: 
                        ser.write(f"{command.upper()}\n".encode()) 
                        print(f"Sent command: {command}") 
                        speak(f"Command {command} sent to the relay")
                    elif command == "close the program":
                        speak("Closing the program. Thank you for watching my project. I hope you liked it.")
                        print("Closing the program")
                        return
if __name__ == "__main__":
    main()
