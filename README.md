# esp8266-basic-setup

This repo documents a basic setup for working with the ESP8266. It is intended to be a starting point for new projects. Specifically, this is for the [Adafruit Feather Huzzah](https://www.adafruit.com/product/2821) (`esp8266:esp8266:huzzah`).

## Initial setup

This is the stuff you'll likely just have to do once.

### Install Arduino CLI

```sh
brew update
brew install arduino-cli
```

### Configure Arduino CLI

```sh
arduino-cli config init
```

This outputs a file to `/Users/<username>/Library/Arduino15/arduino-cli.yaml`, which looks like this:

```yaml
board_manager:
  additional_urls: []
daemon:
  port: "50051"
directories:
  data: /Users/<username>/Library/Arduino15
  downloads: /Users/<username>/Library/Arduino15/staging
  user: /Users/<username>/Documents/Arduino
library:
  enable_unsafe_install: false
logging:
  file: ""
  format: text
  level: info
metrics:
  addr: :9090
  enabled: true
output:
  no_color: false
sketch:
  always_export_binaries: false
updater:
  enable_notification: true
```

More info on [configuration](https://arduino.github.io/arduino-cli/0.28/configuration/).

### Configure board manager for esp8266

Update config file with:

```yaml
board_manager:
  additional_urls:
    - https://arduino.esp8266.com/stable/package_esp8266com_index.json
```

### Update board index

```sh
arduino-cli core update-index
```

Connect your board and check for it:

```sh
arduino-cli board list
```

If you're not sure which it is, try disconnecting, running the above, reconnecting, and running it again, then compare. Likely it'll be named `/dev/cu.usbserial-<ID>`.

For ESP8266 you'll likely get unknown FQBN. For the Huzzah it's `esp8266:esp8266:huzzah`.

### Install the board core

You have to install the board core. See [this](https://arduino.github.io/arduino-cli/0.28/getting-started/#install-the-core-for-your-board) for more details.

Search for esp8266 cores (or others) like so:

```sh
arduino-cli core search esp8266
```

And then install, like so:

```sh
arduino-cli core install esp8266:esp8266
```

## Per project

This is the stuff you'll do for new projects. You may want to repeat some of the steps above for updates.

### New sketch

Initialise a new "sketch":

```sh
arduino-cli sketch new ExampleSketch
```

This will create a directory `ExampleSketch` in the cwd with a `ExampleSketch.ino` file inside with:

```ino
void setup() {
}

void loop() {
}
```

For an example, we'll use:

```ino
void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH);
  delay(1000);
  digitalWrite(LED_BUILTIN, LOW);
  delay(1000);
}
```

### Compile and upload the sketch

```sh
arduino-cli compile --fqbn esp8266:esp8266:huzzah ExampleSketch
```

This gives:

```
$ arduino-cli compile --fqbn esp8266:esp8266:huzzah ExampleSketch
Executable segment sizes:
ICACHE : 32768           - flash instruction cache
IROM   : 231724          - code in flash         (default or ICACHE_FLASH_ATTR)
IRAM   : 26793   / 32768 - code in IRAM          (IRAM_ATTR, ISRs...)
DATA   : 1496  )         - initialized variables (global, static) in RAM/HEAP
RODATA : 876   ) / 81920 - constants             (global, static) in RAM/HEAP
BSS    : 25608 )         - zeroed variables      (global, static) in RAM/HEAP
Sketch uses 260889 bytes (24%) of program storage space. Maximum is 1044464 bytes.
Global variables use 27980 bytes (34%) of dynamic memory, leaving 53940 bytes for local variables. Maximum is 81920 bytes.


Used platform   Version Path
esp8266:esp8266 3.0.2   /Users/<username>/Library/Arduino15/packages/esp8266/hardware/esp8266/3.0.2
```

To upload the sketch to your board, run the following command, using the serial port your board is connected to.

Remember, `arduino-cli board list` to find the serial port in format `/dev/<port>`.

```sh
arduino-cli upload -p /dev/cu.usbserial-01D231D2 --fqbn esp8266:esp8266:huzzah ExampleSketch
```

Output:

```
esptool.py v3.0
Serial port /dev/cu.usbserial-01D231D2
Connecting....
Chip is ESP8266EX
Features: WiFi
Crystal is 26MHz
MAC: 50:02:91:68:87:ca
Uploading stub...
Running stub...
Stub running...
Configuring flash size...
Auto-detected Flash size: 4MB
Compressed 265040 bytes to 195057...
Wrote 265040 bytes (195057 compressed) at 0x00000000 in 17.1 seconds (effective 123.7 kbit/s)...
Hash of data verified.

Leaving...
Hard resetting via RTS pin...
```

### Adding libraries

See [this](https://arduino.github.io/arduino-cli/0.28/getting-started/#add-libraries) for info on adding libs.
