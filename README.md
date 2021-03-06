# LutronCasetaLeap

[![verified-by-homebridge](https://badgen.net/badge/homebridge/verified/purple)](https://github.com/homebridge/homebridge/wiki/Verified-Plugins)

This is a plugin that interfaces between Homebridge (and HomeKit) and the [Lutron Caséta Smart Bridge](https://www.casetawireless.com/products/expansion-kits-and-smart-bridge). Specifically, this adds support for the [Serena Smart Wood Blinds](https://www.serenashades.com), which is all I care about at the moment. :)

This is specifically _not_ for the Smart Bridge Pro, which uses (can use) an entirely different protocol. This plugin makes use of the [lutron-leap-js](https://github.com/thenewwazoo/lutron-leap-js) library, which implements the Lutron LEAP protocol, used by the Lutron mobile apps.

## Preparation

### Get your bridge ID

The bridge ID is the serial number, and is printed on the underside of the bridge, indicated here in green:

![picture of the underside of a bridge, showing the sticker with the ID on it](assets/bridge.png)

### Get your bridge credentials

The easiest way is to use the [`get_lutron_cert.py`](https://github.com/gurumitts/pylutron-caseta/blob/dev/get_lutron_cert.py) script that comes with the [`pylutron-caseta`](https://github.com/gurumitts/pylutron-caseta) project, without which this would not have been possible.

Each file corresponds to a configuration item:
* `caseta-bridge.crt` => CA certificate
* `caseta.key` => Private key
* `caseta.crt` => Signed certificate

## Installation

Either run `npm -i homebridge-lutron-caseta-leap` in your Homebridge installation directory or install it using [`homebridge-config-ui-x`](https://github.com/oznu/homebridge-config-ui-x#readme).

## Configuration

Each bridge on your network that you wish to control needs its own configuration entry.

### Using `homebridge-config-ui-x`

If you're using the GUI, you'll want to fill out the form, copying and pasting in the appropriate fields:

![a screenshot of the configuration UI, with fields filled with the contents of certificate files](assets/config.png)

The UI will handle multi-line input, so just paste it in.

Click the "ADD SECRETS" button to add additional bridges.

### Using `config.json`

The shape of the configuration is:

```json
{
    "platform": "LutronCasetaLeap",
        "secrets": [
            {
                "bridgeid": "0a1b2c3d",
                "ca": "-----BEGIN CERTIFICATE-----\nMII...",
                "key": "-----BEGIN PRIVATE KEY-----\nMII...",
                "cert": "-----BEGIN CERTIFICATE-----\nMII..."
            },
            { ... }
        ]
}
```

The authn strings are newline-escaped versions of the files you generated.

## To-do and contributions

I welcome contributions! I wrote this to scratch an itch (no Serena wood blind support), but would be fine with additional devices. To add a device:
* Add a case to `LutronCasetaLeap.configureAccessory` and `handleBridgeDiscovery` for your new device type.
* Add a class for your device. The constructor should wire up event handlers and manage services and characteristics.
* Add relevant methods to the [`SmartBridge`](https://github.com/thenewwazoo/lutron-leap-js/blob/main/src/SmartBridge.ts) class in the `lutron-leap-js` project to control the device. These methods should construct command requests.
* Add an "unsolicited events" handler in your new device class to update itself when new information arrives from the bridge, and to update characteristic values.
