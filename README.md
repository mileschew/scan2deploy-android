# scan2deploy-android

`Scan2Deploy` is an application devoted to the initial staging of Android devices by reading a barcode sequence. Depending on the content of the staging barcodes `Scan2Deploy` will, in this order:

* configure the device Wi-Fi network,
* control some device inner system settings (e.g. the lock-screen enabled state),
* download a data archive from a HTTP/HTTPS server and unpack it to the device,
* install/update applications and the system image, and
* run a customary script (to perform any additional setup).

It's worth noticing that the application is *not* a resident service. The above-mentioned actions are performed as long as `Scan2Deploy` is active and in use.

## Using the latest Scan2Deploy

Scan2Deploy is pre-installed on Android devices. However, if you want to use the latest version instead, you can create a custom Android Enterprise enrollment barcode that pulls a new version from Github (or another location of your choosing). The list of valid fields you can choose from are [documented here](https://developers.google.com/android/work/play/emm-api/prov-devices#create_a_qr_code).  A sample configuration and pre-generated barcode are provided below.

There are several tools you can use to encode your JSON data into a QR code. A few options are provided below.

* [qrencode](https://fukuchi.org/works/qrencode/index.html.en) - Linux command line utility. Example usage: `qrencode -o s2dupdate.png < s2dupdate.json`
* [QR Code Generator](https://kazuhikoarase.github.io/qrcode-generator/js/demo/) - online tool suitable for copy-paste of JSON data.

### Installing the latest version of Scan2Deploy

#### JSON (Latest version)

```json
{
    "android.app.extra.PROVISIONING_DEVICE_ADMIN_COMPONENT_NAME": "com.datalogic.scan2deploy/com.datalogic.scan2deploy.DeviceOwnerReceiver",
    "android.app.extra.PROVISIONING_LEAVE_ALL_SYSTEM_APPS_ENABLED": true,
    "android.app.extra.PROVISIONING_SKIP_ENCRYPTION": true,
    "android.app.extra.PROVISIONING_DEVICE_ADMIN_SIGNATURE_CHECKSUM": "6CG8ls-pzlKcJNeoZtAC-neobD_ypZCI853n_TBGEHI=",
    "android.app.extra.PROVISIONING_DEVICE_ADMIN_PACKAGE_DOWNLOAD_LOCATION": "https://github.com/datalogic/scan2deploy-android/raw/master/Scan2Deploy.apk"
}
```

#### QR Code (Latest version)

![Scan2Deploy v1.9](media/scan2deploy-master.gif)

### Installing a specific version of Scan2Deploy

You can also download a specific release of Scan2Deploy. For example, here is the process for using version `1.9`:

#### JSON (Version 1.9)

```json
{
    "android.app.extra.PROVISIONING_DEVICE_ADMIN_COMPONENT_NAME": "com.datalogic.scan2deploy/com.datalogic.scan2deploy.DeviceOwnerReceiver",
    "android.app.extra.PROVISIONING_LEAVE_ALL_SYSTEM_APPS_ENABLED": true,
    "android.app.extra.PROVISIONING_SKIP_ENCRYPTION": true,
    "android.app.extra.PROVISIONING_DEVICE_ADMIN_SIGNATURE_CHECKSUM": "6CG8ls-pzlKcJNeoZtAC-neobD_ypZCI853n_TBGEHI=",
    "android.app.extra.PROVISIONING_DEVICE_ADMIN_PACKAGE_DOWNLOAD_LOCATION": "https://github.com/datalogic/scan2deploy-android/raw/v1.9/Scan2Deploy.apk"
}
```

#### QR Code (Version 1.9)

![Scan2Deploy v1.9](media/scan2deploy1-9.gif)


## Creating Scan2Deploy files

You will need to create your Scan2Deploy JSON files using a text editor. A JSON schemas is available to help ensure valid file content. There are several advantages to using an editor that supports this schema:

* Provides help text for each field
* Provides real-time compiler-like messages letting you know when you have made a mistake
* Allows you to write files faster and with fewer mistakes

### Choosing an editor

There are several good JSON editors available. We reccommend [Visual Studio Code](https://code.visualstudio.com/). It is free and has [many features designed to make writing JSON files easier](https://code.visualstudio.com/docs/languages/json), including utilizing JSON schemas.

Visual Studio and Visual Studio Code can be configured to use JSON schemas by including the `$schema` tag in you JSON file:

```json
{
    "$schema": "http://json.schemastore.org/datalogic-scan2deploy-android"
}
```

Some other editors know to use the schema files on schemastore.org when a given file uses a specific file extension that is registered on schemastore.org:  `file-name.dla.json`

### Barcode Generation

Scan2Deploy barcodes are generated with the `Scan2Deploy` UI inside DXU Desktop. From the main *Device Configuration Utility* page, click the *Scan2Deploy (Beta)* button:

![Scan2Deploy button](./media/image81.png)

Once you are at the Scan2Deploy UI page, you can browse to the JSON file you have created and then click `Generate PDF`.

![Scan2Deploy button](./media/image82.png)

A PDF page will open up in your default PDF viewer, ready for you to print, save, or scan.

![Scan2Deploy button](./media/image83.png)

## File structure

In order to describe the JSON input-file structure used for Scan2Deploy Android, we start off by picking an example template file containing all the available parameters, as follows

```json
{
    "$schema": "http://json.schemastore.org/datalogic-scan2deploy-android",
    "layout": {
        "description": "This is for our Android devices",
        "enroll": true
    },
    "padlock": {
        "valid-until": "20991231235959",
        "key": "ihavenomouthandimustscream",
        "state": "locked",
        "hide-from-launcher": false
    },
    "global": {
        "target-path": "/mnt/sdcard/airwatch",
        "install-path": "/mnt/sdcard/airwatch",
        "update-path": "/mnt/sdcard/airwatch",
        "purge-target-path": true,
        "auto-scan": true,
        "script": "setup.s",
        "action": "close",
        "backup-to-enterprise": true
    },
    "settings": {
        "date-time": "Thu, 19 Apr 2018 07:51:37 GMT",
        "auto-time": false,
        "auto-time-zone": false,
        "auto-time-server": "pool.ntp.org",
        "debug-bridge": false,
        "lock-screen": false,
        "status-bar": false,
        "navigation-bar": true,
        "charge-threshold": 0,
        "usb-profile": "NONE",
        "usb-function": "CHARGING"
    },
    "network": {
        "essid": "TESTTKIP",
        "hidden": false,
        "mode": "wpa-psk",
        "mode-key": "datalogic",
        "mode-key-encrypted": false,
        "ephemeral": false,
        "wait-for-connection": true
    },
    "deployment": {
        "scheme": "http",
        "host": "172.19.0.77",
        "port": 8080,
        "path": "/airwatch.zip",
        "ping-timeout": 1000,
        "fetch-timeout": 60000,
        "working-archive": "/mnt/sdcard/target.zip",
        "skip-inflation": true
    },
    "blobs": [
        {
            "file": "/mnt/sdcard/airwatch/credentials.bin",
            "content": "fd09B1iL/k4jRWrjrP0/sO44teY            +B3UafBLsMsCEqd1KOv6b6JYBXLVv70FmHdZhIVoEOQvHu7O4PUJStpZQ+4PYjPqCO+NQr81M7GOF421Ke8L2u4EYyqDE5qXfLy2shEgaRwRpr2f35/38WZkh6edyiWZQJjyLeZcuI7WiaJPpw7Jcw7ye7mb7Rl+ePNFmfvUrpeRFtN+5kUsx/SbB1v0gDyOOuoep"
        }
    ]
}
```

Each section is *optional* in nature. Sections `padlock`, `settings`, `network`, `deployment`, and `blobs` are skipped when missing. Also, for the `settings` section, the configuration parameters are *optional* and when not provided the current setting is kept.

### Layout

The `layout` section is used to format the output file. The available parameters are the following

* `description`: (optional) Free-form description field (350 max characters). Description will be displayed in header of output file. The default description is `none`.
* `enroll`: (optional) Boolean flag instructing DL-Config to generate Scan2Deploy Device Owner Enrollment QR code in output file. The default value is `false`.

### Padlock

The `padlock` section is used to configure the staging locking feature. The available parameters are:

* `valid-until`: (optional) Specifies the expiration date of the barcode sequence. In order for this to properly work the device date should be synchronized or at least configured. The default value is `20991231235959`, that is a non-expiring barcode sequence.
* `key`: (optional) Defines the padlock key to be used. If the values doesn't match the device one the barcode is rejected. The default value is the empty string (meaning, no key).
* `state`: (optional) Configures the padlock state. Can be either `locked` indicating that the provided key is possibly set, or `unlocked` in the case device padlock need to be disabled.
* `hide-from-launcher`: (optional) Boolean value that enables the `Scan2Deploy` to be disabled for good once the staging is complete. Please note that once disabled the application can't be re-enabled unless a factory-reset is performed. The default value is `false`.

### Global

The `global` section is used to configure some application scoped settings. It also specifies

* `target-path`: (optional) This is the base destination folder where any archive/fill will be inflated/written. The default value points to the device internal SD-card root.
* `install-path`: (optional) Folder where the application expects auto-installed/auto-updated APKs are to be found. The default value point to the value specified for the `target-path` parameter.
* `update-path`: (optional) Folder where the application expects auto-updated OTA packages are to be found. The default value point to the value specified for the `target-path` parameter.
* `purge-target-path`: (optional) Boolean value that drives the application behaviour with regards to the target path, that is whether a pre-existing target need to be *purged* (i.e. *deleted*) prior inflation of the deployment archive. The default value is `true` in order to ensure a cleanest-as-possibile deployment.
* `auto-scan`: (optional) Boolean value that enables/disables the auto-installation and auto-update of APKs and OTA packages. The default value is `false`.
* `downgrade-preinstalled`: (optional) Boolean value used to force the downgrade even of (system) pre-installed application, if required. The default value is `false`.
* `script`: (optional) Can be either a string specifying the absolute/relative path of a file, or a JSON array of strings describing the file content line-by-line. The script file will be interpreted and run at the very end of the staging process, after any setting/network/deployment has been completed. The default value is the empty string (i.e. `""`), disabling the script interpretation.
* `action`: (optional) Specifies the final action performed by the application at the very end of the staging process. This can be `none`, `close`, `enterprise-reset`, `factory-reset`, `reset`, or an `intent:`/`android-app://` URI. The default value is `none`.
* `backup-to-enterprise`: (optional) Boolean flag that activates the enterprise backup persistence for the staging data. That means that both the staging script and archive are copied in the enterprise partition. Upon enterprise reset, the application will re-stage the device with such information. The default value is `false`.

### Settings section

The `settings` sections can be used to controls some inner device settings, that typically need to be changed from the default (Android) setting. The available parameters are the following

* `date-time`: (optional) String representation, in RFC-1123 format, of the date to be set. The default value is `null`, which leave the current device date untouched.
* `auto-time`: (optional) Boolean value controlling the *Date & Time* automatic date-time adjustment setting. The default (Android) value is `true`.
* `auto-time-zone`: (optional) Boolean value controlling the *Date & Time* automatic time-zone adjustment setting. The default (Android) value is `true`.
* `auto-time-server`: (optional) Address of the NTP server to be used for date-time synchronization. Please note that the timezone won't possibly be synched due to lack of a GPS unit in the device. If the server is set a device reboot is suggested for the new setting to be spread system-wide.  The default value is `null`, which leave the default NTP is used (i.e. `asia.pool.ntp.org`).
* `debug-bridge`: (optional) Boolean value controlling the state of *Android Debug Bridge*. The default (Android) value is `true`.
* `lock-screen`: (optional) Boolean value controlling the state of Android's lock-screen, requiring the user to swipe the display to unlock the device. The default (Android) value is `true`.
* `status-bar`: (optional) Boolean value controlling the (top) display status-bar. By hiding the status-bar notifications will disappear, too. The default (Android) value is `true`.
* `navigation-bar`: (optional) Boolean value controlling the (bottom) display navigation-bar. The default (Android) value is `true`.
* `charge-threshold`: (optional) Integer value in the range `0` to `100` indicating the charge threshold a battery exhausted device need to reach to automatically boot when recharging. The default value is `5`.
* `usb-profile`: (optional) USB communication profile in use. Available values are `NONE`, `BOTH`, `DATA`, and `CHARGE`. The default (Android) value is `BOTH`.
* `usb-function`: (optional) USB communication function in use. Available values are `MTP`, `PTP`, and `CHARGING`. The default (Android) value is `CHARGING`.

### Network

The `network` sections is used to configure the device Wi-Fi network. The available parameters are the following

* `essid`: (optional) The wireless network ESSID. The default value is `tsunami`.
* `hidden`: (optional) Boolean value indicating whether the wireless network is using a hidden ESSID. The default value is `false`.
* `mode`: (optional) The wireless connection mode. Supported values are `open`, `wep-40`, `wep-104`, `wpa-psk`, `wpa2-psk`, `wpa-eap`, and `wpa2-eap`. The default value is `open`.
* `mode-key`: (optional) The wireless network key, if needed. The default value is the empty string (i.e. `""`).
* `mode-key-encrypted`: (optional) Boolean value reporting if the `mode-key` is written in plain-text or encrypted (with a custom encryption algorithm). The default value is `false`.
* `eap-method`: (optional) Configures the EAP authentication method. Supported values are `none`, `peap`, `tls`, `ttls`, `pwd`, `sim`, `aka`, and `aka-prime`. The default value is `none`.
* `eap-phase2`: (optional) Configures the EAP phase 2 authentication method. Supported values are `none`, `pap`, `mschap`, `mschapv2`, and `gtc`.The default value is `none`.
* `eap-identity`: (optional) Indicates the EAP identity. The default value is the empty string (i.e. `""`).
* `eap-anonymous-identity`: (optional) Indicates the EAP anonymous identity, used as the unencrypted identity with certain EAP types. The default value is the empty string (i.e. `""`).
* `eap-password`: (optional) Sets the EAP password, if needed. The default value is the empty string (i.e. `""`).
* `eap-password-encrypted`: (optional) Boolean value reporting if the `eap-password` is written in plain-text or encrypted (with a custom encryption algorithm). The default value is `false`.
* `eap-certificate`: (optional) Base64 representation of the EAP certificate to use. The default value is the empty string (i.e. `""`).
* `proxy-host`: (optional) Server name or IP address of the proxy to be user for HTTP/HTTPS communications. The default value is the empty string (i.e. `""`).
* `proxy-port`: (optional) Server IP port of the proxy for HTTP/HTTPS communications. The default value is `0`.
* `purge`: (optional) Boolean value telling if any currently configured wireless network is to be removed. This can be useful in order to avoid profile roaming. The default value is `true`.
* `reconfigure`: (optional) When the `purge` parameter is set to `false` the wireless network the application is going to set-up could already be existing. This boolean parameter controls the application behavior, that will re-configure any existing and matching network (`true`) or skip it and leave it untouched (`false`).
* `sleep-policy`: (optional) Wireless sleep policy, as for Android's [Settings.Global](https://developer.android.com/reference/android/provider/Settings.Global.html) parameter. Available values are `0` (`WIFI_SLEEP_POLICY_DEFAULT`), `1` (`WIFI_SLEEP_POLICY_NEVER_WHILE_PLUGGED`), and `2` (`WIFI_SLEEP_POLICY_NEVER`). The default value is `2` (i.e. `WIFI_SLEEP_POLICY_NEVER`).
* `frequency-band`: (optional) Controls the frequency bands supported by the device. Supported value are `auto`, `5ghz`, and `2ghz`. The default value is the current device setting (typically `"auto"`).
* `ephemeral`: (optional) If set to `true` the wireless connection profile will be solely used during the staging process, and deleted once complete. When `false` the profile will still be present after the staging process. The default value is `true`.
* `save-to-file`: (optional) Absolute path of the file where the current network configuration will be saved. The default value is the empty string (`""`), indicating that the network configuration will not be serialized to file.
* `wait-for-connection`: (optional) Tells whether a valid Wi-Fi connection has to be waited once the network configuration is complete. Can be useful when the device need to be configured *but* a valid Wi-Fi connection is not ready, yet. The default value is `true`.

### Deployment

The `deployment` section can be used to download a ZIP archive from a server and inflate it to the `target-path` folder. The available parameters are the following

* `scheme`: (optional) The deployment download scheme to use. Can be either `http` or `https`. The default value is `http`.
* `host`: (mandatory) The host-name or internet-protocol address of the server from which the resource is to be fetched.
* `port`: (optional) TCP/IP port to be used to contact the server. The default value is `80`.
* `path`: (optional) Path to the server resource to download, complete with query-string if needed. The default value is the empty string (`""`).
* `check-timeout`: (optional) The default value is `1000`.
* `fetch-timeout`: (optional) The default value is `60000`.
* `skip-inflation`: (optional) Boolean value instructing the application *not* to inflate the deployment archive once downloaded. This can be useful to speed the OTA update process up. The default value is `false`.
* `working-archive`: (optional) String representation of the local archive path-file name, once downloaded. The default value is `/mnt/sdcard/scan2deploy.archive`.

### Blobs

The `blobs` (optional) section is a JSON-array of objects. Each object *must* contain the following two attributes

* `file`, the path of the file to be created, and
* `content`, the base64 representation of the actual file content.

The `file` attribute can be either absolute or relative (in this latter case, relative to the `global/target-path` parameter value). During the files de-serialization any required (parent) path is automatically created.

In order to generate the base64 representation of a give binary file any suitable tool can be used (e.g. `base64` command-line program, or online services such as [HexEd.it](https://hexed.it) or [CyberChef](https://gchq.github.io/CyberChef/)).

### Third-party Integration

The application can be either controller by the user, or programmatically controlled in un-manned manner. This can be achieved by calling the launch intent for the `com.datalogic.android.scan2deploy` package and passing the encoded data representing the configuration to be applied.

```java
Context context = ...; // Android context to be used (e.g. the current activity one)
String data = ...; // Encoded Scan2Deploy configuration
PackageManager packageManager = context.getPackageManager();
Intent launchIntent = packageManager.getLaunchIntentForPackage("com.datalogic.android.scan2deploy");
launchIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
launchIntent.putExtra("data", data);
context.startActivity(launchIntent);
```

To obtain the encoded configuration data, the `dl_config.exe` program need to be called with the `--encodeonly=true` flag. In this case, obviously, the output filename is not required. An example output in this case is the following:

```console
> dl_config.exe lock-test.json --encodeonly=true
Datalogic Staging Configurator (ver 1.6.0)
Copyright (c) 2016-2018 by Datalogic MCSA S.r.l.
======================================================================
Loading file 'lock-test.json'
Processing data
-----BEGIN ENCODED DATA-----
Bm9uiYeZ/NWOnA0ZtIrgLMe1iTFLmjusB6P7S4z/lVZ7LFk1d++YM73GqVnqsT9dmxEEuWfR9My93fUA2tUcEv+jxlGNjljHQpXt96BvbVR8bokir3SsXTnFU3qTDeZznLs3PFh+O3n59luLWVNR3c2mFJF5vaGmLue6xHi3bwJgrZOKzs3/bpibCqeNBLcHa/zQRCPQw4=
-----END ENCODED DATA-----
```

The data string to be passed is the ASCII armored sequence (header and trailer included).

As a sidenote, when launched from intent it could be beneficial to specify the global action for the application in order to (for example) automatically close it when the settings have been applied.

### Scripting Language

During the staging finalization phase, the application can be instructed to interpret and run a custom script, enabling it to perform additional post-staging procedures.

The language structure is a simple one-statement-per-line sequence, executed in order.

Some (the most useful) of the supported commands are the following:

* `DELETE <path> <pattern> <include>`: Recursively deletes files/folders starting from `path`, only if the name matches the `pattern` regular-expression. If `include` is `true` the matching entries will be deleted, the non-matching otherwise. If folder `path` is empty at the end of the process it will be deleted, as well.
* `GRANT <package-name>`: Grants all the permissions declared in the manifest for an application given its `package-name`.
* `INFLATE <archive-path> <target-path>`: Inflates the ZIP archive found at `archive-path` to `target-path` folder.
* `INSTALL <apk-path>`: Installs an application given the path to the containing APK.
* `LAUNCH <package-name>`: Starts the launching intent given an application `package-name`.
* `SHELL <command-with-arguments>`: Runs an arbitrary shell command, similarly to the `ADB shell` command.
* `UNINSTALL <package-name>`: Uninstalls a previously installed application given its `package-name`.
* `UPDATE <ota-path> <reset-type> <force-update>`: Installs a firmware update from an OTA package.
* `WAIT <milliseconds>`: Suspend the script execution for `milliseconds` milliseconds.

## Example usage

The following example will update the Datalogic tools on the device to the following versions:

* DXU Agent 1.20.168
* SoftSpot 1.8.92

```json
{
    "$schema": "http://json.schemastore.org/datalogic-scan2deploy-android",
    "global": {
        "auto-scan": true,
        "target-path": "/sdcard/Download/"
    },
    "deployment": {
        "scheme": "https",
        "host": "github.com",
        "port": 443,
        "path": "/datalogic/sandbox/raw/master/demo.zip"
    }
}
```