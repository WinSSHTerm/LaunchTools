# Common Functions
WinSSHTerm provides PowerShell functions, which can be accessed in the Launch Tool script. This way, common functionality can be reused without duplicating code.

## Function `WinSSHTerm-StartMultipleJump`
This function lets you create a SSH tunnel through multiple Jump Servers (see [here](https://github.com/WinSSHTerm/LaunchTools/blob/main/Multiple_Jump.md) for more information how to use it).

It makes use of `Plink's` (local) port forwarding feature. After creating up the tunnel the function will return a random local port, which has to be accessed with `127.0.0.1:<local_port>`.

### Parameters
- `-jumpList`: set `@({{LT.JUMP_LIST}})` as value
- `-passwordList` (optional): set `@({{LT.PASSWORD_LIST}})` as value
- `-plinkPath`: path to plink.exe, you can set `{{WST.PLINKPATH}}` as value
- `-conHost`: set `{{CON.HOST}}` as value
- `-conPort`: set `{{CON.PORT}}` as value
- `-conName` (optional): set `{{CON.NAME}}` as value

### Return value
- the local tcp port of the tunnel (random, between 49152 and 65355)

## Function `WinSSHTerm-StartPortKnock`
This function performs port knocking against a remote host by sending a sequence of UDP datagrams to specific ports (see [here](https://github.com/WinSSHTerm/LaunchTools/blob/main/Port_Knock.md) for more information how to use it).

### Parameters
- `-portSequence`: set `@({{LT.KNOCK_PORT_SEQUENCE}})` as value. Each entry can be a plain port number (UDP only).
- `-hostname`: set `{{LT.KNOCK_HOST}}` as value (defaults to `{{CON.HOST}}`)
- `-sourcePort` (optional): set `{{LT.KNOCK_SOURCE_PORT}}` as value. Binds the knock packets to a specific source port (0 = random)
- `-delayMilliseconds` (optional): set `@({{LT.KNOCK_DELAY_MS}})` as value. Array of 3 integers:
  1. Delay between each knock in milliseconds (default: 50)
  2. Additional delay after sequence completes before starting destination check (default: 250)
  3. Maximum total time in milliseconds to retry the destination check. The timeout for the verification check is 500ms. (default: 2000)
- `-base64Payload` (optional): set `{{LT.KNOCK_PAYLOAD_B64}}` as value. A payload for UDP knocks (does not need to be base64 encoded). If empty, a single zero byte is sent
- `-checkDestHost` (optional): set `{{LT.KNOCK_CHECK_DEST_HOST}}` as value (defaults to `{{CON.HOST}}`)
- `-checkDestTcpPort` (optional): set `{{LT.KNOCK_CHECK_DEST_TCP_PORT}}` as value. TCP port to verify after knocking. Set to 0 to skip verification (defaults to `{{CON.PORT}}`)
- `-checkMaxRetries` (optional): set `{{LT.KNOCK_CHECK_MAX_RETRIES}}` as value. Number of times to retry the entire knocking sequence if the destination check fails (default: 3). Each retry includes re-sending the full knock sequence

You can use the parameter `LT.KNOCK_PRE_RUN_POWERSHELL_CODE_B64` to dynamically change the port knocking behaviour by overriding these PowerShell variables:
- `$knockPortSequence` 
- `$knockHost`
- `$knockSourcePort`
- `$knockDelayMilliseconds`
- `$knockPayload`
- `$knockCheckDestHost`
- `$knockCheckDestTcpPort`
- `$knockCheckMaxRetries`

The parameter accepts multiline PowerShell code that will be executed before the WinSSHTerm-StartPortKnock function call. The base64-encoded code is decoded at runtime and injected into the script, allowing you to override any of the port knocking parameters dynamically. 

### Return value
- returns `true` if the port knock was successful
