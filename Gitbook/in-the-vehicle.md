# In the vehicle

## Node

The sensors are configured as nodes on the CAN bus, meaning they each are paired with a small computer board. The latter acts as the middleman between the sensor’s interfacing protocol (i.e. the language the sensor talks in) and the CAN bus (i.e. what the telemetry system talks in).

The node computer hardware diagram is presented in the figure below.

<figure><picture><source srcset=".gitbook/assets/node dark 1@4x.png" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/node light 1@4x.png" alt=""></picture><figcaption></figcaption></figure>

<figure><picture><source srcset=".gitbook/assets/node dark 2@4x.png" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/node light 2@4x.png" alt=""></picture><figcaption><p>Technical diagrams of the node computer</p></figcaption></figure>

### Technical specs

{% tabs %}
{% tab title="Tech specs" %}
* The nodes use a ESP32 microcontroller (S3 or C3 models) from Espressif, manufactured by Seeed ([XIAO ESP32C3](https://wiki.seeedstudio.com/XIAO\_ESP32C3\_Getting\_Started/) / [XIAO ESP32S3](https://wiki.seeedstudio.com/xiao\_esp32s3\_getting\_started/)) (top surface).
* It uses two rows of 6 and 5 female headers (top surface) to connect to the sensor shield.
* A Qwiic / Stemma QT connector to connect to sensor breakout boards directly (no shield).
* They are equipped with [TCAN1462V-Q1](https://www.ti.com/lit/ds/symlink/tcan1462-q1.pdf?HQS=dis-mous-null-mousermode-dsf-pf-null-wwe\&ts=1698833597358) CAN transceivers (bottom surface) that make use of the MCU’s native [TWAI](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/peripherals/twai.html#overview) controllers.
* They have two daisy-chained 4-pin JST XH connectors (top surface) for the data transmission CAN High / Low, and the power pins from the Receiver +5V and GND.
* The slide switch on the top surface is the CAN termination switch, connecting the CAN High and Low lines by a 120 Ohm resistor.
* The CAN lines also have footprint for capacitors in series and parallel (bottom surface) to help if filtering is needed on the bus.
{% endtab %}

{% tab title="Pinout" %}
<figure><img src=".gitbook/assets/node pinout light@4x.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
This pinout diagram is valid for the XIAO ESP32-C3 microcontrollers, but some pins also double as analog pins on the XIAO ESP32-S3 (see Table 1), marked as A\<Number> (e.g. A5).
{% endhint %}

<table><thead><tr><th width="154">pin #</th><th>C3 pinout</th><th>S3 pinout</th></tr></thead><tbody><tr><td>1</td><td>5V</td><td>5V</td></tr><tr><td>2</td><td>GND</td><td>GND</td></tr><tr><td>3</td><td>3.3V</td><td>3.3V</td></tr><tr><td>4</td><td>D7 / RX / GPIO20</td><td>D7 / RX / GPIO44</td></tr><tr><td>5</td><td>D8 / SCK / GPIO8</td><td>D8 / A8 / SCK / GPIO7</td></tr><tr><td>6</td><td>D9 / MISO / GPIO9</td><td>D9 / A9 / MISO / GPIO8</td></tr><tr><td>7</td><td>D6 / TX / GPIO21</td><td>D6 / TX / GPIO43</td></tr><tr><td>8</td><td>D5 / SCL / GPIO7</td><td>D5 / A5 / SCL / GPIO6</td></tr><tr><td>9</td><td>D4 / SDA / GPIO6</td><td>D4 / A4 / SDA / GPIO5</td></tr><tr><td>10</td><td>D3 / A3 / GPIO5</td><td>D3 / A3 / GPIO4</td></tr><tr><td>11</td><td>D10 / MOSI / GPIO10</td><td>D10 / A10 / MOSI / GPIO9</td></tr><tr><td>CAN_TX</td><td>GPIO_NUM_3</td><td>GPIO_NUM_2</td></tr><tr><td>CAN_RX</td><td>GPIO_NUM_4</td><td>GPIO_NUM_3</td></tr><tr><td>CAN_Standby</td><td>GPIO2</td><td>GPIO1</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### Setup

#### Hardware

1. Assembling the node case (in ascending order)

<figure><img src=".gitbook/assets/node assembly light@4x.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
You can also connect sensor breakout boards that feature a Qwiic or Stemma QT connector from Adafruit and Sparkfun directly to the similar connector on the bottom surface. This is the case for the GPS node, which uses a Sparkfun breakout board.
{% endhint %}

2. Attaching the node to the CAN/Power bus

Connecting to the CAN and Power bus is easy. There are two 4-pin JST XH connectors opposite to the USB-C connector, which are connected in parallel for you to daisy-chain the whole system. This means that there’s no wrong way to connect it, just attach one cable to the bus, and if you want to add a new sensor, just attach another cable in the other connector and into the new node.

<figure><picture><source srcset=".gitbook/assets/can assembly dark@4x.png" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/can assembly light@4x.png" alt=""></picture><figcaption></figcaption></figure>

3. Installing in the vehicle

Place the node wherever it is needed in the vehicle.

<figure><img src=".gitbook/assets/sensor placement light@4x.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
If you need to make a new cable, make sure to use the proper connections as seen in the figure below.
{% endhint %}

<figure><picture><source srcset=".gitbook/assets/cable wiring dark@4x.png" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/cable wiring light@4x.png" alt="" width="375"></picture><figcaption><p>CAN &#x26; Power cable wiring diagram</p></figcaption></figure>

#### Software

1. Installing the integrated development environment (IDE)

The system works similar to most Arduino projects, as it has been coded in C++ using the Arduino IDE.

Any IDE will do the job, however we recommend using the following:

[Arduino IDE](https://www.arduino.cc/en/software) <mark style="color:orange;">is the easiest to use, has plenty of documentation and forum help and is sufficient almost 99% of the time.</mark>

[Platform IO](https://platformio.org/) (through VSCode) is essentially a pro version, where you have more control but requires a manual setup.

[ESP-IDF](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/index.html#introduction) is the MCU's native IDE, which gives more liberty in using all its functions.

2. ESP board definitions

The ESP boards (MCU) are not defined by default in the Arduino IDE. You will have to add them initially to be able to connect to the nodes and others.

To do this in the Arduino IDE, navigate to **File > Preferences**, and fill **"Additional Boards Manager URLs"** with the url below: [_https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package\_esp32\_index.json_](https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package\_esp32\_index.json)

Navigate to **Tools > Board > Boards Manager...**, type the keyword "**esp32**" in the search box, select the latest version of **esp32**, and install it.

3. Installing the node library

Download the two files `Node.h` and `Node.cpp` from the [Github repository](https://github.com/augustjaubert/UCL-SEM-Telemetry-System) in the Node folder.

To add the node library to your arduino sketch[^1], simply add the two files in your sketch folder.

<details>

<summary>Example folder organisation</summary>

```
example_sketch (folder)
-> Node.cpp
-> Node.h
-> example_sketch.ino (arduino sketch)
```

</details>

4. Connecting to the node computer board

To connect the node computer to the arduino sketch, select the top-left text box **Select Board**.

<figure><img src=".gitbook/assets/image (2).png" alt="" width="375"><figcaption></figcaption></figure>

For C3 models - XIAO\_ESP32C3

For S3 models - XIAO\_ESP32S3

### How to use

Find the quick guide in the [overview section](./#quick-installation-guide).

{% hint style="info" %}
You can add up to 70+ nodes onto the CAN bus, however limiting the number of sensors to <15 is recommended (i.e. limit the different messages competing on the CAN bus).
{% endhint %}

#### Steps

1. Switch the node ON.

<figure><img src=".gitbook/assets/node switch light@4x (1).png" alt="" width="375"><figcaption></figcaption></figure>

2. Assemble the node (sensor, computer and case).
3. Connect a CAN cable to one of the JST sockets to add the node on the CAN bus.
4. If the node is added at the end of the bus, switch the CAN termination ON<mark style="color:orange;">.</mark>

{% hint style="info" %}
After the system is powered, the node is powered if you see a LED lighting up.
{% endhint %}

{% hint style="warning" %}
Later versions of the node computer boards have a slide switch on the bottom, which need to be activated for the power rail to go through the board.&#x20;

Be careful because earlier versions have reversed ON/OFF writings by mistake, so if in doubt test it outside the vehicle, and if you see a blinking red/yellow light coming from the node, it is powered.
{% endhint %}

#### Troubleshooting

If it seems as though the Receiver is not seeing the sensor messages, or something else is not working as it should:

1. Disassemble the node.
2. Connect the node computer to your laptop through USB.
3. Open Arduino IDE, and check that you have the correct board definition in the top-left text box **Select Board**.

<figure><img src=".gitbook/assets/image (2).png" alt="" width="375"><figcaption></figcaption></figure>

<details>

<summary>No board appearing</summary>

If you don't see a board appearing, then the MCU is not powered, or the serial connection has been severed. This can be because,

* The USB cable is not properly connected either to the node or laptop
* The MCU is not receiving power, which can be checked with a multimeter on the MCU pins to see if it detects a voltage. This can be due to a severed power connection on the circuit.
* The serial connection is severed, which can be because&#x20;
  * the USB connector on the MCU has been damaged,&#x20;
  * or because something (e.g. static electricity) has caused your laptop to disconnect the USB port (safety mode). It happens that connection is re-established after some time.
    * You can first try to reset the board by clicking the **RESET BUTTON** once while the board is connected to your PC. If that does not work, hold the **BOOT BUTTON**, connect the board to your PC while holding the **BOOT** button, and then release it to enter **bootloader mode**.

</details>

<details>

<summary>The wrong board is appearing</summary>

If the wrong board is appearing, that is a good sign because it recognises something.

You can open the text box, click on select **other board and port...** and search for the appropriate board.

The boards are,

For C3 models - XIAO\_ESP32C3

For S3 models - XIAO\_ESP32S3

S3 models are identifiable by the writing on the sticker and the presence of solderable pins on the bottom, as seen on the right side of the image below.

<img src=".gitbook/assets/image (1) (1) (1).png" alt="" data-size="original">

</details>

4. Open the serial monitor, in the top-right corner.

<figure><img src=".gitbook/assets/image (2) (1).png" alt="" width="188"><figcaption></figcaption></figure>

This will present what is output on the serial port by the board, which you can use to deduce where the issues are coming from.

e.g. it can display "sensor not found on I2C bus" even when the sensor board is connected meaning that there is a connection issue between the node computer and sensor board).

{% hint style="info" %}
The serial output will only output things that it has been coded to output, so it is your responsibility to leave serial print commands in your code for debugging.
{% endhint %}

### Development

#### Node Library - to code a node

{% tabs %}
{% tab title="API reference" %}
* The `Node` class methods provide a high-level interface for interacting with a CAN bus network, abstracting away the complexities of direct hardware manipulation.
* The `VariableType` enumeration and `ExpectedMessage` structure facilitate the definition and handling of expected messages within the CAN network.

#### Usage Notes

* `uint32_t id`: The identifier of the expected message.
* `VariableType var1Type`: The type of the first variable in the message payload.
* `VariableType var2Type`: The type of the second variable in the message payload.

A structure representing an expected message with its identifier and variable types:

**`ExpectedMessage`**

* `INT32`: 32-bit signed integer.
* `UINT32`: 32-bit unsigned integer.
* `FLOAT`: Floating-point number.
* `NONE`: No variable (used when there is only one variable).

An enumeration defining the possible types of variables that can be included in a CAN message:

**`VariableType`**

12. `void initializeCANBus(gpio_num_t canRxPin, gpio_num_t canTxPin, uint8_t canStdbyPin, bool listenMode)`&#x20;
    * Initialises the CAN bus with the specified pins and mode.
13. `template <typename T> void convertToBytes(T value, byte *buffer)`&#x20;
    * Converts a value of type `T` to a byte array.

**Private Methods**

1. `void begin(gpio_num_t canRxPin = GPIO_NUM_NC, gpio_num_t canTxPin = GPIO_NUM_NC, uint8_t canStdbyPin = 0, bool listenMode = false)`
   * Initialises the CAN bus with the specified pins and mode.
2. `void initializeMessage(uint32_t id, uint8_t dataLength)`&#x20;
   * Initialises a message container with the specified ID and data length.
3. `void deleteMessage(uint32_t id)`&#x20;
   * Deletes a message from the `messages` container.
4. `template <typename T1, typename T2 = std::nullptr_t> void updateMessageData(uint32_t id, T1 var1, T2 var2 = nullptr)`&#x20;
   * Updates the data of a message with the given ID in the Node's message map.
5. `void transmitAllMessages(bool lowPowerMode = false, uint32_t sleepDurationMs = 1000)`&#x20;
   * Transmits all messages in the Node's message map to the CAN bus.
6. `void transmitMessage(uint32_t id, TickType_t ticks_to_wait = pdMS_TO_TICKS(1000))`&#x20;
   * Transmits a specific message with the specified ID and waits for the specified number of ticks.
7. `void addExpectedMessage(uint32_t id, VariableType var1Type)`&#x20;
   * Adds a single expected message with only one variable.
8. `void addExpectedMessage(uint32_t id, VariableType var1Type, VariableType var2Type)`&#x20;
   * Adds an expected message with two variables.
9. `void addExpectedMessages(const std::vector<ExpectedMessage> &messages)`&#x20;
   * Adds a collection of expected messages to the internal map.
10. `std::pair<uint32_t, std::pair<String, String>> parseReceivedMessage()`&#x20;
    * Parses the received CAN message and returns the message ID and string representations of the first and second variables in the message payload.
11. `void displayLatestMessageData()`&#x20;
    * Displays the data of the latest message in the Node's message map.

**Public Methods**

Creates an instance of the `Node` class.

```cpp
Node();
```

**Constructor**

**`Node`**

#### Classes and Structures

The `Node` library provides a framework for interacting with a CAN bus network using the ESP-IDF framework on ESP32 devices. It includes functionalities for initializing the CAN bus, sending and receiving messages, and managing message expectations.

The `Node` library provides a framework for interacting with a CAN bus network using the ESP-IDF framework on ESP32 devices. It includes functionalities for initializing the CAN bus, sending and receiving messages, and managing message expectations.

#### Classes and Structures

**`Node`**

**Constructor**

```cpp
Node();
```

Creates an instance of the `Node` class.

**Public Methods**

1. `void begin(gpio_num_t canRxPin = GPIO_NUM_NC, gpio_num_t canTxPin = GPIO_NUM_NC, uint8_t canStdbyPin = 0, bool listenMode = false)`
   * Initialises the CAN bus with the specified pins and mode.
2. `void initializeMessage(uint32_t id, uint8_t dataLength)`&#x20;
   * Initialises a message container with the specified ID and data length.
3. `void deleteMessage(uint32_t id)`&#x20;
   * Deletes a message from the `messages` container.
4. `template <typename T1, typename T2 = std::nullptr_t> void updateMessageData(uint32_t id, T1 var1, T2 var2 = nullptr)`&#x20;
   * Updates the data of a message with the given ID in the Node's message map.
5. `void transmitAllMessages(bool lowPowerMode = false, uint32_t sleepDurationMs = 1000)`&#x20;
   * Transmits all messages in the Node's message map to the CAN bus.
6. `void transmitMessage(uint32_t id, TickType_t ticks_to_wait = pdMS_TO_TICKS(1000))`&#x20;
   * Transmits a specific message with the specified ID and waits for the specified number of ticks.
7. `void addExpectedMessage(uint32_t id, VariableType var1Type)`&#x20;
   * Adds a single expected message with only one variable.
8. `void addExpectedMessage(uint32_t id, VariableType var1Type, VariableType var2Type)`&#x20;
   * Adds an expected message with two variables.
9. `void addExpectedMessages(const std::vector<ExpectedMessage> &messages)`&#x20;
   * Adds a collection of expected messages to the internal map.
10. `std::pair<uint32_t, std::pair<String, String>> parseReceivedMessage()`&#x20;
    * Parses the received CAN message and returns the message ID and string representations of the first and second variables in the message payload.
11. `void displayLatestMessageData()`&#x20;
    * Displays the data of the latest message in the Node's message map.

**Private Methods**

12. `void initializeCANBus(gpio_num_t canRxPin, gpio_num_t canTxPin, uint8_t canStdbyPin, bool listenMode)`&#x20;
    * Initialises the CAN bus with the specified pins and mode.
13. `template <typename T> void convertToBytes(T value, byte *buffer)`&#x20;
    * Converts a value of type `T` to a byte array.

**`VariableType`**

An enumeration defining the possible types of variables that can be included in a CAN message:

* `INT32`: 32-bit signed integer.
* `UINT32`: 32-bit unsigned integer.
* `FLOAT`: Floating-point number.
* `NONE`: No variable (used when there is only one variable).

**`ExpectedMessage`**

A structure representing an expected message with its identifier and variable types:

* `uint32_t id`: The identifier of the expected message.
* `VariableType var1Type`: The type of the first variable in the message payload.
* `VariableType var2Type`: The type of the second variable in the message payload.

#### Usage Notes

* The `Node` class methods provide a high-level interface for interacting with a CAN bus network, abstracting away the complexities of direct hardware manipulation.
* The `VariableType` enumeration and `ExpectedMessage` structure facilitate the definition and handling of expected messages within the CAN network.
{% endtab %}

{% tab title="Example sketch" %}
This example code initialises the CAN messages in the setup, and then in the loop updates them and sends them onto the bus.

```cpp
#include "Node.h"

/**
 * Pin Configuration for XIAO ESP32-C3 and XIAO ESP32-S3
 *
 * This section defines the GPIO pins used for TX, RX, and controlling
 * the sleep mode of the CAN transceiver chip based on the specific
 * board variant being used. Ensure that you select the correct pin
 * configuration for your hardware setup.
 *
 * TX_GPIO_NUM: Transmit Pin
 * - XIAO ESP32-C3: GPIO_NUM_3
 * - XIAO ESP32-S3 (e.g. Screen Node): GPIO_NUM_2
 *
 * RX_GPIO_NUM: Receive Pin
 * - XIAO ESP32-C3: GPIO_NUM_4
 * - XIAO ESP32-S3 (e.g. Screen Node): GPIO_NUM_3
 *
 * sleepPin: Sleep Control Pin for the CAN Transceiver
 * - XIAO ESP32-C3: 2
 * - XIAO ESP32-S3: 1
 *
 * Note:
 * - Make sure to define the correct board type before setting these pins.
 * - These configurations are critical for proper communication over the CAN bus.
 */

Node node;
void setup()
{
  // Begin serial communication at a baud rate of 115200.
  // This is necessary to print messages to the serial monitor.
  Serial.begin(115200);

  // Delay for 1 second to allow the serial monitor to initialize properly.
  // This is necessary to avoid interference with serial printing.
  delay(1000);

  // Initialize the node with the specified RX and TX pins, a standby pin, and a listen mode flag.
  // The node will use the specified pins for CAN communication.
  // The standby pin is used to control the CAN transceiver.
  // The listen mode flag determines whether the node will listen for messages on the CAN bus.
  node.begin(GPIO_NUM_4, GPIO_NUM_3, 2);

  // Initialize a message container with the specified ID and data length.
  // This function is called once per message ID that is used (i.e. to be sent or received).
  // The message container is used to store the message ID and the data associated with the message.
  node.initializeMessage(10, 8);

  // Initialize another message container with another ID and data length.
  node.initializeMessage(14, 4);

  // Display the data of the latest message in the Node's message map.
  // This function iterates through the messages in the Node's message map to find
  // the message with the highest ID, which is assumed to be the latest message.
  // It then prints the ID and data of the latest message.
  node.displayLatestMessageData();

  // Add multiple expected messages.
  // This vector contains ExpectedMessage objects that represent the expected messages received by the node.
  // Each ExpectedMessage object has an ID and two variable types.
  std::vector<ExpectedMessage> messages = {
      {20, INT32, FLOAT}, // Example of a message with two variables
      {24, UINT32, NONE}  // Example of a message with only one variable
  };

  // Add the expected messages to the Node's internal map of expected messages.
  node.addExpectedMessages(messages);
}

void loop()
{
  /*
   * In the example loop, we update and transmit messages.
   * We update message 10 with sine values of i and -i.
   * We update message 14 with the value -14.0.
   * We transmit all messages.
   * We delay for 1 second before the next iteration.
   */
  for (int i = 0; i < 314; i++)
  {
    // Update message 10 with sine values of i and -i.
    node.updateMessageData(10, (sin((float)i / 100)), (sin((float)-i / 100)));

    // Update message 14 with the value -14.0.
    node.updateMessageData(14, (float)-14);

    // Transmit all messages.
    node.transmitAllMessages(false);

    // Delay for 1 second before the next iteration.
    delay(1000);
  }
}
```
{% endtab %}

{% tab title="Example screen" %}
This example code receives CAN messages on the bus and hypothetically updates the screen accordingly.

```cpp
#include "Node.h"

/**
 * Pin Configuration for XIAO ESP32-C3 and XIAO ESP32-S3
 *
 * This section defines the GPIO pins used for TX, RX, and controlling
 * the sleep mode of the CAN transceiver chip based on the specific
 * board variant being used. Ensure that you select the correct pin
 * configuration for your hardware setup.
 *
 * TX_GPIO_NUM: Transmit Pin
 * - XIAO ESP32-C3: GPIO_NUM_3
 * - XIAO ESP32-S3 (e.g. Screen Node): GPIO_NUM_2
 *
 * RX_GPIO_NUM: Receive Pin
 * - XIAO ESP32-C3: GPIO_NUM_4
 * - XIAO ESP32-S3 (e.g. Screen Node): GPIO_NUM_3
 *
 * sleepPin: Sleep Control Pin for the CAN Transceiver
 * - XIAO ESP32-C3: 2
 * - XIAO ESP32-S3: 1
 *
 * Note:
 * - Make sure to define the correct board type before setting these pins.
 * - These configurations are critical for proper communication over the CAN bus.
 */

Node node; // Create an instance of the Node class

// The setup function is called once at startup
void setup() {
  // Initialize serial communication at a baud rate of 115200.
  Serial.begin(115200);

  // Introduce a delay of 1 second to allow for serial printing.
  // This delay is necessary to avoid issues with the serial output.
  delay(1000);

  // Initialize the CAN node with the specified pins and configuration.
  // The CAN_RX pin is GPIO_NUM_4, CAN_TX pin is GPIO_NUM_3,
  // CAN_STBY pin is 2, and CAN_LISTEN_MODE is set to false.
  // The CAN_RX and CAN_TX pins are specific to the XIAO ESP32-C3 and XIAO ESP32-S3.
  // Ensure to configure the correct pin configuration for your hardware setup.
  node.begin(GPIO_NUM_4, GPIO_NUM_3, 2, false);

  // Define multiple expected messages.
  // Each message is represented by an instance of the ExpectedMessage struct.
  // The struct contains the message ID and the variable types of the expected message payload.
  // In this example, we have two messages:
  // - Message ID: 10, Variable Type: FLOAT, FLOAT
  // - Message ID: 14, Variable Type: FLOAT, NONE (no second variable)
  std::vector<ExpectedMessage> messages = {
    { 10, FLOAT, FLOAT },
    { 14, FLOAT, NONE }
  };

  // Add the expected messages to the node.
  // This informs the node about the expected messages to receive.
  node.addExpectedMessages(messages);
}

// Main loop
void loop() {
  // Parse the received message and get the message ID, variable 1, and variable 2
  // The first element of the pair is the message ID, and the second element is a pair of strings for variable 1 and variable 2
  auto parsedMessage = node.parseReceivedMessage();

  // Check if a valid message ID was returned
  if (parsedMessage.first != 0) {
    // Get the message ID, variable 1, and variable 2
    uint32_t messageId = parsedMessage.first;  // The ID of the received message
    String var1 = parsedMessage.second.first;  // The value of the first variable in the message payload
    String var2 = parsedMessage.second.second;  // The value of the second variable in the message payload

    // Print the message ID and the values of the variables on the serial monitor
    printf("Message ID: %u\n", messageId);
    printf("Var1: %s\n", var1.c_str());
    printf("Var2: %s\n", var2.c_str());
    
    // ADD THE CODE TO UPDATE THE SCREEN HERE
  }
}
```
{% endtab %}

{% tab title="Example sensor" %}
This code was made for the voltmeter node, to show the implementation of the node library with a specific sensor (i.e. TI ADS1115 ADC chip).

```cpp
#include "Node.h"
#include <Adafruit_ADS1X15.h>

/**
 * Pin Configuration for XIAO ESP32-C3 and XIAO ESP32-S3
 *
 * This section defines the GPIO pins used for TX, RX, and controlling
 * the sleep mode of the CAN transceiver chip based on the specific
 * board variant being used. Ensure that you select the correct pin
 * configuration for your hardware setup.
 *
 * TX_GPIO_NUM: Transmit Pin
 * - XIAO ESP32-C3: GPIO_NUM_3
 * - XIAO ESP32-S3 (e.g. Screen Node): GPIO_NUM_2
 *
 * RX_GPIO_NUM: Receive Pin
 * - XIAO ESP32-C3: GPIO_NUM_4
 * - XIAO ESP32-S3 (e.g. Screen Node): GPIO_NUM_3
 *
 * sleepPin: Sleep Control Pin for the CAN Transceiver
 * - XIAO ESP32-C3: 2
 * - XIAO ESP32-S3: 1
 *
 * Note:
 * - Make sure to define the correct board type before setting these pins.
 * - These configurations are critical for proper communication over the CAN bus.
 */

Adafruit_ADS1115 ads; /* Use this for the 16-bit version */

Node node;
void setup()
{
  // Initialize serial communication
  Serial.begin(115200);

  // Delay to prevent issues with serial printing
  delay(1000);

  // Initialize the Node with specific GPIO pins and standby pin
  node.begin(GPIO_NUM_4, GPIO_NUM_3, 2);

  // Initialize messages with specific IDs and data lengths
  node.initializeMessage(10, 8);
  node.initializeMessage(11, 4);

  // The ADC input range (or gain) can be changed via the following
  // functions, but be careful never to exceed VDD +0.3V max, or to
  // exceed the upper and lower limits if you adjust the input range!
  // Setting these values incorrectly may destroy your ADC!
  //                                                                ADS1015  ADS1115
  //                                                                -------  -------
  // ads.setGain(GAIN_TWOTHIRDS);  // 2/3x gain +/- 6.144V  1 bit = 3mV      0.1875mV (default)
  // ads.setGain(GAIN_ONE);        // 1x gain   +/- 4.096V  1 bit = 2mV      0.125mV
  ads.setGain(GAIN_TWO); // 2x gain   +/- 2.048V  1 bit = 1mV      0.0625mV
  // ads.setGain(GAIN_FOUR);  // 4x gain   +/- 1.024V  1 bit = 0.5mV    0.03125mV
  //  ads.setGain(GAIN_EIGHT);      // 8x gain   +/- 0.512V  1 bit = 0.25mV   0.015625mV
  //  ads.setGain(GAIN_SIXTEEN);    // 16x gain  +/- 0.256V  1 bit = 0.125mV  0.0078125mV

  // Initialize the ADS1115 ADC
  if (!ads.begin())
  {
    Serial.println("Failed to initialize ADS.");
    while (1)
      ;
  }

  // Delay after initialization
  delay(500);

  // Set the data rate for the ADS1115 ADC
  ads.setDataRate(RATE_ADS1115_8SPS);
}

void loop()
{
  // Read ADC values for differential inputs 0-1 and 2-3
  int32_t results01 = ads.readADC_Differential_0_1();
  int32_t results23 = ads.readADC_Differential_2_3();

  // Map the ADC values to the desired range
  float mapped01 = map(results01, 5100, 32640, 500, 3200) / 1E2;
  // float mapped23 = map(results23, 5100, 32640, 500, 3200) / 1E2;
  // float mappedSOC = map((int32_t)(mapped23 * 10), 0, 327, 0, 100);

  // printf("0-1: %d  2-3: %d\n", results01, results23);
  printf("%d,%d,\n", results01, results23);
  printf("%f,\n", mapped01);

  // Update the message data
  // node.updateMessageData(10, mappedSOC, mapped23);
  // node.updateMessageData(11, mapped01);
  // node.transmitAllMessages();
  
  delay(500);
}
```
{% endtab %}
{% endtabs %}

#### Design a new sensor

The sensors come in all forms, but only one shape. They all have a standardised footprint that you can build on to make new sensors while still remaining compatible with the node computer.

<figure><img src=".gitbook/assets/image (3) (1).png" alt="" width="375"><figcaption><p>Template sensor shield footprint (Unit: mm)</p></figcaption></figure>

The footprint has four M3 sized holes for support, and two rows of pin headers placed just like the [node computer pinout diagram](in-the-vehicle.md#technical-specs).

The fusion PCB file can be found on the [Github repository](https://github.com/augustjaubert/UCL-SEM-Telemetry-System) under **Hardware**, and has the pin header sensor connections already pre-defined to simplify the design.

Some general help and thoughts on selecting a sensor:

* Think about what you want to know performance-wise (e.g. fuel consumption etc.), and search up what kind of sensor best suits your requirements.

You can have multiple sensors that can accomplish the same task, but have different pros and cons.&#x20;

e.g. to measure velocity, we could have had an RPM sensor or a GPS. GPS was a safer, more absolute solution, and in this case we already needed it for position.

{% hint style="info" %}
On big retailers websites (e.g. Mouser, Farnell, DigiKey, etc.) look for:

* the price (unit or bulk?),&#x20;
* availability (is it end of life?),&#x20;
* size (does it fit on the board?),&#x20;
* functionalities (handy reliability mechanisms, etc.)
{% endhint %}

* What communication protocol does it use?

Connecting to your sensor depends on the protocol the sensor uses to communicate. These can be Serial-Peripheral-Interface (SPI), Inter-Integrated Communication (I2C), UART or you have some general-purpose input/output (GPIO) pins that you can reprogram based on the ESP32-C3 or ESP32-S3 capabilities. The board also has an input for simple analog voltage reading up to 2.5V, however it is recommended to use the ADC shield instead of the ADC pins as they have lower resolution and impedance (essentially just worse, check out Espressif’s documentation).

You can see the pinout of the node computer in the [technical specifications](in-the-vehicle.md#technical-specs).

* Does it have an ECAD footprint available?

Often you are able to find the footprint ready-made by a retailer on their page (Mouser, etc.) or by a hobbyist. This simplifies the design process, however it is recommended to always double check the pinout matches that of the datasheet, as well as the dimensions.

{% hint style="info" %}
**Tip:** Find sensors with extensive online coverage (arduino tutorials, pre-existing code, etc.) as well as from breakout board manufacturers (e.g. Adafruit, Sparkfun, Seed). They often open source their electrical diagrams and code.
{% endhint %}

When it comes to wiring the sensor on the board and adding the necessary components around the chip, it is best to follow the datasheet given by the manufacturer (e.g. Texas Instruments). They often present example schematics showcasing the most common setups.

Most of the sensors developed this year were chosen because they had been covered in online tutorials, and thus had already proven code and easy-to-use libraries for them. This makes it easy to pair with our node library, as you can see in the [sensor example](in-the-vehicle.md#node-library-to-code-a-node).

#### Build a node computer

If you need to order and assemble a new node computer, you can use the gerber files provided in the [Github repository](https://github.com/augustjaubert/UCL-SEM-Telemetry-System), and upload them to JLCPCB.

{% tabs %}
{% tab title="Schematics" %}
**Electrical Diagram**

{% file src=".gitbook/assets/Node computer schematic.pdf" %}

**PCB footprint**

{% file src=".gitbook/assets/Node PCB Footprint.pdf" %}
{% endtab %}

{% tab title="3D Viewer" %}
{% embed url="https://a360.co/3WZYaDd" %}
{% endtab %}

{% tab title="Parts list (BOM)" %}
<table><thead><tr><th>Qty</th><th>Value</th><th>Device</th><th>Package</th><th width="72">Parts</th><th width="120">Description</th><th width="136">Manufacturer</th><th>Link</th><th>Unit (£)</th><th>Total (£)</th></tr></thead><tbody><tr><td>1</td><td></td><td>PINHD-1X5</td><td>1X05</td><td>JP3</td><td>PIN HEADER</td><td>n/a</td><td>n/a</td><td>n/a</td><td>#VALUE!</td></tr><tr><td>1</td><td></td><td>PINHD-1X6</td><td>1X06</td><td>JP1</td><td>PIN HEADER</td><td>n/a</td><td>n/a</td><td>n/a</td><td>#VALUE!</td></tr><tr><td>2</td><td>0</td><td>R_CHIP-0805(2012-METRIC)</td><td>RESC2012X65</td><td>R3</td><td></td><td>n/a</td><td>n/a</td><td>n/a</td><td>#VALUE!</td></tr><tr><td>3</td><td>0.1 uF</td><td>C_CHIP-0805(2012-METRIC)</td><td>CAPC2012X110</td><td>C1</td><td></td><td>n/a</td><td>n/a</td><td>n/a</td><td>#VALUE!</td></tr><tr><td>2</td><td>60</td><td>R_CHIP-0805(2012-METRIC)</td><td>RESC2012X65</td><td>R1</td><td></td><td>n/a</td><td>n/a</td><td>n/a</td><td>#VALUE!</td></tr><tr><td>1</td><td>CDSOT23-T24CAN</td><td>CDSOT23-T24CAN</td><td>SOT95P230X117-3N</td><td>D1</td><td>CAN TVS Diode</td><td>Bourns</td><td><a href="https://www.mouser.co.uk/ProductDetail/Bourns/CDSOT23-T24CAN?qs=Z7P4xsdcg2LuAOb/zQ7xoQ%3D%3D">https://www.mouser.co.uk/ProductDetail/Bourns/CDSOT23-T24CAN?qs=Z7P4xsdcg2LuAOb/zQ7xoQ%3D%3D</a></td><td>0.288</td><td>0.288</td></tr><tr><td>1</td><td>JS102011JCQN</td><td>JS102011JCQN</td><td>JS102011JCQN</td><td>S1</td><td>Slide Switches .3A 6VDC SPDT VERT MNT SMT J BEND</td><td>C &#x26; K COMPONENTS</td><td><a href="https://www.mouser.co.uk/ProductDetail/CK/JS102011JCQN?qs=74EMXstkWMWQs9mNwlyl0g%3D%3D">https://www.mouser.co.uk/ProductDetail/CK/JS102011JCQN?qs=74EMXstkWMWQs9mNwlyl0g%3D%3D</a></td><td>0.728</td><td>0.728</td></tr><tr><td>2</td><td>JST-XH-04-PIN-LONG-PAD</td><td>JST-XH-04-PIN-LONG-PAD</td><td>JST-XH-04-PACKAGE-LONG-PAD</td><td>X1</td><td></td><td>n/a</td><td>n/a</td><td>n/a</td><td>#VALUE!</td></tr><tr><td>3</td><td>NC</td><td>C_CHIP-0805(2012-METRIC)</td><td>CAPC2012X110</td><td>C3</td><td></td><td>n/a</td><td>n/a</td><td>n/a</td><td>#VALUE!</td></tr><tr><td>1</td><td>PCM12SMTR</td><td>PCM12SMTR</td><td>PCM12SMTR</td><td>S3</td><td>Slide Switches 0.3A SPDT ON-ON</td><td>C &#x26; K COMPONENTS</td><td><a href="https://www.mouser.co.uk/ProductDetail/CK/PCM12SMTR?qs=mfFuHy8STfL3qrPSfCHA7w%3D%3D&#x26;_gl=1*1yvwsae*_ga*ODY2MzE2ODU2LjE2ODk3MDQzMTM.*_ga_15W4STQT4T*MTcwODAwMTEwMS40My4wLjE3MDgwMDExMDEuNjAuMC4w*_ga_1KQLCYKRX3*MTcwODAwMTEwMS4yNy4wLjE3MDgwMDExMDEuMC4wLjA">https://www.mouser.co.uk/ProductDetail/CK/PCM12SMTR?qs=mfFuHy8STfL3qrPSfCHA7w%3D%3D&#x26;_gl=1*1yvwsae*_ga*ODY2MzE2ODU2LjE2ODk3MDQzMTM.*_ga_15W4STQT4T*MTcwODAwMTEwMS40My4wLjE3MDgwMDExMDEuNjAuMC4w*_ga_1KQLCYKRX3*MTcwODAwMTEwMS4yNy4wLjE3MDgwMDExMDEuMC4wLjA</a>.</td><td>0.8</td><td>0.8</td></tr><tr><td>1</td><td>PMEG1020EJ</td><td>PMEG1020EJ</td><td>SOD-323</td><td>D4</td><td>Schottky Rectifier, 10 V, 2 A, Single, SOD-323, 2 Pins, 460 mV</td><td>NEXPERIA</td><td><a href="https://www.mouser.co.uk/ProductDetail/Nexperia/PMEG1020EJ115?qs=LOCUfHb8d9tlKh8c7mukrA%3D%3D">https://www.mouser.co.uk/ProductDetail/Nexperia/PMEG1020EJ115?qs=LOCUfHb8d9tlKh8c7mukrA%3D%3D</a></td><td>0.24</td><td>0.24</td></tr><tr><td>1</td><td>QWIIC_RIGHT_ANGLE</td><td>QWIIC_CONNECTORJS-1MM</td><td>JST04_1MM_RA</td><td>J1</td><td>SparkFun I2C Standard Qwiic Connector</td><td>n/a</td><td>n/a</td><td>n/a</td><td>#VALUE!</td></tr><tr><td>1</td><td>TCAN1462VDRQ1</td><td>TCAN1462VDRQ1</td><td>SOIC127P600X175-8N</td><td>IC2</td><td>CAN FD transceiver</td><td>Texas Instruments</td><td><a href="https://www.mouser.co.uk/ProductDetail/Texas-Instruments/TCAN1462VDRQ1?qs=rQFj71Wb1eU28cwJFt9kYg%3D%3D">https://www.mouser.co.uk/ProductDetail/Texas-Instruments/TCAN1462VDRQ1?qs=rQFj71Wb1eU28cwJFt9kYg%3D%3D</a></td><td>1.63</td><td>1.63</td></tr><tr><td>1</td><td>XIAO-ESP32C3</td><td>XIAO-ESP32C3</td><td>XIAO-ESP32C3-MODULE14P-2.54-21X17.8MM</td><td>U1</td><td>Seeed Studio XIAO ESP32C3</td><td>Seed</td><td><a href="https://thepihut.com/products/seeed-xiao-esp32c3">https://thepihut.com/products/seeed-xiao-esp32c3</a></td><td>5</td><td>5</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

Some tips on soldering the node computer,&#x20;

* Start with the bottom surface with the slide switch
* Progress to the other small components
* Place large components before placing the smaller ones around it (e.g. CAN transceiver before its capacitors)
* On the top side, place the switch first, then the MCU, then the JST sockets and finally the female pin headers. Note that the pin headers are male on the Fusion 360 project, which is not a problem since male and female have the same PCB footprint.

## Receiver

The receiver is a computer board, who’s only function is to receive those sensor messages, store them onto a microSD card and handle the radio.

The receiver hardware diagram is presented in the figure below.

<figure><picture><source srcset=".gitbook/assets/receiver diagram dark@4x.png" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/receiver diagram light@4x.png" alt=""></picture><figcaption><p>Technical diagram of the receiver</p></figcaption></figure>

### Technical specs

{% tabs %}
{% tab title="Tech specs" %}
The receiver uses a [ESP32-S3-WROOM-1-N16R2](https://www.espressif.com/sites/default/files/documentation/esp32-s3-wroom-1\_wroom-1u\_datasheet\_en.pdf) module as a microcontroller.

On the top surface it has,

* a microSD socket for datalogging using the ESP's native SD\_MMC library
* a USB-C port to connect to a computer
* boot and reset buttons like any Arduino board
* an Adafruit SK6812 RGB LED, programmable with [Adafruit's Neopixel Library](https://www.arduino.cc/reference/en/libraries/adafruit-neopixel/)
* a programmable mode button
* a 4 pin JST-XH connector similar to the node computers for CAN and power
* a CAN termination switch and a power switch to the rest of the system (nodes)
* a Omron XW4K-04A1-V1 board side connector socket to receive 5V from a power source (battery, etc.) connected with a Omron XW4H-04A1 wire side connector plug

<figure><img src=".gitbook/assets/image (4) (1).png" alt=""><figcaption><p>Image of the soldered Omron XW4K-04A1-V1 board side connector socket</p></figcaption></figure>

On the underside it has,

* a USB - Battery power switch, to disconnect the battery from the receiver MCU (hard reset)
* a 433MHz RFM69HCW radio module
* a male SMA radio antenna connector

It can be programmed and display data on the serial port by connecting to a computer through the USB-C port, and opening an IDE (Arduino IDE, PlatformIO or ESP-IDF).
{% endtab %}

{% tab title="Pinout" %}
| Pin Description              |  ESP32-S3-WROOM-1-N16R2 |
| ---------------------------- | ----------------------- |
| RFM69\_DIO\_0                | GPIO 3                  |
| RFM69\_DIO\_1                | GPIO 9                  |
| RFM69\_DIO\_2                | GPIO 10                 |
| RFM69\_RESET                 | GPIO 48                 |
| RFM69\_NSS (SPI Chip Select) | GPIO 47                 |
| SPI\_SCK (RMF69)             | GPIO 12                 |
| SPI\_MOSI (RMF69)            | GPIO 11                 |
| SPI\_MISO (RFM69)            | GPIO 13                 |
| CAN\_TX                      | GPIO 18                 |
| CAN\_RX                      | GPIO 8                  |
| CAN\_STANDBY                 | GPIO 35                 |
| MODE\_BUTTON                 | GPIO 14                 |
| NEOPIXEL\_LED                | GPIO 21                 |
| SD\_DAT0                     | GPIO 6                  |
| SD\_DAT1                     | GPIO 5                  |
| SD\_DAT2                     | GPIO 17                 |
| SD\_DAT3                     | GPIO 16                 |
| SD\_CMD                      | GPIO 15                 |
| SD\_CD                       | GPIO 4                  |
{% endtab %}
{% endtabs %}

### Setup

#### Hardware

1. Assembling the receiver case (in ascending order)

<figure><img src=".gitbook/assets/image (9).png" alt="" width="563"><figcaption></figcaption></figure>

2. Attaching the node to the CAN/Power bus

Just like on the nodes, there is a JST-XH socket to connect the receiver to the CAN and Power bus.

The Omron socket also has CAN lines so that if a power source also has a CAN output (e.g. Battery BMS system outputting CAN messages), they can be attached to the same connector plug.&#x20;

You can see the connection placement in the centre of the image below, where **L is for CAN\_LOW**, **H for CAN\_HIGH**, **- for GROUND** and **5V for +5V**.

<figure><img src=".gitbook/assets/image (1).png" alt="" width="375"><figcaption></figcaption></figure>

3. Installing in the vehicle

Just like for [a node](in-the-vehicle.md#hardware), you can then place the receiver anywhere in the vehicle.

#### Software

Similar to in the [node software setup](in-the-vehicle.md#software).

<details>

<summary>Similar steps</summary>

1. Installing the integrated development environment (IDE)

The system works similar to most Arduino projects, as it has been coded in C++ using the Arduino IDE.

Any IDE will do the job, however we recommend using the following:

[Arduino IDE](https://www.arduino.cc/en/software) <mark style="color:orange;">is the easiest to use, has plenty of documentation and forum help and is sufficient almost 99% of the time.</mark>

[Platform IO](https://platformio.org/) (through VSCode) is essentially a pro version, where you have more control but requires a manual setup.

[ESP-IDF](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/index.html#introduction) is the MCU's native IDE, which gives more liberty in using all its functions.

2. ESP board definitions

The ESP boards (MCU) are not defined by default in the Arduino IDE. You will have to add them initially to be able to connect to the nodes and others.

To do this in the Arduino IDE, navigate to **File > Preferences**, and fill **"Additional Boards Manager URLs"** with the url below: [_https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package\_esp32\_index.json_](https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package\_esp32\_index.json)

Navigate to **Tools > Board > Boards Manager...**, type the keyword "**esp32**" in the search box, select the latest version of **esp32**, and install it.

</details>

3. Installing the receiver library

Download the 15 files from the [Github repository](https://github.com/augustjaubert/UCL-SEM-Telemetry-System) in the Receiver folder.

To add the receiver library to your arduino sketch[^2], add all the files in your sketch folder.

<details>

<summary>Example project organisation</summary>

<pre><code>example_sketch (folder)
-> Receiver.cpp
-> Receiver.h
-> Config.h
-> SDCardHandler.cpp
-> SDCardHandler.h
<strong>-> RadioHandler.cpp
</strong>-> RadioHandler.h
-> ModeConfig.cpp
-> ModeConfig.h
-> DataProcessing.cpp
-> DataProcessing.h
-> ConfigFileParser.cpp
-> ConfigFileParser.h
-> CANMessageParser.cpp
-> CANMessageParser.h
-> example_sketch.ino (arduino sketch)
</code></pre>

</details>

4. Connecting to the receiver board

To connect the receiver board to the arduino sketch, select the top-left text box **Select Board** and select **ESP32-S3-USB-OTG**.

<figure><img src=".gitbook/assets/image (2).png" alt="" width="375"><figcaption></figcaption></figure>

1. Assemble the receiver (board and case).
2. Connect a CAN cable to the JST sockets to add the receiver to the CAN bus.
3. Usually the receiver is added at the end of the bus, therefore switch the CAN termination ON<mark style="color:orange;">.</mark>
   1. If the power source connector (Omron) also has a CAN input, then switch the CAN termination OFF. There will need to be a CAN termination at the power source side of the CAN bus.

### How to use

#### Steps

1. Insert the micro-SD card in the socket. See [here](in-the-vehicle.md#development-1) for more info on the telemetry data configuration.
2. Assemble the node (sensor, computer and case).
3. Connect a CAN cable to one of the JST sockets to add the node on the CAN bus.
4. If the node is added at the end of the bus, switch the CAN termination ON.

#### **Troubleshooting**

If it seems as though the receiver is not working as intended:

1. Disassemble the receiver.
2. Connect the receiver board to your laptop through USB.
3. Open Arduino IDE, and check that you have the correct board definition in the top-left text box **Select Board**.

<details>

<summary>No board appearing</summary>

If you don't see a board appearing, then the MCU is not powered, or the serial connection has been severed. This can be because,

* The USB cable is not properly connected either to the node or laptop
* The MCU is not receiving power, which can be checked with a multimeter on the MCU 3.3V pins to see if it detects a voltage. This can be due to a severed power connection on the circuit. It is recommended to check the Espressif's chip datasheet in detail.
* The serial connection is severed, which can be because&#x20;
  * the USB connector on the MCU has been damaged,&#x20;
  * or because something (e.g. static electricity) has caused your laptop to disconnect the USB port (safety mode). It happens that connection is re-established after some time.
    * You can first try to reset the board by clicking the **RESET BUTTON** once while the board is connected to your PC. If that does not work, hold the **BOOT BUTTON**, connect the board to your PC while holding the **BOOT** button, and then release it to enter **bootloader mode**.
  * a very unlucky reason can be that the MCU chip has failed. This has happened a few times in the past, and can be fixed by carefully desoldering the MCU chip with a hot air gun and heat-resistant Capton tape around other components, to then either replacing with a new chip or de-bricking the "dead" chip by connecting it with a test board module (possibly left in the MechSpace Workshop C drawers).

<img src=".gitbook/assets/image (1) (1).png" alt="" data-size="original">

</details>

<details>

<summary>The wrong board is appearing</summary>

If the wrong board is appearing, that is a good sign because it recognises something.

You can open the text box, click on select **other board and port...** and search for the appropriate board (ESP32-S3-USB-OTG).

</details>

4. Open the serial monitor, in the top-right corner.

<figure><img src=".gitbook/assets/image (2) (1).png" alt="" width="188"><figcaption></figcaption></figure>

This will present what is output on the serial port by the board, which you can use to deduce where the issues are coming from.

{% hint style="info" %}
The serial output will only output things that it has been coded to output, so it is your responsibility to leave serial print commands in your code for debugging.

This also means that you can check the receiver sketch and library codebase to find out where from the serial print output is coming from in the code.
{% endhint %}

### Development

#### Receiver Library

{% hint style="info" %}
The Receiver codebase is quite a convoluted assortment of libraries to separate and classify all the different functions.&#x20;

There is a handler for everything SD communication related, Radio related, mode button,&#x20;
{% endhint %}

{% tabs %}
{% tab title="API Reference" %}
The `Receiver` library provides a framework for interacting with a CAN bus network using the ESP-IDF framework on ESP32 devices. It includes functionalities for initialising an SD card, a CAN bus, receiving, processing and storing CAN messages, and transmitting them over radio.

#### Classes and Structures

**`Receiver`**

**Constructor**

```cpp
Receiver::Receiver(const String& configFilePath)
```

Creates a new `Receiver` instance initialized with the specified configuration file path.

**Public Methods**

1. `void Receiver::begin(bool liveSendingMode)`
   * Initialises the Receiver object by setting up the SD card, radio, configuration parser, and CAN interface. liveSendingMode Flag indicating whether to enable live sending mode.
2. `void Receiver::receiveCAN(SemaphoreHandle_t& dataMutex, bool displayParameters)`&#x20;
   * Receives a CAN message and handles it if the message is valid.
     * dataMutex is a semaphore handle used to protect access to shared data.
     * the displayParameters Flag indicating whether to display updated parameters after handling the message.
3. `void Receiver::printParameters()`&#x20;
   * Prints the CAN message parameters stored in the configuration parser.
4. `void Receiver::displayUpdatedParameters()`&#x20;
   * Displays the updated parameters by iterating over the CAN message configurations and checking for updates.
5. `void transmitAllMessages(bool lowPowerMode = false, uint32_t sleepDurationMs = 1000)`&#x20;
   * Transmits all messages in the Node's message map to the CAN bus.
6. `void Receiver::initLapFence(float latA, float lngA, float latB, float lngB, float latC, float lngC, float latD, float lngD)`
   * Initialises the lap fence to count laps with the given coordinates.
7. `void Receiver::initRadioFence(float latA, float lngA, float latB, float lngB, float latC, float lngC, float latD, float lngD)`
   * Initialises the radio fence to send telemetry data with the given coordinates.
8. `void Receiver::togglePrintStatements()`
   * Toggles the state of print statements.

#### Usage Notes

* The `Receiver` class provides a high-level interface for handling CAN messages, radio communication, and SD card operations in a receiver system. It abstracts away the complexities of direct hardware manipulation, making it easier to integrate these functionalities into a larger system.
* Key features include:
  * Initialisation of the receiver system with configurable options
  * Reception and handling of CAN messages
  * Displaying and updating of CAN message parameters
  * Support for both lap fences and radio fences
  * Toggleable print statements for debugging and monitoring
  *
{% endtab %}

{% tab title="Example sketch" %}
This example shows the use of the receiver library in the receiver sketch.

```cpp
#include <RHReliableDatagram.h>
#include <Adafruit_NeoPixel.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "driver/twai.h"
#include "esp_system.h"
#include <RH_RF69.h>
#include "esp_err.h"
#include "esp_log.h"
#include <stdlib.h>
#include "SD_MMC.h"
#include <stdio.h>
#include <SPI.h>
#include "FS.h"
#include <map>
#include <EEPROM.h>

#include "Receiver.h"

#define ESP_EXCEPTION_DECODER

Receiver receiver("/config/README.txt");

SemaphoreHandle_t dataMutex;  // Mutex to protect shared data

void CANHandlerTask(void *pvParameters) {
  for (;;) {
    // Handle button press events here
    receiver.receiveCAN(dataMutex, true);
    //receiver.displayUpdatedParameters();
    vTaskDelay(pdMS_TO_TICKS(0));  // Adjust delay according to your application requirements
  }
}

void setup() {
  Serial.begin(115200);

  delay(100);  // delay to let serial begin have a breather huff huff

  receiver.begin(true);
  receiver.printParameters();

  //receiver.initLapFence(51.5280372, -0.1426583, 51.5280462, -0.1425165, 51.5279792, -0.1425112, 51.5279757, -0.1426627);    // NW1 3QS Compton Close Park
  //receiver.initRadioFence(51.5280363, -0.1430492, 51.5280545, -0.1422529, 51.5279662, -0.1422228, 51.5279683, -0.1430449);  // NW1 3QS Compton Close Park

  //receiver.initLapFence(51.4199761, 0.3482651, 51.4200143, 0.3483264, 51.4198693, 0.3488128, 51.4198191, 0.3487607);    // CycloPark
  //receiver.initRadioFence(51.4205795, 0.3484007, 51.4206552, 0.3484280, 51.4202241, 0.3496966, 51.4201799, 0.3496453);  // CycloPark

  receiver.initLapFence(43.7708310, -0.0408539, 43.7709265, -0.0406479, 43.7706997, -0.0404429, 43.7706075, -0.0406456);    // Nogaro track
  receiver.initRadioFence(43.7719449, -0.0423221, 43.7723014, -0.0417674, 43.7712505, -0.0403314, 43.7707955, -0.0409531);  // Nogaro track

  // Create mutex
  dataMutex = xSemaphoreCreateMutex();
  if (dataMutex == NULL) {
    printf("Failed to create mutex\n");
    // Handle the error appropriately, e.g., stop the program
    while (1)
      ;
  }

  xTaskCreatePinnedToCore(
    CANHandlerTask,    // Task function
    "CANHandlerTask",  // Task name
    4096,              // Stack size (bytes)
    NULL,              // Task parameters
    3,                 // Task priority
    NULL,              // Task handle
    0                  // Core to run the task (0 or 1)
  );
}

void loop() {
}
```
{% endtab %}
{% endtabs %}

#### Telemetry Data Configuration

On the SD card connected to the receiver there is a **config** folder with a **README.TXT** file.

The README file is used to configure the telemetry messages received from nodes and which ones to be sent over radio to the race engineer.

<details>

<summary>README file format</summary>

The format of the configuration is,

```
<Message_name>, <CAN_ID>, <Param1_Name>, <Param1_Unit>, <Param1_type>, <Param1_bits>, <Param2_Name>, <Param2_Unit>,<Param2_type>,<Param2_bits>
```

For example, for the GPS node which we will name GPS1,

* a CAN bus message ID of 10&#x20;
* sending latitude in degrees in the first 4 bytes with a variable type of 32-bit integer
* sending longitude in degrees in the last 4 bytes with a variable type of 32-bit integer

Altogether, to add a message from the GPS node into one line,

```
GPS1, 22, Lat, deg, I, 32, Lon, deg, I, 32
```

To specify wether the message is also to be forwarded to the race engineer over radio, you can add an @ sign in front of the line.

<pre><code><strong>@GPS1, 22, Lat, deg, I, 32, Lon, deg, I, 32
</strong></code></pre>

This process is repeated for all the expected messages from the nodes,

```
<Message_name>, <CAN_ID>, <Param1_Name>, <Param1_Unit>, <Param1_type>, <Param1_bits>, <Param2_Name>, <Param2_Unit>,<Param2_type>,<Param2_bits>

@SUPCAP1, 10, SOC,%, F, 32, CAP_Volt, V,F, 32

THROTTLE, 11, Volt1_2, V,F,32

@CURR1, 14, Curr1, A,F, 32, Curr1_raw, bit, I, 32

@CURR2, 15, Curr2, A, F, 32, Curr2_raw, bit, I, 32

@CURR3, 16, Curr3, A,F, 32, Curr3_raw, bit, I, 32

GPS1, 22, Lat, deg, I, 32, Lon, deg, I, 32

etc...
```

When the receiver sees a message and cross-identifies it from the README file, it will record it into a folder with its Message\_name. Messages that are not written down correctly onto the README file will not be recorded onto the SD card.

</details>

<details>

<summary>How radio modes work</summary>

There are two radio modes:

* **Live-send telemetry**, where all the data to be sent is sent the instant it is received by the in-vehicle receiver and is never sent again after that. This is useful to have real-time information, but is only possible if the vehicle is in view and at a short distance (e.g. 200m)
* **Batch-send telemetry**, where all the data to be sent is stored in a file on the SD card until the vehicle enters a pre-defined geofenced zone where it starts sending all the stored data for a lap together. The idea is that you would be sending a whole lap's data just in one section of the track. This is useful if the vehicle is not in view at all times, or if it goes further than the max transmission distance (400-500m).

To activate either mode, you need to define it in the begin statement of the Receiver class/object.

```cpp
Receiver receiver("/config/README.txt");
bool liveSendModeOn = false; // This means the batch-send mode will be used
...
void setup() {
  receiver.begin(liveSendModeOn);
...
}
```

</details>

#### Build a receiver

If you need to order and assemble a new receiver, you can use the gerber files provided in the [Github repository](https://github.com/augustjaubert/UCL-SEM-Telemetry-System), and upload them to JLCPCB.

{% tabs %}
{% tab title="Schematics" %}
**Electrical Diagram**

{% file src=".gitbook/assets/receiver v2ra schematic.pdf" %}

**PCB footprint**

{% file src=".gitbook/assets/Receiver PCB Footprint.pdf" %}
{% endtab %}

{% tab title="3D Viewer" %}
{% embed url="https://a360.co/3wpQ9fS" %}
{% endtab %}

{% tab title="Parts list (BOM)" %}
<table><thead><tr><th>Qty</th><th>Value</th><th>Device</th><th>Package</th><th>Parts</th><th>Description</th><th>MANUFACTURER_NAME</th><th>MANUFACTURER_PART_NUMBER</th><th width="94">Link</th><th>Unit (£)</th><th>Total (£)</th></tr></thead><tbody><tr><td>1</td><td></td><td>SMA CONNECTOR EDGE</td><td>SMA_EDGELAUNCH</td><td>X1</td><td>SMA Female Connector</td><td>Amazon</td><td>N/A</td><td><a href="https://www.amazon.co.uk/100PCS-Female-Coaxial-Connector-Material/dp/B0B2KTBDZX/ref=sr_1_1?crid=15EKR3UBDX0H2&#x26;keywords=kerlife+10Pcs+Brass+SMA+Female+PCB+Panel+Mount+Connector+Female+Base+Socket+Jack+50+Ohm+RF+Connectors+for+Wireless+Device+Equipments&#x26;qid=1708338485&#x26;sprefix=kerlife+10pcs+brass+sma+female+pcb+panel+mount+connector+female+base+socket+jack+50+ohm+rf+connectors+for+wireless+device+equipments,aps,64&#x26;sr=8-1">https://www.amazon.co.uk/100PCS-Female-Coaxial-Connector-Material/dp/B0B2KTBDZX/ref=sr_1_1?crid=15EKR3UBDX0H2&#x26;keywords=kerlife+10Pcs+Brass+SMA+Female+PCB+Panel+Mount+Connector+Female+Base+Socket+Jack+50+Ohm+RF+Connectors+for+Wireless+Device+Equipments&#x26;qid=1708338485&#x26;sprefix=kerlife+10pcs+brass+sma+female+pcb+panel+mount+connector+female+base+socket+jack+50+ohm+rf+connectors+for+wireless+device+equipments,aps,64&#x26;sr=8-1</a></td><td>0.1953</td><td>0.1953</td></tr><tr><td>1</td><td>0</td><td>R_CHIP-0603(1608-METRIC)</td><td>RESC1608X60</td><td>R22</td><td>Resistor Fixed - SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>2</td><td>0.1 uF</td><td>C_CHIP-0603(1608-METRIC)</td><td>CAPC1608X85</td><td>C1</td><td>Capacitor - Ceramic SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>7</td><td>0.1uF</td><td>C_CHIP-0603(1608-METRIC)</td><td>CAPC1608X85</td><td>C2</td><td>Capacitor - Ceramic SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>2</td><td>0.1uF (NC)</td><td>C_CHIP-0603(1608-METRIC)</td><td>CAPC1608X85</td><td>C12</td><td>Capacitor - Ceramic SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>2</td><td>100k</td><td>R_CHIP-0603(1608-METRIC)</td><td>RESC1608X60</td><td>R2</td><td>Resistor Fixed - SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>12</td><td>10k</td><td>R_CHIP-0603(1608-METRIC)</td><td>RESC1608X60</td><td>R4</td><td>Resistor Fixed - SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>4</td><td>10uF</td><td>C_CHIP-0805(2012-METRIC)</td><td>CAPC2012X110</td><td>C5</td><td>Capacitor - Ceramic SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>1</td><td>1k</td><td>R_CHIP-0603(1608-METRIC)</td><td>RESC1608X60</td><td>R25</td><td>Resistor Fixed - Generic</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>1</td><td>1uF</td><td>C_CHIP-0603(1608-METRIC)</td><td>CAPC1608X85</td><td>C20</td><td>Capacitor - Ceramic SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>1</td><td>300</td><td>R_CHIP-0603(1608-METRIC)</td><td>RESC1608X60</td><td>R12</td><td>Resistor Fixed - Generic</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>1</td><td>400k</td><td>R_CHIP-0603(1608-METRIC)</td><td>RESC1608X60</td><td>R21</td><td>Resistor Fixed - Generic</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>2</td><td>5.1k</td><td>R_CHIP-0603(1608-METRIC)</td><td>RESC1608X60</td><td>R3</td><td>Resistor Fixed - SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>1</td><td>500</td><td>R_CHIP-0603(1608-METRIC)</td><td>RESC1608X60</td><td>R1</td><td>Resistor Fixed - SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>2</td><td>60</td><td>R_CHIP-0805(2012-METRIC)</td><td>RESC2012X65</td><td>R8_C</td><td>Resistor Fixed - SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>4</td><td>NC</td><td>C_CHIP-0603(1608-METRIC)</td><td>CAPC1608X85</td><td>C4</td><td>Capacitor - Ceramic SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>2</td><td>NC</td><td>R_CHIP-0603(1608-METRIC)</td><td>RESC1608X60</td><td>R14</td><td>Resistor Fixed - SMD</td><td></td><td></td><td></td><td></td><td>0</td></tr><tr><td>1</td><td>500ASSP1M6QE</td><td>500ASSP1M6QE</td><td>500ASSP1M6QE</td><td>S1</td><td>Power Slide Switch</td><td>E-SWITCH</td><td>500ASSP1M6QE</td><td><a href="https://www.mouser.co.uk/ProductDetail/E-Switch/500ASSP1M6QE?qs=%252BZnE/xbLNR9QgzPkMZRAQg%3D%3D">https://www.mouser.co.uk/ProductDetail/E-Switch/500ASSP1M6QE?qs=%252BZnE/xbLNR9QgzPkMZRAQg%3D%3D</a></td><td>3.37</td><td>3.37</td></tr><tr><td>1</td><td>CDSOT23-T24CAN</td><td>CDSOT23-T24CAN</td><td>SOT95P230X117-3N</td><td>D1</td><td>CAN TVS Diode</td><td>Bourns</td><td>CDSOT23-T24CAN</td><td><a href="https://www.mouser.co.uk/ProductDetail/Bourns/CDSOT23-T24CAN?qs=Z7P4xsdcg2LuAOb/zQ7xoQ%3D%3D">https://www.mouser.co.uk/ProductDetail/Bourns/CDSOT23-T24CAN?qs=Z7P4xsdcg2LuAOb/zQ7xoQ%3D%3D</a></td><td>0.288</td><td>0.288</td></tr><tr><td>1</td><td>COM-13909</td><td>COM-13909</td><td>MOD_COM-13909</td><td>RFM69</td><td>Rfm69hcw General ISM &#x3C; 1GHz Transceiver Module 915/433MHz Surface Mount</td><td>HOPERF</td><td>RFM69HCW</td><td><a href="https://www.mouser.co.uk/ProductDetail/Adafruit/5693?qs=mELouGlnn3fnO8nGmYJgWQ%3D%3D">https://www.mouser.co.uk/ProductDetail/Adafruit/5693?qs=mELouGlnn3fnO8nGmYJgWQ%3D%3D</a></td><td>4.76</td><td>4.76</td></tr><tr><td>1</td><td>ESP32-S3-WROOM-1-N16R2</td><td>ESP32-S3-WROOM-1-N16R2</td><td>XCVR_ESP32-S3-WROOM-1-N16R2</td><td>U1</td><td>Espressif ESP32-S3 Microcontroller</td><td>Espressif</td><td>ESP32-S3-WROOM-1-N16R2</td><td><a href="https://www.amazon.co.uk/gp/product/B09TPMJ8SN/ref=ox_sc_saved_title_1?smid=A30DC7701CXIBH&#x26;psc=1">https://www.amazon.co.uk/gp/product/B09TPMJ8SN/ref=ox_sc_saved_title_1?smid=A30DC7701CXIBH&#x26;psc=1</a></td><td>1.856</td><td>1.856</td></tr><tr><td>1</td><td>JS102011JCQN</td><td>JS102011JCQN</td><td>JS102011JCQN</td><td>S3</td><td>Slide Switch .3A 6VDC SPDT VERT MNT SMT J BEND (CAN Termination)</td><td>C &#x26; K COMPONENTS</td><td>JS102011JCQN</td><td><a href="https://www.mouser.co.uk/ProductDetail/CK/JS102011JCQN?qs=74EMXstkWMWQs9mNwlyl0g%3D%3D">https://www.mouser.co.uk/ProductDetail/CK/JS102011JCQN?qs=74EMXstkWMWQs9mNwlyl0g%3D%3D</a></td><td>0.728</td><td>0.728</td></tr><tr><td>1</td><td>JST-XH-04-PIN-LONG-PAD</td><td>JST-XH-04-PIN-LONG-PAD</td><td>JST-XH-04-PACKAGE-LONG-PAD</td><td>X4</td><td>JST XH Connector 4 Pin</td><td>Amazon</td><td>N/A</td><td><a href="https://www.amazon.co.uk/sourcing-map-Connector-Housing-Adapter/dp/B0BTGWYK4Y/ref=sr_1_1?crid=1DS3556JRIRSL&#x26;keywords=sourcing+map+2.54mm+Pitch+XH+Connector+Kits+4+Pin+Male+Female+Header+Right+Angle+Socket+Housing+Cable+Adapter+and+Crimp+DIP+Kits,+50+Set(300pcs)&#x26;qid=1708336662&#x26;sprefix=sourcing+map+2.54mm+pitch+xh+connector+kits+4+pin+male+female+header+right+angle+socket+housing+cable+adapter+and+crimp+dip+kits+50+set+300pcs+,aps,121&#x26;sr=8-1&#x26;th=1">https://www.amazon.co.uk/sourcing-map-Connector-Housing-Adapter/dp/B0BTGWYK4Y/ref=sr_1_1?crid=1DS3556JRIRSL&#x26;keywords=sourcing+map+2.54mm+Pitch+XH+Connector+Kits+4+Pin+Male+Female+Header+Right+Angle+Socket+Housing+Cable+Adapter+and+Crimp+DIP+Kits,+50+Set(300pcs)&#x26;qid=1708336662&#x26;sprefix=sourcing+map+2.54mm+pitch+xh+connector+kits+4+pin+male+female+header+right+angle+socket+housing+cable+adapter+and+crimp+dip+kits+50+set+300pcs+,aps,121&#x26;sr=8-1&#x26;th=1</a></td><td>0.02496667</td><td>0.02496667</td></tr><tr><td>1</td><td>LM1117MPX-3.3_NOPB</td><td>LM1117MPX-3.3_NOPB</td><td>SOT230P700X180-4N</td><td>IC3</td><td>Space saving 800-mA low-dropout linear regulator with internal current limit</td><td>Texas Instruments</td><td>LM1117MPX-3.3/NOPB</td><td><a href="https://www.mouser.co.uk/ProductDetail/Texas-Instruments/LM1117MPX-3.3-NOPB?qs=X1J7HmVL2ZHRbBIxXi4utg%3D%3D">https://www.mouser.co.uk/ProductDetail/Texas-Instruments/LM1117MPX-3.3-NOPB?qs=X1J7HmVL2ZHRbBIxXi4utg%3D%3D</a></td><td>0.886</td><td>0.886</td></tr><tr><td>1</td><td>MEM2075-00-140-01-A</td><td>MEM2075-00-140-01-A</td><td>MEM20750014001A</td><td>J1</td><td>MEM2075-00-140-01-A - MICRO SD CARD CONN</td><td>GCT</td><td>MEM2075-00-140-01-A</td><td><a href="https://www.mouser.co.uk/ProductDetail/GCT/MEM2075-00-140-01-A?qs=KUoIvG/9IlZvfWpeERlq3Q%3D%3D">https://www.mouser.co.uk/ProductDetail/GCT/MEM2075-00-140-01-A?qs=KUoIvG/9IlZvfWpeERlq3Q%3D%3D</a></td><td>1.58</td><td>1.58</td></tr><tr><td>1</td><td>NEOPIXEL</td><td>1655</td><td>1655</td><td>LED1</td><td>NeoPixel 5050 RGB LED with Integrated Driver Chip</td><td>Adafruit</td><td>NeoPixel 5050 - 1655</td><td><a href="https://www.mouser.co.uk/ProductDetail/Adafruit/1655?qs=GURawfaeGuATdkjqVQs49g%3D%3D">https://www.mouser.co.uk/ProductDetail/Adafruit/1655?qs=GURawfaeGuATdkjqVQs49g%3D%3D</a></td><td>0.36</td><td>0.36</td></tr><tr><td>1</td><td>PCM12SMTR</td><td>PCM12SMTR</td><td>PCM12SMTR</td><td>S2</td><td>Slide Switches 0.3A SPDT ON-ON (USB Switch)</td><td>C &#x26; K COMPONENTS</td><td>PCM12SMTR</td><td><a href="https://www.mouser.co.uk/ProductDetail/CK/PCM12SMTR?qs=mfFuHy8STfL3qrPSfCHA7w%3D%3D&#x26;_gl=1*1yvwsae*_ga*ODY2MzE2ODU2LjE2ODk3MDQzMTM.*_ga_15W4STQT4T*MTcwODAwMTEwMS40My4wLjE3MDgwMDExMDEuNjAuMC4w*_ga_1KQLCYKRX3*MTcwODAwMTEwMS4yNy4wLjE3MDgwMDExMDEuMC4wLjA">https://www.mouser.co.uk/ProductDetail/CK/PCM12SMTR?qs=mfFuHy8STfL3qrPSfCHA7w%3D%3D&#x26;_gl=1*1yvwsae*_ga*ODY2MzE2ODU2LjE2ODk3MDQzMTM.*_ga_15W4STQT4T*MTcwODAwMTEwMS40My4wLjE3MDgwMDExMDEuNjAuMC4w*_ga_1KQLCYKRX3*MTcwODAwMTEwMS4yNy4wLjE3MDgwMDExMDEuMC4wLjA</a>.</td><td>0.8</td><td>0.8</td></tr><tr><td>1</td><td>PMEG1020EJ</td><td>PMEG1020EJ</td><td>SOD-323</td><td>D4</td><td>Schottky Rectifier, 10 V, 2 A, Single, SOD-323, 2 Pins, 460 mV</td><td>NEXPERIA</td><td></td><td><a href="https://www.mouser.co.uk/ProductDetail/Nexperia/PMEG1020EJ115?qs=LOCUfHb8d9tlKh8c7mukrA%3D%3D">https://www.mouser.co.uk/ProductDetail/Nexperia/PMEG1020EJ115?qs=LOCUfHb8d9tlKh8c7mukrA%3D%3D</a></td><td>0.24</td><td>0.24</td></tr><tr><td>3</td><td>PTS841ESDGKPSMTRLFS</td><td>PTS841ESDGKPSMTRLFS</td><td>PTS841ESDGKPSMTRLFS</td><td>BOOT</td><td>Side-actuated tactile switch</td><td>C&#x26;K</td><td>PTS841 ESD GKP SMTR LFS</td><td><a href="https://www.mouser.co.uk/ProductDetail/CK/PTS841-ESD-GKP-SMTR-LFS?qs=d0WKAl%252BL4KbeUlwuiSQZWA%3D%3D">https://www.mouser.co.uk/ProductDetail/CK/PTS841-ESD-GKP-SMTR-LFS?qs=d0WKAl%252BL4KbeUlwuiSQZWA%3D%3D</a></td><td>0.312</td><td>0.936</td></tr><tr><td>1</td><td>SRV05-4.TCT</td><td>SRV05-4.TCT</td><td>SOT95P280X145-6N</td><td>D2</td><td>USB TVS Diode ESD Suppressor</td><td>Semtech</td><td>SRV05-4.TCT</td><td><a href="https://www.mouser.co.uk/ProductDetail/Semtech/SRV05-4.TCT?qs=rBWM4%252BvDhIe0tfPjqKjR5Q%3D%3D">https://www.mouser.co.uk/ProductDetail/Semtech/SRV05-4.TCT?qs=rBWM4%252BvDhIe0tfPjqKjR5Q%3D%3D</a></td><td>0.808</td><td>0.808</td></tr><tr><td>1</td><td>TCAN1462VDRQ1</td><td>TCAN1462VDRQ1</td><td>SOIC127P600X175-8N</td><td>CAN_T</td><td>CAN FD transceiver</td><td>Texas Instruments</td><td>TCAN1462VDRQ1</td><td><a href="https://www.mouser.co.uk/ProductDetail/Texas-Instruments/TCAN1462VDRQ1?qs=rQFj71Wb1eU28cwJFt9kYg%3D%3D">https://www.mouser.co.uk/ProductDetail/Texas-Instruments/TCAN1462VDRQ1?qs=rQFj71Wb1eU28cwJFt9kYg%3D%3D</a></td><td>1.63</td><td>1.63</td></tr><tr><td>1</td><td>TPS2110APWR</td><td>TPS2110APWR</td><td>SOP65P640X120-8N</td><td>IC2</td><td>Autoswitching Power Mux</td><td>Texas Instruments</td><td>TPS2110APWR</td><td><a href="https://www.mouser.co.uk/ProductDetail/Texas-Instruments/TPS2110APWR?qs=ojKcPFmCWSW5gmLyPB8quA%3D%3D">https://www.mouser.co.uk/ProductDetail/Texas-Instruments/TPS2110APWR?qs=ojKcPFmCWSW5gmLyPB8quA%3D%3D</a></td><td>1.3</td><td>1.3</td></tr><tr><td>1</td><td>TYPE-C-31-M-12</td><td>TYPE-C-31-M-12</td><td>HRO_TYPE-C-31-M-12</td><td>J2</td><td>USB Type C Connector (JLCPCB Assembled)</td><td>HRO Electronics Co.</td><td>TYPE-C-31-M-12</td><td><a href="https://jlcpcb.com/partdetail/Korean_HropartsElec-TYPE_C_31_M12/C165948">https://jlcpcb.com/partdetail/Korean_HropartsElec-TYPE_C_31_M12/C165948</a></td><td>0.157</td><td>0.157</td></tr><tr><td>1</td><td>XW4K-04A1-V1CONN_XW4K-04A1_OMR</td><td>XW4K-04A1-V1CONN_XW4K-04A1_OMR</td><td>CONN_XW4K-04A1_OMR</td><td>J3</td><td>Pluggable Terminal Block 4 pin Vertical Port</td><td>Omron</td><td>XW4K-04A1-V1</td><td><a href="https://www.mouser.co.uk/ProductDetail/Omron-Electronics/XW4K-04A1-V1?qs=wYonFgBYHKlPZJOhR4CEXw%3D%3D&#x26;countryCode=GB&#x26;currencyCode=GBP&#x26;_gl=1*1bs521v*_ga*ODY2MzE2ODU2LjE2ODk3MDQzMTM.*_ga_1KQLCYKRX3*MTcwODMzMjk4NS4xMi4xLjE3MDgzMzI5ODguMC4wLjA.*_ga_15W4STQT4T*MTcwODMzMjk4NS4yOC4xLjE3MDgzMzI5ODguNTcuMC4w">https://www.mouser.co.uk/ProductDetail/Omron-Electronics/XW4K-04A1-V1?qs=wYonFgBYHKlPZJOhR4CEXw%3D%3D&#x26;countryCode=GB&#x26;currencyCode=GBP&#x26;_gl=1*1bs521v*_ga*ODY2MzE2ODU2LjE2ODk3MDQzMTM.*_ga_1KQLCYKRX3*MTcwODMzMjk4NS4xMi4xLjE3MDgzMzI5ODguMC4wLjA.*_ga_15W4STQT4T*MTcwODMzMjk4NS4yOC4xLjE3MDgzMzI5ODguNTcuMC4w</a></td><td>1.79</td><td>1.79</td></tr><tr><td>1</td><td>XW4H-04A1</td><td>XW4H-04A1</td><td>XW4H-04A1</td><td>N/A</td><td>Pluggable Terminal Blocks PCB Terminal Block Wire Side, 4 con</td><td>Omron</td><td>XW4H-04A1</td><td><a href="https://www.mouser.co.uk/ProductDetail/Omron-Electronics/XW4H-04A1?qs=gqBM4hNBiUXOkrvf5L3rEA%3D%3D&#x26;countryCode=GB&#x26;currencyCode=GBP&#x26;_gl=1*1d1by1q*_ga*ODY2MzE2ODU2LjE2ODk3MDQzMTM.*_ga_15W4STQT4T*MTcwODMzMjk4NS4yOC4xLjE3MDgzMzI5OTEuNTQuMC4w*_ga_1KQLCYKRX3*MTcwODMzMjk4NS4xMi4xLjE3MDgzMzI5OTEuMC4wLjA">https://www.mouser.co.uk/ProductDetail/Omron-Electronics/XW4H-04A1?qs=gqBM4hNBiUXOkrvf5L3rEA%3D%3D&#x26;countryCode=GB&#x26;currencyCode=GBP&#x26;_gl=1*1d1by1q*_ga*ODY2MzE2ODU2LjE2ODk3MDQzMTM.*_ga_15W4STQT4T*MTcwODMzMjk4NS4yOC4xLjE3MDgzMzI5OTEuNTQuMC4w*_ga_1KQLCYKRX3*MTcwODMzMjk4NS4xMi4xLjE3MDgzMzI5OTEuMC4wLjA</a>.</td><td>3.05</td><td>3.05</td></tr><tr><td>1</td><td>PMEG3010EJ</td><td>PMEG3010EJ</td><td>SOD-323</td><td>D3</td><td>Schottky Rectifier, 10 V, 2 A, Single, SOD-323, 2 Pins</td><td>NEXPERIA</td><td></td><td><a href="https://www.mouser.co.uk/ProductDetail/Nexperia/PMEG3010EJ115?qs=LOCUfHb8d9u%252Bcs3tLvEEcA%3D%3D">https://www.mouser.co.uk/ProductDetail/Nexperia/PMEG3010EJ115?qs=LOCUfHb8d9u%252Bcs3tLvEEcA%3D%3D</a></td><td>0.328</td><td>0.328</td></tr><tr><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td>TOTAL</td><td>25.0872667</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

Some tips on soldering the receiver,

* Start with the bottom surface with the slide switch
* Progress to the other small components (capacitors and resistors)
* Place large components before placing the smaller ones around it (e.g. CAN transceiver before its capacitors)
* On the top side, place the LED first, then the pushbutton switches, then the SD socket, then the CAN termination switch, then the Power switch, then the SMA connector, then the MCU chip, then the Omron connector and finally the JST-XH connector.





## Sensors

#### IMU

The IMU shield uses the Bosch BNO055 breakout board from Adafruit, soldered onto a shield board.

It is the simplest of the sensor shields, showcasing its use case with a typical sensor breakout board.

&#x20;The shield only connects the power lines (5V and GND) as well as the I2C communication lines (SDA and SCL) of the sensor to the node computer.

<figure><img src=".gitbook/assets/Screenshot 2024-09-15 at 19.37.31.png" alt="" width="375"><figcaption><p>Exploded view of the IMU node, with the sensor shield on top.</p></figcaption></figure>

***

#### GPS

The GPS is not a shield but a breakout board directly connected via the Qwiic connector on both the sensor and node computer. This cable connects the power and I2C lines.

<figure><img src=".gitbook/assets/IMG_5613.jpg" alt="" width="563"><figcaption><p>Test bench showcasing the GPS connected to the node computer through the Qwiic cable.</p></figcaption></figure>

***

#### Power meter (Voltage and Current)

{% hint style="danger" %}
It is recommended to re-design this shield as it presents a few flaws, most notably not enough protection and confusing layout making it hard to use.

The components were prone to failure (fried) when used with the hydrogen setup during testing and competition.

More on this in the [future work section](in-the-vehicle.md#future-work).
{% endhint %}

The power meter, sometimes referred to as the voltmeter or current meter, can measure either two voltages at the same time or be slightly modified to connect to an analog current transducer to measure one current. It is not possible to measure a voltage and current at the same time.

It uses a TI ADS1115 chip, which is a 16-bit analog to digital converter (ADC). The reason for using a separate ADC chip to a op-amp to MCU ADC configuration was because the ESP32 ADC is not very good (noisy, low impedance and small range).

The current transducer used is a [LEM HO 50-S/SP30-0100](https://uk.rs-online.com/web/p/current-transformers/1383468).

{% tabs %}
{% tab title="Overview" %}
The shield has two voltage inputs (+ and -, green terminal plug connectors), which can be modified into one power output and one voltage measurement when used with a current transducer.

All the resistors and capacitors on the topside are interchangeable to change the purpose of the sensor, and change the output voltage of the [simple voltage dividers](https://learn.sparkfun.com/tutorials/voltage-dividers/all).

<figure><img src=".gitbook/assets/image.png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (4).png" alt="" width="375"><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Voltage Measurement" %}
{% hint style="warning" %}
This is a guess from my previous work as I do not have access to the shields.

Look at the shield available to figure out if that is indeed how it is configured (i.e. resistor placement and values).
{% endhint %}

To set it up for voltage measurements, the ADS1115 chip is used in [differential mode](#user-content-fn-3)[^3].

* Footprints R9/R4 and C1/C4 are populated by the voltage dividing resistors (yes a resistor on a capacitor footprint), R9 being the first resistors and C1 connecting the measured positive pole to the measured negative pole without grounding it to the node. Since the measured voltage and the node's power are not connected, I think it is better not to connect the grounds. But I'm not 100% sure.
* Footprints R15/R11/R6/R16 are populated with a 0 Ohm resistor, or can just be connected with solder.
  *   The resistor value that were used:

      * R9/R4  -> a low value e.g. 30kOhm
      * C1/C4 -> [a very high value](#user-content-fn-4)[^4] e.g. 1 MOhm

      The goal was to lower the maximum voltage experienced by the supercapacitor (around 32.4V) down to the maximum voltage of the ADC chip (configured as 2V in software) to give us the highest possible measurement resolution.

<figure><img src=".gitbook/assets/image (8).png" alt="" width="200"><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Current Measurement" %}
To set it up for current measurements, the ADS1115 chip is used in [differential mode](#user-content-fn-5)[^5].

The LEM transducer has 4 pins we are connecting to:

* **Power pins**, namely voltage in Uc and GND, routed to connector 1.
  * For this, populating R22 and R2[^6] connect the + and - terminals of connector 1 to +5V and GND respectively.
* **Voltage output pins**, Vout and Vref, routed to connector 2.
  * The ADC compares the Vout[^7] which is a voltage between 0V and Uc (+5V) to Vref which is the reference voltage at Uc/2.
  * For this, populating R4 and C4 with voltage dividing resistors in order to bring the 5V maximum output down to the maximum of ADC chip. This is setup just like a normal voltage measurement in differential mode.
    * Footprints R6/R16 are populated with a 0 Ohm resistor, or can just be connected with solder.

{% hint style="warning" %}
The current transducer senses the electro-magnetic field around a wire.

It was observed that the measurements could sometimes be offset by a fixed value when large currents passed through the wire within a short period of time.

Therefore, we implemented a re-calibration feature in software.

1. The node calibrates the sensor to 0.0A on startup so make sure no current is flowing on whatever you are measuring.
2. In the serial monitor command line, write the letter 'c' without the ' ' to start the calibration process to a maximum current value currently running in the wire and that is know (use an adequate power supply). You will need to enter the current value in Amperes that you are calibrating to.
3. The node automatically recalibrates the sensor to 0.0A when a shift from the initial calibration is detected within a small range around 0 (e.g. +/- 300 bits, which can represent a few mA). This is to counteract the shift/offset effect of the sensor, but it does not solve the problem on drastic shifts.
{% endhint %}
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Schematics" %}
**Electrical diagram**

{% file src=".gitbook/assets/Power Shield Schematic.pdf" %}

**PCB footprint**

{% file src=".gitbook/assets/Power Shield PCB Footprint.pdf" %}
{% endtab %}

{% tab title="3D Viewer" %}
{% embed url="https://a360.co/3AmoNJs" %}
{% endtab %}

{% tab title="Parts list (BOM)" %}
<table><thead><tr><th width="87">Qty</th><th width="87">Value</th><th width="87">Device</th><th width="87">Package</th><th width="87">Parts</th><th width="87">Description</th></tr></thead><tbody><tr><td>6</td><td></td><td>C_CHIP-0805(2012-METRIC)</td><td>CAPC2012X110</td><td>C1</td><td></td></tr><tr><td>1</td><td></td><td>PINHD-1X5</td><td>1X05</td><td>JP3</td><td>PIN HEADER</td></tr><tr><td>1</td><td></td><td>PINHD-1X6</td><td>1X06</td><td>JP1</td><td>PIN HEADER</td></tr><tr><td>4</td><td></td><td>R_CHIP-0805(2012-METRIC)</td><td>RESC2012X65</td><td>R6</td><td></td></tr><tr><td>2</td><td>(NC)</td><td>C_CHIP-0805(2012-METRIC)</td><td>CAPC2012X110</td><td>C2</td><td></td></tr><tr><td>1</td><td>(NC) 10k</td><td>R_CHIP-0805(2012-METRIC)</td><td>RESC2012X65</td><td>R21</td><td>Resistor Fixed - Generic</td></tr><tr><td>8</td><td>0 (NC)</td><td>R_CHIP-0805(2012-METRIC)</td><td>RESC2012X65</td><td>R2</td><td></td></tr><tr><td>3</td><td>0.1uF</td><td>C_CHIP-0805(2012-METRIC)</td><td>CAPC2012X110</td><td>C3</td><td></td></tr><tr><td>4</td><td>10k</td><td>R_CHIP-0805(2012-METRIC)</td><td>RESC2012X65</td><td>R17</td><td></td></tr><tr><td>2</td><td>2P 5.08mm pitch</td><td>2828XX-2282837-2</td><td>TERMBLK_508-2N</td><td>J1</td><td></td></tr><tr><td>2</td><td>3.3k</td><td>R_CHIP-0805(2012-METRIC)</td><td>RESC2012X65</td><td>R5</td><td></td></tr><tr><td>2</td><td>43k</td><td>R_CHIP-0805(2012-METRIC)</td><td>RESC2012X65</td><td>R4</td><td></td></tr><tr><td>1</td><td>ADS1115IDGSR</td><td>ADS1115IDGSR</td><td>SOP50P490X110-10N</td><td>IC2</td><td>16-Bit 860SPS 4-Ch Delta-Sigma ADC With PGA</td></tr><tr><td>2</td><td>Fuse</td><td>R_CHIP-0603(1608-METRIC)</td><td>RESC1608X60</td><td>R1</td><td></td></tr><tr><td>2</td><td>MCP6401T-E/OT</td><td>LMH6629_SOT23</td><td>SOT95P280X145-5N</td><td>IC1</td><td></td></tr><tr><td>1</td><td>SOT23 TVS DIODE</td><td>CDSOT23-T24CAN</td><td>SOT95P230X117-3N</td><td>D1</td><td>Bourns CDSOT23-T24CAN</td></tr><tr><td>4</td><td>TEST-POINT3X5</td><td>TEST-POINT3X5</td><td>PAD.03X.05</td><td>TP-PWM-1</td><td></td></tr></tbody></table>
{% endtab %}
{% endtabs %}

***

#### Driver display

The driver display ([4D Systems gen4-uLCD-70D](https://resources.4dsystems.com.au/datasheets/diablo16/gen4-uLCD-70D-series/)) acts as a sensor node, as the screen is connected via a 30-pin FPC cable to a shield which communicates with the node computer and thus the CAN bus.

This shield is a copy of the screen manufacturer's [own module](https://4dsystems.com.au/products/gen4-ib/#Description), and was designed after looking at their schematics.

{% tabs %}
{% tab title="Schematics" %}
Electrical Diagram

{% file src=".gitbook/assets/gen4-IB shield schematic.pdf" %}

PCB footprint

{% file src=".gitbook/assets/gen4-IB shield pcb layout.pdf" %}
{% endtab %}

{% tab title="3D Viewer" %}
{% embed url="https://a360.co/3UKuSaM" %}
{% endtab %}

{% tab title="Parts list (BOM)" %}
| Qty | Value                    | Device                    | Package         | Parts | Description                                                   | Manufacturer | Link                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | Unit (£) | Total (£) |
| --- | ------------------------ | ------------------------- | --------------- | ----- | ------------------------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | --------- |
| 1   |                          | PINHD-1X5                 | 1X05            | JP3   | PIN HEADER                                                    | n/a          | n/a                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | n/a      | #VALUE!   |
| 1   |                          | PINHD-1X6                 | 1X06            | JP1   | PIN HEADER                                                    | n/a          | n/a                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | n/a      | #VALUE!   |
| 3   | 0                        | R\_CHIP-0805(2012-METRIC) | RESC2012X65     | R4    | Resistor Fixed - Generic                                      | n/a          | n/a                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | n/a      | #VALUE!   |
| 1   | 0.1 uF                   | C\_CHIP-0805(2012-METRIC) | CAPC2012X110    | C1    | Capacitor - Generic                                           | n/a          | n/a                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | n/a      | #VALUE!   |
| 1   | 10uF                     | C\_CHIP-0805(2012-METRIC) | CAPC2012X110    | C2    | Capacitor - Generic                                           | n/a          | n/a                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | n/a      | #VALUE!   |
| 1   | 2P 2.54mm Screw Terminal | 2828XX-2282834-2          | TERMBLK\_254-2N | J2    | 2 Position Wire to Board Terminal Block Horizontal with Board | n/a          | [https://www.amazon.co.uk/Screw-Terminal-Connector-2-54mm-Universal/dp/B07QLX73YP/ref=sr\_1\_2?crid=3CL0CRF3J3XYF\&dib=eyJ2IjoiMSJ9.LbbBpfKYYd4D5dkjRLcxj-gghuS2R6IM0szEhA3OHVHfn4EjFVEgds2HuJqN2-P7Qjboh0Ml7oAVJ5ja5ESzpyAxvcU1fATTIdL92SoxXLcmprO9iZYqXb1QcrCb21dF4cjKoVk9JtTWVB3m\_JmGJpnee\_eKV\_X8Ma7lRmQvp6xp-6\_2Nz04zg9o4ooo2TfmCOvKA8GKFfP9Z6k5e49UMm0wHO0I85lDTXMXh\_x-lftTK1N3MDY-98eW-tgjbEtG7ArG6NllpZu6R71XRHyFFYaBFDTsqyIZnzfKrzSZcSM.bqkXbXQGBBCX7BueQvEKqf1DWG2CGzGP1cy7PCJWkG4\&dib\_tag=se\&keywords=screw+terminal+2pos+2.54mm\&qid=1708427228\&sprefix=screw+terminal+2pos+2.54mm,aps,78\&sr=8-2](https://www.amazon.co.uk/Screw-Terminal-Connector-2-54mm-Universal/dp/B07QLX73YP/ref=sr\_1\_2?crid=3CL0CRF3J3XYF\&dib=eyJ2IjoiMSJ9.LbbBpfKYYd4D5dkjRLcxj-gghuS2R6IM0szEhA3OHVHfn4EjFVEgds2HuJqN2-P7Qjboh0Ml7oAVJ5ja5ESzpyAxvcU1fATTIdL92SoxXLcmprO9iZYqXb1QcrCb21dF4cjKoVk9JtTWVB3m\_JmGJpnee\_eKV\_X8Ma7lRmQvp6xp-6\_2Nz04zg9o4ooo2TfmCOvKA8GKFfP9Z6k5e49UMm0wHO0I85lDTXMXh\_x-lftTK1N3MDY-98eW-tgjbEtG7ArG6NllpZu6R71XRHyFFYaBFDTsqyIZnzfKrzSZcSM.bqkXbXQGBBCX7BueQvEKqf1DWG2CGzGP1cy7PCJWkG4\&dib\_tag=se\&keywords=screw+terminal+2pos+2.54mm\&qid=1708427228\&sprefix=screw+terminal+2pos+2.54mm,aps,78\&sr=8-2) | 0.1712   | 0.1712    |
| 1   | 4.7k                     | R\_CHIP-0805(2012-METRIC) | RESC2012X65     | R7    | Resistor Fixed - Generic                                      | n/a          | n/a                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | n/a      | #VALUE!   |
| 3   | 68                       | R\_CHIP-0805(2012-METRIC) | RESC2012X65     | R1    | Resistor Fixed - Generic                                      | n/a          | n/a                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | n/a      | #VALUE!   |
| 1   | SFV30R-4STBE1HLF         | SFV30R-4STBE1HLF          | SFV30R4STBE1HLF | J1    | 0.50mm Flex Connector                                         | Amphenol     | [https://www.mouser.co.uk/ProductDetail/Amphenol-FCI/SFV30R-4STBE1HLF?qs=SqJKR5MmryfVMDRs/3Q1Tw%3D%3D](https://www.mouser.co.uk/ProductDetail/Amphenol-FCI/SFV30R-4STBE1HLF?qs=SqJKR5MmryfVMDRs/3Q1Tw%3D%3D)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | 0.6      | 0.6       |
{% endtab %}
{% endtabs %}

It also features two holes that can be used for a screw terminal to attach a toggle switch. This toggle switch can be programmed with the MCU to perform logic operations such as dimming the lights of the screen.

#### micro-SD

The micro-SD shield allows the user to transform a node into a CAN datalogger device.

Connect it to the node computer, attach it to the CAN bus and program it to record any CAN message sent.

{% hint style="warning" %}
Make sure to configure the datalogger node in LISTEN ONLY mode of CAN if the receiver is also attached to the bus, otherwise it will not be able to register messages.
{% endhint %}

{% tabs %}
{% tab title="Schematics" %}
Electrical diagram

{% file src=".gitbook/assets/microSD shield v1ra schematic.pdf" %}

PCB layout

{% file src=".gitbook/assets/microSD shield v1ra pcb.pdf" %}
{% endtab %}

{% tab title="3D Viewer" %}
{% embed url="https://a360.co/48m8Cr2" %}
{% endtab %}

{% tab title="Parts list (BOM)" %}
| Qty | Value               | Device                    | Package         | Parts | Description        | Manufacturer | Link                                                                                                                                                                                             | Unit (£) | Total (£) |
| --- | ------------------- | ------------------------- | --------------- | ----- | ------------------ | ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- | --------- |
| 1   | PINHD-1X5           | 1X05                      | JP3             | PIN   | HEADER             | n/a          | n/a                                                                                                                                                                                              | n/a      | #VALUE!   |
| 1   | PINHD-1X6           | 1X06                      | JP1             | PIN   | HEADER             | n/a          | n/a                                                                                                                                                                                              | n/a      | #VALUE!   |
| 1   | 0                   | R\_CHIP-0805(2012-METRIC) | RESC2012X65     | R5    | Resistor           | n/a          | n/a                                                                                                                                                                                              | n/a      | #VALUE!   |
| 1   | 0.1uF               | C\_CHIP-0805(2012-METRIC) | CAPC2012X110    | C1    | Capacitor          | n/a          | n/a                                                                                                                                                                                              | n/a      | #VALUE!   |
| 5   | 10k                 | R\_CHIP-0805(2012-METRIC) | RESC2012X65     | R4,   | Resistor           | n/a          | n/a                                                                                                                                                                                              | n/a      | #VALUE!   |
| 1   | MEM2075-00-140-01-A | MEM2075-00-140-01-A       | MEM20750014001A | J2    | uSD card connector | GCT          | [https://www.mouser.co.uk/ProductDetail/GCT/MEM2075-00-140-01-A?qs=KUoIvG/9IlZvfWpeERlq3Q%3D%3D](https://www.mouser.co.uk/ProductDetail/GCT/MEM2075-00-140-01-A?qs=KUoIvG/9IlZvfWpeERlq3Q%3D%3D) | 1.58     | 1.58      |
{% endtab %}
{% endtabs %}

***

#### RFM69(HCW)

Similar to the datalogger node, there can also be a radio node.

{% tabs %}
{% tab title="Schematics" %}
**Electrical diagram**

{% file src=".gitbook/assets/RFM69HCW Shield Schematic.pdf" %}

**PCB Footprint**

{% file src=".gitbook/assets/RFM69HCW PCB Footprint.pdf" %}
{% endtab %}

{% tab title="3D Viewer" %}
{% embed url="https://a360.co/3OLsfBB" %}
{% endtab %}

{% tab title="Parts list (BOM)" %}
| Qty | Value     | Device                    | Package         | Parts | Description                   | Manufacturer | Link                                                                                                                                                                         | Unit (£) | Total (£) |
| --- | --------- | ------------------------- | --------------- | ----- | ----------------------------- | ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | --------- |
| 1   |           | PINHD-1X5                 | 1X05            | JP3   | PIN HEADER                    | n/a          | n/a                                                                                                                                                                          | n/a      | #VALUE!   |
| 1   |           | PINHD-1X6                 | 1X06            | JP1   | PIN HEADER                    | n/a          | n/a                                                                                                                                                                          | n/a      | #VALUE!   |
| 2   | 100k      | R\_CHIP-0603(1608-METRIC) | RESC1608X60     | R1    | Resistor - Generic            | n/a          | n/a                                                                                                                                                                          | n/a      | #VALUE!   |
| 1   | 10uF      | C\_CHIP-0603(1608-METRIC) | CAPC1608X85     | C1    | Capacitor - Generic           | n/a          | n/a                                                                                                                                                                          | n/a      | #VALUE!   |
| 2   | NC        | R\_CHIP-0603(1608-METRIC) | RESC1608X60     | R2    | Resistor - Generic            | n/a          | n/a                                                                                                                                                                          | n/a      | #VALUE!   |
| 1   | 14        | SMACONNECTOR\_EDGE        | SMA\_EDGELAUNCH | X1    | SMA Connector                 | n/a          | n/a                                                                                                                                                                          | n/a      | #VALUE!   |
| 1   | COM-13909 | COM-13909                 | MOD\_COM-13909  | U2    | Rfm69hcw Wireless Transceiver | HOPERF       | [https://www.mouser.co.uk/ProductDetail/Adafruit/5693?qs=mELouGlnn3fnO8nGmYJgWQ%3D%3D](https://www.mouser.co.uk/ProductDetail/Adafruit/5693?qs=mELouGlnn3fnO8nGmYJgWQ%3D%3D) | 4.76     | 4.76      |
{% endtab %}
{% endtabs %}





## 2024 configuration

The 2024 UCL vehicle had the following nodes,

* (x1) voltage sensor
  * on the super-capacitor poles to measure the State-Of-Charge of the super-capacitor. The SOC was calculated by multiplying the measured voltage (0 to 32.4V) by 3.08642.
  * on the DC-DC output.
* (x3) current sensors
  * on the super-capacitor output.
  * on the motor controller output to the motor.
  * on the DC-DC output.

{% hint style="info" %}
Note that because of how the voltmeter shield was designed, it can be used to measure two voltages at the same time, or re-arranged to measure one current sensor.
{% endhint %}

* An Inertial Measurement Unit (IMU) - placed close to the centre of mass
* A GPS - with antenna



<figure><img src=".gitbook/assets/image (7).png" alt="" width="375"><figcaption><p>Outside view of the vehicle loom</p></figcaption></figure>

<div align="center" data-full-width="true">

<figure><img src=".gitbook/assets/image (6).png" alt="" width="375"><figcaption><p>The placement during competition became very difficult due to the space constraints</p></figcaption></figure>

</div>

* An LCD screen - for the driver

<figure><img src=".gitbook/assets/image (5).png" alt="" width="375"><figcaption><p>Driver display showing a test setup</p></figcaption></figure>



### Future Work

#### New power shield (Voltage + Current)

The voltage shields failed multiple times at the competition.

Sometimes it was only the sensor chip that blew, other times the whole node died (sensor and node computer). We were able to tell the sensor died because when connecting looking at the serial monitor it was showing that it couldn't find any I2C devices on the bus.

The exact reason for the failures is unknown, but there were problems with un-even groundings and the whole chassis of the car being grounded (i.e. you could measure a voltage when touching the carbon fibre). This would mean that, although the theoretical maximum voltage differentials of the sensors were not reached, they may have been destroyed from some electro-static discharge of some kind.

Thus, for future years it is necessary to redesign this sensor shield to incorporate as much protection as possible (ask William Backhouse in MechSpace about isolation circuitry using an isolation amplifier for a voltmeter/current-meter). The redesign should also - if possible - try to make it easier for the user to measure a voltage either by better suited connector plug and sockets (currently pluggable Phoenix terminal blocks) and make it easier to use in general (e.g. making changing the voltage divider layout more obvious and easy to understand).

{% hint style="info" %}
Note: if you're making methods to add 0 Ohm resistors as connectors, consider using special footprints that allow you to easily connect traces with solder. In our implementation we used normal footprints with solder which was often difficult to bridge. Adafruit/Sparkfun probably have some footprints.
{% endhint %}

#### Smaller nodes

The obvious improvement to the system is to miniaturise it, as it is quite bulky and inconvenient as-is.

This would mean copying the PCB design of the XIAO microcontrollers, but removing all the breakout board aspects to only be left with the critical components. You could then place them all to use the less amount of space, and overall get rid of all the pointless switches (power source switching) and accessibility features (termination resistor switch) of the current node computer design.&#x20;

It is important to keep and add all safety features.

<figure><img src=".gitbook/assets/smaller node.png" alt="" width="375"><figcaption><p>Quick drawing of a miniaturised design with a bare ESP32-C3 chip.</p></figcaption></figure>

#### New Driver Display

The driver display is a major part of the current consumption of the system, taking it directly from whatever power source that the vehicle has (e.g. auxiliary 12V battery).

From memory, it was required in the 2024 competition rules that everything be powered from the same source, thus the telemetry system would take from the aux battery charged by the fuel cell, impacting the energy performance.

I think the Politecnico di Torino team used a smartphone as a screen, telemetry communication with the pits and saving device instead of having them all separate.

It is true in hindsight that that is a more viable option if you can get serial communication to work between a simple node computer and an Android phone.

1. Is inherently powered by the phone battery, thus the whole system impacting the performance much much less. This means using at least 3.5Wh less than currently.
   * The competition may not be able to enforce that the phone be counted in the power usage of the vehicle, because it could be the same phone used by the driver to communicate with the race engineer.
2. Better display overall: better brightness/resolution, more processing power, potentially easier to program.
3. So much easier to install in the car, would just need a phone holder and a USB-C cable.
4. Actually cheaper than the 4D Systems display if you get a low budget android smartphone.

So the plan would be that the phone is connected to a node computer via the USB-C port, and communicates over serial.&#x20;

The node computer would send the necessary data it receives similarly to how the [radio station](outside-the-vehicle.md#radio-station) is communicating with the race engineer's software, but here software on the phone receives, processes and displays it on a purpose-built app to the driver.&#x20;

The phone would simultaneously transmit the data of 4G/5G to a server which the race engineer connects to. This could be a webpage.

The phone would also save all the data on-board in a text or csv file, similar to how it is doing it on the SD card. This may even be way faster than currently.

And finally the driver would also be calling the race engineer with that same phone and some wireless earbuds, over 4G.



This is quite a big software project of its own, which may require someone to work on it for a few months.





[^1]: This is the name for an arduino project, essentially the code you upload to a microcontroller.

[^2]: This is the name for an arduino project, essentially the code you upload to a microcontroller.

[^3]: Compares voltage on one line to the other, necessary as measured ground may be at different level to node ground.

[^4]: to have a very high impedance to minimise current draw -> minimal disturbance to the measured system

[^5]: Compares voltage on one line to the other, necessary as measured ground may be at different level to node ground.

[^6]: Board's underside

[^7]: The sensor detects current in both directions by setting the 0.0A value at Uc/2. Currents above this reference indicate positive flow, while currents below it represent negative flow.
