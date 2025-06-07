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
