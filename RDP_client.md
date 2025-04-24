# Launch Tool "RDP client"
This launch tool lets you start a RDP session with mstsc.exe. Tunneling the RDP session through multiple Jump Servers is supported.

## Connection configuration:
* `HOST/IP`: the hostname to connect via RDP
* `Port`: the RDP port to connect to (e.g. 3389)
* `User/Password`: credentials needed if the script is configured to handle them automatically

## Parameters:
* `LT.HANDLE_CREDENTIALS_AUTOMATICALLY` `(default=true)`: If true, the script will create saved credentials with 'cmdkey' for the host and use them for authentication. If false, the user has to manually input user and password.
* `LT.AUTO_DELETE_CREDENTIALS_AFTER_DELAY` `(default=true)`: If true, the script will automatically delete the saved credentials after a delay. If false, the saved credentials will remain on the system.
* `LT.AUTO_DELETE_CREDENTIALS_DELAY_TIME_SECS` `(default=15)`: Time in seconds to wait before deleting the saved credentials
* `LT.ENABLE_MULTIPLE_JUMP` `(default=false)`: Enables support for tunneling the RDP session, see function `WinSSHTerm-StartMultipleJump` in [Common_Functions](https://github.com/WinSSHTerm/LaunchTools/blob/main/Common_Functions.md)
