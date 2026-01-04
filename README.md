# ESPHome E2271KS0C1 E-Paper Display Driver

ESPHome external component for the [Pervasive Displays E2271KS0C1 2.7" e-paper display](https://www.pervasivedisplays.com/product/2-71-e-ink-displays/#wide-temperature-fast-refresh) (176x264 pixels), as used in the [Bitclock](https://github.com/goat-hill/bitclock) project.

## Features

- Full and fast (partial) update modes
- Configurable update interval and full refresh frequency
- Dark mode support (inverted colors)
- 12h/24h time format
- Celsius/Fahrenheit temperature display
- WiFi signal strength indicator

## Installation

Choose the installation method based on your setup:

### Option A: Home Assistant ESPHome Add-on

Use this method if you're running ESPHome as a Home Assistant add-on.

1. **Install the ESPHome add-on**

   In Home Assistant, go to Settings > Add-ons > Add-on Store and install "ESPHome".

2. **Copy the external component to Home Assistant**

   Copy the `components` folder to your Home Assistant config directory as `external_components`:

   ```bash
   scp -r components/* your-homeassistant.local:/config/esphome/external_components
   ```

   Replace `your-homeassistant.local` with your Home Assistant hostname or IP address.

3. **Create your configuration**

   In the ESPHome dashboard, create a new device and use `bitclock.example.yaml` as a reference. The example is pre-configured for Home Assistant.

4. **Add WiFi credentials**

   Add your WiFi credentials to the ESPHome secrets (accessible from the ESPHome dashboard).

5. **Install to device**

   Click "Install" in the ESPHome dashboard to compile and upload to your device.

### Option B: Standalone ESPHome (CLI)

Use this method if you're running ESPHome directly on your computer without Home Assistant.

1. **Install ESPHome CLI**

   ```bash
   pip install esphome
   ```

2. **Create your configuration**

   Copy `bitclock.example.yaml` to your project directory and rename it (e.g., `bitclock.yaml`).

3. **Modify for standalone use**

   Make the following changes in your YAML file:

   - **Time source**: Comment out the `homeassistant` platform and uncomment the `sntp` platform:
     ```yaml
     time:
       # - platform: homeassistant
       #   id: current_time

       - platform: sntp
         id: current_time
         timezone: "America/Los_Angeles"  # Change to your timezone
     ```

   - **External component**: Comment out the `local` source and uncomment the `git` source:
     ```yaml
     external_components:
       # - source:
       #     type: local
       #     path: /config/esphome/external_components
       #   components: [ e2271ks0c1 ]

       - source:
           type: git
           url: https://github.com/gxlabs/esphome-e2271ks0c1
         components: [ e2271ks0c1 ]
     ```

   - **API component**: Optionally remove the `api:` line if you don't need remote API access.

4. **Create a secrets file**

   Create a `secrets.yaml` file in the same directory:
   ```yaml
   wifi_ssid: "your-wifi-name"
   wifi_password: "your-wifi-password"
   ```

5. **Compile and upload**

   ```bash
   esphome run bitclock.yaml
   ```

## Configuration Options

| Option | Default | Description |
|--------|---------|-------------|
| `cs_pin` | required | SPI chip select pin |
| `dc_pin` | required | Data/command pin |
| `reset_pin` | required | Hardware reset pin |
| `busy_pin` | required | Busy status pin (set `inverted: true`) |
| `data_rate` | 4MHz | SPI clock speed |
| `update_interval` | 10s | How often to refresh the display |
| `full_update_every` | 30 | Full refresh every N updates (clears ghosting) |
| `temperature_c` | 25 | Ambient temperature for waveform timing |

## Bitclock Example Configuration

See [bitclock.example.yaml](https://github.com/gxlabs/esphome-e2271ks0c1/blob/main/bitclock.example.yaml) for a complete example with:
- Time display (12h/24h configurable)
- WiFi signal strength icon
- CO2, temperature, humidity, and VOC sensor readings
- Toggleable sensor labels (icons-only mode)
- Material Design Icons
- Dark mode toggle

|Literal screen shot|ESPHome sensors|
|-|-|
|<img src="https://github.com/user-attachments/assets/4aace8f3-e9bc-49de-a069-bb6bac813f6b" width=450 />|<img src="https://github.com/user-attachments/assets/e64ea655-753f-4cdb-8789-05af215b124a" width=450 />|


### Substitutions

The example uses substitutions for easy configuration:

```yaml
substitutions:
  dark_mode: "true"           # White text on black background
  time_24h: "false"           # 12-hour time format
  temp_fahrenheit: "true"     # Temperature in Fahrenheit
  show_sensor_labels: "true"  # Show TEMP/CO2/HUMIDITY/VOC labels
```

## Hardware

This driver is designed for the Pervasive Displays E2271KS0C1 panel with the following pinout (Bitclock PCB):

| Signal | GPIO |
|--------|------|
| SPI CLK | GPIO6 |
| SPI MOSI | GPIO1 |
| CS | GPIO10 |
| DC | GPIO7 |
| RESET | GPIO5 |
| BUSY | GPIO4 |
| I2C SDA | GPIO8 |
| I2C SCL | GPIO9 |

## Credits

The example configuration ([bitclock.example.yaml](https://github.com/gxlabs/esphome-e2271ks0c1/blob/main/bitclock.example.yaml)) extends from [manedfolf/bitclock](https://github.com/manedfolf/bitclock/blob/esphome/bitclock-esphome/bitclock-esphome.yaml) ESPHome configuration.

## License

MIT
