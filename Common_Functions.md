# Common Functions
WinSSHTerm provides PowerShell functions, which can be accessed in the Launch Tool script. This way, common functionality can be reused without duplicating code.

## Function `WinSSHTerm-StartMultipleJump`
This function lets you create a SSH tunnel through multiple Jump Servers (see [here](https://github.com/WinSSHTerm/LaunchTools/blob/main/Multiple_Jump.md) for more information)

### Parameters
- `-jumpList`: set `@({{LT.JUMP_LIST}})` as value
- `-passwordList` (optional): set `@({{LT.PASSWORD_LIST}})` as value
- `-plinkPath`: path to plink.exe, you can set `{{WST.PLINKPATH}}` as value
- `-conHost`: set `{{CON.HOST}}` as value
- `-conPort`: set `{{CON.PORT}}` as value
