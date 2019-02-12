# cBlocks
cBlocks is a tool that empowers non-experts such as designers or electronics beginners to prototype the Internet of Things(IoT). Prototyping early ideas in the context of the Internet of Things is difficult. Building functional prototypes requires
advanced knowledge in technologies like Internet connectivity, sensor hardware and low level programming. At the same time evaluating the user experience of early concepts is extreme valuable because best practices in design are missing for this emerging and rather unexplored design space. With our system we provide a rapid prototyping tool for Internet of Things applications that is tailored to the needs of non-expert users. We encapsulate hardware details and facilitate easy Internet connections. At the same time our system is open source and therefore ready to be used and extended by the DIY community.

The system designs follows the following **design principles**:

- Plug & Play: No configuration, instant feedback.
- Hardware Abstraction: Act on physical instead of electrical values.
- Grow As You Go: Logic can be programmed on various levels of complexity. Users without knowledge in programming can use IFTTT to programm cBlocks. Users with programming experience can read and control cBlocks direclty via MQTT.
- Internet Connectivity: Intuitive pairing mechanism between cBlocks and bridge.
- Open Everything: New sensors or actuators can be added; external IoT products can be connected; built on an open hardware platform.

Anyone with making knowledge in electronics and programming can build their own cBlocks and provide it to the community. Below are two examples of cBlocks we have built. A button sensor (left) and a vibration motor (right). There are no restrictions, e.g. we also build a kitchen scale cBlock. You are only limited by your imagination!

TODO: Bilder

This document is step-by-step workshop to get started with cBlocks. The first part explains how to create a cBlock and set up the infrastructure to get going. The second part describes how the previously built cBlock can be used. As an example, a vibration cBlock is implemented, which is controlled by Google Assistant. 

## Make Them

In this section we will explain how to build a vibration motor cBlock and how to set up the infrastructure to get started.

### Architecture

First of all lets introduce the cBlocks architecture. This will help you in understanding the individual building blocks of the system.

TODO Bild.

#### cBlocks
These are the core building blocks used to sense and control the physical world. E.g. a temperature sensor, a button or a vibration motor.

#### Visualizer
The Visualizer is a web application meant to display sensor data and control cBlock actuators. It reads meta data
from the backend in order to detect which cBlocks are online and it receives current sensor readings via MQTT. The
state of a cBlock is visualized by user interface controls that depend on the data to be visualized or controlled. Furthermore, there is a data mapping feature. This way a user can attach semantic meaning to generic sensor readings; e. g. certain temperature ranges could be mapped to Cold and Hot, which allows for a more natural language.

#### Bridge
In order to connect cBlocks to the Internet we provide the Bridge component, which is currently implemented using a
Raspberry Pi 3. The Bridge communicates with the individual cBlocks via Wi-Fi (IEEE 802.11n). The Bridge allows for
minimal configuration since it offers a pairing mechanism for local cBlocks. Network credentials are exchanged between
the Bridge and cBlocks in order to connect the cBlocks with the network. An [audio protocol](https://github.com/weckbach/AstroMech) has been developed to broadcast the credentials to all nearby cBlocks.

#### Backend
The Backend is responsible for command/response handling, providing meta data about the cBlocks and semantic
mappings of readings and commands. Command/response handling is necessary because the MQTT protocol does not
provide any request/response model off the shelf. For instance, if the Visualizer sends a “turn off” command to the
RGB-LED, the Backend must check if the request has the correct format and respond with a timeout error message if the cBlock does not react timely. The Backend also provides meta data about the specific cBlocks. For example, the Visualizer reads which resources a cBlock provides, as well as which data type and units those resources use, in order to display them correctly. The temperature and humidity sensor for instance, has two numerical resources Temperature and Relative Humidity.

#### MQTT broker
The MQTT broker is used as a message bus, exchanging real-time sensor readings and actuator commands to and from the cBlocks. TThe MQTT Broker provides the means to connect any third-party software to the system, since it is an established standard in the IoT field.

### Make a cBlock 

Now, let's actually make our first cBlock.

#### Hardware

##### cBlocks Board
In order to get started with cblocks we first have to build the board. The cBlocks board is based on the [Adafruit Feather HUZZAH32 Dev Board](https://learn.adafruit.com/adafruit-huzzah32-esp32-feather/overview). It is an excellent dev board because it has a lot of nice features like the battery charger or the USB to serial converter. Since cBlocks need some more functionalites like a mircophone for pairing, a 5V booster, a status LED and some Buttons, we designed a board around the HUZZAH32. It's kind of a breakout of the breakout. You can find the schematics and the layout here (TODO link). Please order the board at your favorite PCB manufacturer. You can also export the parts list via Eagle and order the parts at your favorite store. Additionally you need the following parts:
- A microphone. We use the [MEMS microphone](https://www.sparkfun.com/products/9868) from SparkFun. 
- The Adafruit Feather HUZZAH32 itself. 
- A LiPo battery. We use this [1000mAH battery](https://www.exp-tech.de/zubehoer/batterien-akkus/lipo-akkus/5801/lipo-akku-1000mah-3.7-v-2-mm-jst), because it fits well inside the casing.

Once you have the board and all the parts you can start soldering it. The soldering ist pretty tough because the board includes some SMT pads and small parts. Here is how the soldered board looks like:

TODO Bild.

#### cBlocks Protoboard
In addition to the cBlock board there is also a cBlocks protoboard. It a shield that is stacked on top of the cBlock board that has some labeled pin outs. It makes prototyping a little easier, so feel free to order that board as well.

#### Vibration Motor Circuit
For the vibration motor cicuit you need the [Vibrating Mini Motor Disc](https://www.adafruit.com/product/1201) and the [Adafruit DRV2605L Haptic Controller Breakout](https://learn.adafruit.com/adafruit-drv2605-haptic-controller-breakout/overview). Feel free to use any other vibration motor. The hook up is straight forward:

| Protoboard        | DRV2605L           |
| ------------- |:-------------:|
| SDA      |SDA|
| SCL     |SCL|
| 3V3 | VIN|
| GND | GND|

Here is a photo of the final hook up:

TODO Photo.

#### Casing (optional)

This step is optional, but gives your cBlock a more finished look. We use a laser cutter to do the casing. Here is the corresponding SVG file(TODO Link) for the vibration motor. This is how the final vibration motor cBlock looks like:

TODO Bild.

#### Arduino SDK

Lets bring the cBlock to live! We developed a cBlocks Arduino SDK(TODO Link) thats makes the development of cBlocks as easy as developing Arduino sketches. The Arduino SDK can only be used via [Platform IO](https://platformio.org/). It is an IDE for developing embedded systems. We use it because it has a proper dependency management system and the cBlocks IDE has quite a few dependencies. There are extensions for Visual Studio Code and Atom. After you installed Platform IO, please clone our sample code via:

`git clone git@github.com:weckbach/cblocks-button.git && cd cblocks-button`

If you installed Platfrom IO on the CLI you can run `pio run -t upload` to upload the code to the cBlock. Otherwise follow the instructions for your IDE to upload the code. The cBlock won't work yet, because it needs at least the Bridge component to function.

### Bridge
The cBlocks Bridge has several functions:
- Wireless Access Point
- Gateway to the Internet
- MQTT-Bridge
- Provides pairing mechanism that makes configuration of cBlocks super easy.

#### Hardware
The hard of the Bridge is a [Raspberry Pi 3 Model B](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/). It has ethernet and WiFi and can be used as a low budget Wireless Access Point with [Apache Kura](https://www.eclipse.org/kura/). For the Audio Pairing you need an active speaker. Unfortunately we haven't developed a version with a button yet, so the pairing has to be triggered via the console. We wrote an audio broadcast protocol called [AstroMech](https://github.com/weckbach/AstroMech) for cBlocks. Feel free to write a little script, that uses a button to trigger the pairing. Please refer to the this [script](https://raw.githubusercontent.com/weckbach/AstroMech/master/RaspberryPi/main.py) in the AstroMech repository, it documents how to make AstroMech compliant sounds. Connect the speaker via cinch to the Rasperry Pi and you are set. 

#### Install Raspbian Image

To make the set up of the Bridge as easy as possible we provide Raspbian image(TODO Link) that includes all dependencies. Please download and install it on a SD card. You can use Etcher to write the image on the SD Card. Here are the [instuctions](https://www.raspberrypi.org/documentation/installation/installing-images/) of the official Raspberry documentation. After inserting the SD card into the Raspberry Pi, it is ready to start.

#### Network configuration

Once the Raspberry Pi is started you should see a new wireless networks called `cblocks-gateway`. The default password is `admin`. To configure the WiFi password go to the web page http://172.16.1.1/kura. The credentils are again `admin:admin`. Go to `networks -> wlan0 -> wireless`, here you can change the password. If you can't reach the web page please refer to the troubleshooting section.

After connecting to the network you can SSH onto the Pi via `ssh admin@172.16.1.1`. The password is again `admin`.

Now we have to configure the MQTT Bridge. Since we will use our desktop machine to host the backend, paste your IP address into the bridge config. Run `nano /etc/mosquitto/conf.d/bridge.conf` and exchange the IP address with yours. In linux systems you can find your IP adress by running `ifconfig`. Restart mosquitto by running `sudo service mosquitto restart`. 

#### Troubleshooting

Sometimes Kura does not work properly. SSH onto the Pi and run `sudo service kura restart`. After a while the service should be working and the Kura web page should be reachable again.

### Backend
#### Start
#### Create an entry in the registry
### Visualizer
## Use Them
1. Bridge
2. Pairing
3. Visualizer
4. Turn On
6. Define Mapping
7. IFTTT
