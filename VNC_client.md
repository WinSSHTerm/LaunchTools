# Launch Tool "VNC client"
This launch tool lets you start a VNC session with [TigerVNC](https://github.com/TigerVNC/tigervnc) ([Download](https://sourceforge.net/projects/tigervnc/files/stable)). Make sure that the viewer is available on your system. If you use the installer version of TigerVNC (tigervnc64-X.Y.Z.exe), then no changes should need to be made to the parameters of this launch tool. Alternatively, you can download the standalone Viewer (vncviewer64-X.Y.Z.exe). If you rename the file to `vncviewer.exe` and place it into your `tools` directory, then the Launch Tool will use it by default. Tunneling the VNC session through multiple Jump Servers is supported.

## Connection configuration:
* `HOST/IP`: the hostname to connect via VNC
* `Port`: the VNC port to connect to (e.g. 5900)
* `Password`: needed in case `LT.HANDLE_CREDENTIALS_AUTOMATICALLY` is set to true (see below)

## Parameters:
* `LT.VNC_VIEWER_BINARY_PATH_TYPE [absolute|tools]` `(default=absolute)`: With "absolute", the script will use `LT.VNC_VIEWER_BINARY_ABSOLUTE_PATH` to get the path to the viewer binary. With "tools", the script will read the viewer binary name from `LT.VNC_VIEWER_BINARY_TOOLS` and assume the binary is in the directory "tools" of your WinSSHTerm's directory.
* `LT.VNC_VIEWER_BINARY_ABSOLUTE_PATH [valid path to exe]` `(default=C:\Program Files\TigerVNC\vncviewer.exe)`: An absolute path to the viewer binary
* `LT.VNC_VIEWER_BINARY_TOOLS [filename]` `(default=<empty>)`: The filename of the viewer binary (e.g. vncviewer64-1.15.0.exe)
* `LT.HANDLE_CREDENTIALS_AUTOMATICALLY` `(default=true)`: If true, the script will temporarily save the VNC password into a environment variable `VNC_PASSWORD` and use it for authentication. If false, the user has to manually input the VNC password.
* `LT.ENABLE_MULTIPLE_JUMP` `(default=false)`: Enables support for tunneling the VNC session, see function `WinSSHTerm-StartMultipleJump` in [Common_Functions](https://github.com/WinSSHTerm/LaunchTools/blob/main/Common_Functions.md)
