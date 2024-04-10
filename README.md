# raspberrypi3-Video control
control video from gpio inputs
import RPi.GPIO as GPIO
import os
import psutil

# Set up GPIO using BCM numbering
GPIO.setmode(GPIO.BCM)

# Define the GPIO pins connected to your inputs
input_pin1 = 18
input_pin2 = 23

# Set up the GPIO pins as inputs with pull-down resistors
GPIO.setup(input_pin1, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)
GPIO.setup(input_pin2, GPIO.IN, pull_up_down=GPIO.PUD_DOWN)

# Define the path to the video files on the USB drive
usb_path = "/media/pi/USB_DRIVE/"
video_file1 = "video1.mp4"
video_file2 = "video2.mp4"

# Function to play a video using VLC media player
def play_video(video_path):
    os.system("vlc --fullscreen --play-and-exit '" + video_path + "'")

# Function to stop the VLC media player process
def stop_video():
    for proc in psutil.process_iter(['pid', 'name']):
        if 'vlc' in proc.info['name']:
            proc.kill()

try:
    while True:
        # Check if input_pin1 is high
        if GPIO.input(input_pin1) == GPIO.HIGH:
            # Play video_file1
            video_path1 = os.path.join(usb_path, video_file1)
            play_video(video_path1)

        # Check if input_pin2 is high
        if GPIO.input(input_pin2) == GPIO.HIGH:
            # Play video_file2
            video_path2 = os.path.join(usb_path, video_file2)
            play_video(video_path2)

        # Check if input_pin1 is low
        if GPIO.input(input_pin1) == GPIO.LOW:
            stop_video()

        # Check if input_pin2 is low
        if GPIO.input(input_pin2) == GPIO.LOW:
            stop_video()

except KeyboardInterrupt:
    GPIO.cleanup()
