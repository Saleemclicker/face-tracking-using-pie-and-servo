import cv2
import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BCM)
pan_pin = 17  
tilt_pin = 27  
GPIO.setup(pan_pin, GPIO.OUT)
GPIO.setup(tilt_pin, GPIO.OUT)


pan_servo = GPIO.PWM(pan_pin, 50)  
tilt_servo = GPIO.PWM(tilt_pin, 50)
pan_servo.start(7.5) 
tilt_servo.start(7.5)

face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + 'haarcascade_frontalface_default.xml')
cap = cv2.VideoCapture(0)  

# Function to control servos
def move_servo(face_x, face_y, frame_width, frame_height):
   
    pan_angle = 7.5 + ((face_x - frame_width / 2) / frame_width) * 5
    tilt_angle = 7.5 + ((face_y - frame_height / 2) / frame_height) * 5
    
 
    pan_servo.ChangeDutyCycle(max(2.5, min(12.5, pan_angle)))
    tilt_servo.ChangeDutyCycle(max(2.5, min(12.5, tilt_angle)))

try:
    while True:
        ret, frame = cap.read()
        if not ret:
            print("Failed to capture frame")
            break


        gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
        faces = face_cascade.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))

        for (x, y, w, h) in faces:
            face_center_x = x + w // 2
            face_center_y = y + h // 2
            frame_height, frame_width = frame.shape[:2]
            move_servo(face_center_x, face_center_y, frame_width, frame_height)
            break

        # Display the frame
        cv2.imshow('Face Tracking', frame)

        # Exit on pressing 'q'
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

finally:
    pan_servo.stop()
    tilt_servo.stop()
    GPIO.cleanup()
    cap.release()
    cv2.destroyAllWindows()
