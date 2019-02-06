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

This document is step-by-step workshop to get started with cBlocks. The first part explains how to create a cBlock and set up the infrastructure to get going. The second part describes how the previously built cBlock can be used. As an example, a vibration cBlock is implemented, which is controlled by Google Assistant. 

## Make Them
### Architecture
### cBlock 
#### Hardware
- Schaltplan cBlock
- Schaltplan Protoboard
- Liste Teile
- Anleitung + Fotos (Vibrationsmotor mit Protoboard) 
#### Casing
#### Arduino SDK
### Bridge
#### Hardware
- Speaker
- Casing
- (Button)
#### Raspbian Image Link
### Backend
### Visualizer
## Use Them
1. Bridge
2. Pairing
3. Visualizer
4. Turn On
6. Define Mapping
7. IFTTT
