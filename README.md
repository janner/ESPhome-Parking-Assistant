# ESPhome Parking Assistant
ESPhome parking assistant with ESP32-C6, HC-SR04 ultrasonic sensor and WS2818 led strip

I wanted to create a visual parking assistant that uses an ultrasonic sensor to measure the distance between the garage wall and the car and an LED strip to visualize it. I used ESPHome's `on_value_range` trigger to define four distance ranges with corresponding `light.turn_on `trigger effects.

Under normal conditions, the ultrasonic sensor measures the distance every 60 seconds. However, I also created a binary sensor and an automation to enable "high-speed" measurement—every 500 milliseconds—when the garage door or PIR sensor is triggered.

This setup worked well enough for me, although I don’t like that it depends on the connection to Home Assistant.

## Getting started

### Hardware
- ESP32 dev board. I used ESP32 C6 like this one:

![image](https://github.com/user-attachments/assets/d09724e0-6afb-4344-9a18-318aa8b20f01)

- HC-SR04 ultrasonic sensor

![image](https://github.com/user-attachments/assets/e27d7426-4454-4a4d-8bcf-de937e5ea4f0)

- Short led strip like this

 ![image](https://github.com/user-attachments/assets/228bc865-072b-483d-a0d9-8951ce45a922)

Here is a rough wiring diagram

![image](https://github.com/user-attachments/assets/2617f1b2-430a-4875-bcb3-6e988db12a64)


### Home Assistant & ESPhome configuration

Create a helper input_boolean to Home Assistant. Let's call is "high_speed_update".

Edit the following automation to suit your use case.
```
alias: Garage event detection
description: ""
triggers:
  - type: occupied
    device_id: XXX
    entity_id: XXX
    domain: binary_sensor
    trigger: device
  - device_id: XXX
    domain: cover
    entity_id: XXX
    type: opening
    trigger: device
conditions: []
actions:
  - action: input_boolean.turn_on
    metadata: {}
    data: {}
    target:
      entity_id: input_boolean.high_speed_update
  - delay:
      hours: 0
      minutes: 1
      seconds: 0
      milliseconds: 0
  - action: input_boolean.turn_off
    metadata: {}
    data: {}
    target:
      entity_id: input_boolean.high_speed_update
mode: single
```

Copy the yaml file from this repo, edit the ESP board and other settings to your liking. Deploy it to your esp board.
