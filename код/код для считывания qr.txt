import cv2
import numpy as np
import pyzbar.pyzbar as pyzbar
import urllib.request
import serial
import time

arduino = serial.Serial(port='COM9', baudrate=115200, timeout=.1)
url='http://192.168.161.77/'
prev=""
pres=""
qr_counter = 1

def write_read(x):
    arduino.write(bytes(x,   'utf-8'))
    time.sleep(0.05)
    data = arduino.readline()
    return   data

while True:
    img_resp=urllib.request.urlopen(url+'cam-hi.jpg')
    imgnp=np.array(bytearray(img_resp.read()),dtype=np.uint8)
    frame=cv2.imdecode(imgnp,-1)
 
    decodedObjects = pyzbar.decode(frame)
    for obj in decodedObjects:
        pres=obj.data
        if prev == pres:
            pass
        else:
            out = str(obj.data)
            print(out[2:-1], "получено python консолью")
            value   = write_read(out)
            print(value)
            print("отправленно в ардуино")
            prev=pres
            file_name = "qr_{}.png".format(qr_counter)
            cv2.imwrite(file_name, frame)
            qr_counter += 1
