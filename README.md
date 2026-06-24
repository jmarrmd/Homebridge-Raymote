<p align="center">
  <img src="https://raw.githubusercontent.com/homebridge/branding/latest/logos/homebridge-color-round-stylized.png" width="120">
</p>

<h1 align="center">homebridge-raymote</h1>

<p align="center">
  Control and monitor your <strong>Raypak</strong> pool/spa heater from Apple HomeKit, via the Raymote cloud API.
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/homebridge-raymote"><img src="https://img.shields.io/npm/v/homebridge-raymote.svg" alt="npm version"></a>
  <a href="https://www.npmjs.com/package/homebridge-raymote"><img src="https://img.shields.io/npm/dt/homebridge-raymote.svg" alt="npm downloads"></a>
  <a href="https://github.com/jmarrmd/Homebridge-Raymote/blob/main/LICENSE"><img src="https://img.shields.io/npm/l/homebridge-raymote.svg" alt="license"></a>
  <a href="https://github.com/homebridge/homebridge/wiki/Verified-Plugins"><img src="https://img.shields.io/badge/homebridge-plugin-blue.svg" alt="homebridge"></a>
</p>

---

This [Homebridge](https://homebridge.io) plugin connects your Raypak heater to HomeKit using the [Raymote](https://raymote.raypak.com) cloud service. It exposes a single **Thermostat** accessory so you can:

- 🌡️ **See the current water temperature** (read from the heater's inlet sensors)
- 🔥 **See when the heater is actively firing** vs. idle
- 🎚️ **Set the target temperature** (50–104 °F)
- 🔌 **Turn the heater on and off** (`Off` / `Heat` modes)
- 🏡 **Use Siri, automations, and scenes** like any other HomeKit thermostat

> **Note:** This is an unofficial, community-built plugin. It is not affiliated with or endorsed by Raypak. It talks to the same Raymote cloud API your phone app uses, so the heater must be online and connected to Raymote.

## Requirements

- [Homebridge](https://homebridge.io) v1.3.0 or newer
- Node.js v14 or newer
- A Raypak heater registered with the [Raymote](https://raymote.raypak.com) service
- A Raymote **API token** (see [Getting your token](#getting-your-token))

## Installation

### Homebridge UI (recommended)

1. Open the **Homebridge UI** and go to the **Plugins** tab.
2. Search for **`homebridge-raymote`**.
3. Click **Install**.
4. Open the plugin settings, enter your Raymote token, and save.
5. Restart Homebridge.

### Command line

```bash
npm install -g homebridge-raymote
```

## Configuration

The easiest way to configure is through the Homebridge UI settings screen — just enter your token and you're done.

If you prefer to edit `config.json` manually, add a platform block:

```json
{
  "platforms": [
    {
      "platform": "RaymotePlatform",
      "name": "Raymote Pool Heater",
      "token": "YOUR_TOKEN_HERE"
    }
  ]
}
```

### Options

| Option         | Required | Default                                      | Description                                                                 |
| -------------- | :------: | -------------------------------------------- | --------------------------------------------------------------------------- |
| `platform`     |   yes    | —                                            | Must be `RaymotePlatform`.                                                   |
| `token`        |   yes    | —                                            | Your Raymote API token. Keep this secret. See [below](#getting-your-token). |
| `name`         |    no    | `Raymote Pool Heater`                        | The accessory name shown in HomeKit.                                         |
| `baseUrl`      |    no    | `https://raymote.raypak.com/external/api`    | Raymote API base URL. Only change this if you know you need to.              |
| `pollInterval` |    no    | `30`                                         | How often (in seconds) to poll the heater for updates. Minimum `5`.         |

### Getting your token

This plugin authenticates with the Raymote service using an API token tied to your account/heater. Enter it in the **Raymote Token** field in the plugin settings (or the `token` field in `config.json`). Treat the token like a password — anyone with it can control your heater.

> If you're unsure how to obtain your token, check your Raymote account/app or contact Raypak/Raymote support. Open an [issue](https://github.com/jmarrmd/Homebridge-Raymote/issues) if you'd like help.

## How it works

On startup the plugin registers a single Thermostat accessory and then polls the Raymote API every `pollInterval` seconds. HomeKit characteristics map to the heater as follows:

| HomeKit characteristic        | Source                                                            |
| ----------------------------- | ----------------------------------------------------------------- |
| Current Temperature           | Heater inlet temperature sensor                                   |
| Target Temperature            | Heater setpoint (settable, 50–104 °F)                             |
| Current Heating/Cooling State | `Heat` while the heater is actively firing, otherwise `Off`       |
| Target Heating/Cooling State  | `Heat` when the heater is enabled, `Off` when disabled (settable) |

Temperatures are exchanged with HomeKit in Celsius internally (HomeKit's native unit) but the heater operates in Fahrenheit, so the plugin converts between the two automatically. Only **Off** and **Heat** modes are exposed — Cool and Auto are intentionally hidden because the heater can't cool.

> **Target vs. Current state:** HomeKit shows *Heating* (active) only when the heater is physically firing. If you turn the heater on but the water is already at temperature, the mode reads *Off* until it starts firing again — this is expected.

## Troubleshooting

- **Accessory never appears / "Plugin is not configured: Raymote Token is missing."** — The `token` is empty. Add it in the plugin settings and restart Homebridge.
- **No data / values not updating** — The heater may be offline or unreachable from the Raymote cloud. Confirm it works in the official Raymote app first. Enable Homebridge debug mode (`homebridge -D`) to see fetch errors.
- **Temperature changes don't stick** — Setpoint changes are sent to the cloud, then confirmed on the next poll. Give it a poll cycle (`pollInterval` seconds) to reflect.

Run Homebridge in debug mode to see detailed logs:

```bash
homebridge -D
```

## Contributing

Issues and pull requests are welcome — please open them on the [GitHub repository](https://github.com/jmarrmd/Homebridge-Raymote).

## License

[MIT](LICENSE) © Josh Marr

---

<p align="center"><sub>Raypak® and Raymote™ are trademarks of their respective owners. This project is not affiliated with Raypak.</sub></p>
