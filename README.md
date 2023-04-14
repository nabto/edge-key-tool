# Key tool for Nabto Edge

This tool can be used to generate private keys and convert them to different formats used in Nabto Edge devices.

The tool takes a private key as input, converts it, and presents it as output.

## Building

This build requires cmake to be installed on the system.

From the root of the repo:
```
mkdir build
cd build
cmake ../
make -j
./edge_key_tool --help
```

## Key Formats

The tool supports the following [in/out] formats:
 * `pem` [in/out]: PEM encoded string as used by (nabto_device_set_private_key)[https://docs.nabto.com/developer/api-reference/embedded-device-sdk/context/nabto_device_set_private_key.html]
 * `raw` [in/out]: 64 character HEX string of the raw private key. This compact string representation of the Private Key is useful for providing keys as environment variables, handling in bash scripts, and using as command line argument.
 * `fingerprint` [out]: 64 character HEX string of the public key fingerprint to be uploaded to the Nabto Cloud Console.
 * `cert` [out]: The certificate of the Private key.
 * `generate` [in]: The tool generates a private key to use as input.
 * `none` [out]: The tool creates no output.

## Input Private Key

The Input Private Key can be provided in 3 different ways:

__From File__: Using the `--input` option, a Private Key can be loaded from a file
__Command line__: Using the `--string-input` option, a Private Key can be provided directly as a string. This is mainly useful when providing the key in `raw` format.
__Generate__: By providing no input, the `--input-format` defaults to `generate`, making the tool generate a private key. When generating a Private Key, the tool will always print the Key in `pem`, `raw`, and `fingerprint` to stdout. The output controls can still be used to generate a specific output, but this will be in addition to the stdout info.


When providing a Private Key to the tool, by default, the tool assumes it is provided in the `raw` format. If the input is in `pem` format, use `--input-format pem`.

## Output control

By default, the tool will write the output to stdcout, but it can also write it to a file using `--output /path/to/file`.

The `--output-format` defaults to the `none` format. This is mainly useful with the default `generate` input, as this will make the tool print the most used output formats to stdout.

## Examples

### Create a new Private Key

```
$ ./edge_key_tool
Generating new key
PEM Private Key:
-----BEGIN EC PRIVATE KEY-----
MHcCAQEEIOlYRuvUxdDidARnwjQ/wGxj2VyAdsQdff8mQhUiJ7JxoAoGCCqGSM49
AwEHoUQDQgAExNr4VVL8TsarzKvfRnRrIu6zu9QytZLSKrH9dQ0RPkA86gF8WMbB
6JnFAJPpnCzqDTdOI/7Lp8xDp8L6cEx04w==
-----END EC PRIVATE KEY-----

Raw Private Key:
e95846ebd4c5d0e2740467c2343fc06c63d95c8076c41d7dff2642152227b271

Fingerprint:
204fc4ded857ce4b066fb40f36e37b5e7a97515456ecd1dda89e589aa8b6ae70
```

### Set device key from environment

You have a created a Private Key for a device and uploaded the fingerprint to the Nabto Cloud Console.
Now you want to configure one of our example applications to use this key from an environment variable.
Storing the Private Key in an environment variable is done using the `raw` format, however, the examples reads its Private Key from the `device.key` file in `pem` format.

```
$ MY_RAW_KEY=e95846ebd4c5d0e2740467c2343fc06c63d95c8076c41d7dff2642152227b271
$ ./edge_key_tool -s ${MY_RAW_KEY} --output device.key --output-format pem
Private key from input format 'raw' successfully converted to format 'pem'
Output successfully written to file device.key
$ simple_tunnel_device <PRODUCT_ID> <DEVICE_ID>
```
__note:__ This example requires the `simple_tunnel_device` app to be install in your path. You must also run the example app from the same folder in which the `device.key` output is created.

### Get the fingerprint from PEM file

If an example application has generated a Private key for itself, it is saved in `pem` format in the `device.key` file in the same folder from which the example is run. This tool can read this file and show you the fingerprint you must upload to the Nabto Cloud Console before the device is able to attach. (The example will also print this fingerprint, so this tool is simply another way of getting the fingerprint):

```
./edge_key_tool --input device.key --input-format pem --output-format fingerprint
Parsing input file 'device.key' with format 'pem'
Private key from input format 'pem' successfully converted to format 'fingerprint'
Resulting output:
204fc4ded857ce4b066fb40f36e37b5e7a97515456ecd1dda89e589aa8b6ae70
```
