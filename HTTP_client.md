# Launch Tool "HTTP client"
This launch tool lets you open an URL in your default or a custom browser. Tunneling the web service through multiple Jump Servers is supported.

When tunneling an `https` secured web service, the browser won't be able to verify the SSL certificate because the host part of the URL needs to be changed to `127.0.0.1`. For testing purposes, you can disable the certificate verification in your browser. For `chrome`, you could set these arguments:
* `LT.BROWSER_ARGS="--ignore-certificate-errors --allow-running-insecure-content --test-type"`
* `LT.BROWSER="chrome"`

## Connection configuration:
* `HOST/IP`: The URL to open in the browser

## Connection configuration (multiple jump mode) :
* `HOST/IP`: Set the template variable `{{LTRET.FWD_HOST}}`
* `PORT`: Set the template variable `{{LTRET.FWD_PORT}}`
* `Logical Host`: Set the remote URL to open in the browser. The host and port will be automatically replaced so that the traffic will be routed through the tunnel.

## Parameters:
* `LT.BROWSER` `(default=AUTO)`: If `AUTO`, the URL will be opened in your default browser. You can define another browser, e.g. "msedge", "chrome", "firefox", ...
* `LT.BROWSER_ARGS` `(default=<empty>)`: Set a space-separated list of arguments for the browser. Works only if `LT.BROWSER` is not set to `AUTO`. Example: `"--new-window --incognito"` (and `LT.BROWSER="chrome"`) 
* `LT.ENABLE_MULTIPLE_JUMP` `(default=false)`: Enables support for tunneling the Web service, see function `WinSSHTerm-StartMultipleJump` in [Common_Functions](https://github.com/WinSSHTerm/LaunchTools/blob/main/Common_Functions.md)
