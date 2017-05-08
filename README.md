# To run this python application, type this command into a terminal windown that is open to the directory containing
# this file: python opencv.py haarcascade_frontalface_default.xml

import cv2
import sys
import requests
import logging as log
import datetime as dt
import requests
import json
from time import sleep

cascPath = sys.argv[1]
faceCascade = cv2.CascadeClassifier(cascPath)
log.basicConfig(filename='webcam.log',level=log.INFO)

video_capture = cv2.VideoCapture(0)
anterior = 0

while True:
    if not video_capture.isOpened():
        print('Unable to load camera.')
        sleep(5)
        pass

    # Capture frame-by-frame
    ret, frame = video_capture.read()

    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

    faces = faceCascade.detectMultiScale(
        gray,
        scaleFactor=1.1,
        minNeighbors=5,
        minSize=(30, 30)
    )

    # Draw a rectangle around the faces
    for (x, y, w, h) in faces:
        cv2.rectangle(frame, (x, y), (x+w, y+h), (0, 255, 0), 2)

 # Send a text message to a cell number to notify that a person has been detected       
        requests.post('https://wt-wndsrfr21-gmail_com-0.run.webtask.io/hello?msgvar=%22Hello! Face detected! %22&phonenumber=+14443338888')

    if anterior != len(faces):
        anterior = len(faces)
        log.info("faces: "+str(len(faces))+" at "+str(dt.datetime.now()))

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

    # Display the resulting frame
    cv2.imshow('Video', frame)

# When everything is done, release the capture
video_capture.release()
cv2.destroyAllWindows()
