# Launch Tools

**Launch Tools** is a feature that allows you run a **PowerShell script** for each connection in **WinSSHTerm**. It will be triggered before the SSH session is opened (opening an SSH session is optional). Using variables, you can exchange data between the script and WinSSHTerm.

A **Launch Tool** is defined by an **XML document** that contains parameters, options, and a PowerShell script. You can use the **Launch Tools** provided in this repository or create your own.

The feature was introduced in version **2.39.0**. To use the Launch Tool, the Windows installation on which you run WinSSHTerm must support **ConPTY** (supported since **Windows 10 version 1809**, released in October 2018). This documentation describes the feature implementation from the **latest version** of WinSSHTerm.

## Configure a Launch Tool in WinSSHTerm

To configure the Launch Tool in the connection settings of WinSSHTerm, you can either:

1. Set the path to the XML file via **Connection → Launch Tool**, or
2. Encode the content of the XML file with **Base64** (as a single line) and save the encoded string into **Connections → L-Tool Base64**.

In case both parameters have a valid value, then **Connection → Launch Tool** will be prioritized.

You can override the parameters of the Launch Tool by saving a single-line string with key-value pairs (JSON formatted) into **Connections → L-Tool Vars**.

### Example

A Launch Tool contains the following parameters:

```xml
<param>
  <name>LT.MY_VAR_1</name>
  <value>false</value>
</param>
<param>
  <name>LT.MY_VAR_2</name>
  <value>10</value>
</param>
<param>
  <name>LT.MY_SECRET_VAR</name>
  <value></value>
</param>
```

To override `LT.MY_VAR_1` with `"true"` and `LT.MY_VAR_2` with `"20"`, you have to set the string like this:

```json
{"LT.MY_VAR_1":"true","LT.MY_VAR_2":"20"}
```

If you need to pass a secret value, you can set a hidden variable in `File->Preferences->Connections`, e.g. `{{MY_SECRET}}`, set the value there and set the string like this:

```json
{"LT.MY_VAR_1":"true","LT.MY_VAR_2":"20","LT.MY_SECRET_VAR":"{{MY_SECRET}}"}
```

## Structure of the XML File
### Metadata (Optional)

You can define the tags `<name>`, `<version>` and `<description>`.

### Parameters (Optional)

Parameters allow you to dynamically change the behavior of the PowerShell script. WinSSHTerm will replace each placeholder in the PowerShell script with its value before executing the script. A placeholder contains the parameter name inside two brackets, e.g., `{{LT.MY_VAR_1}}`.

In the `<params>` section, you can define multiple `<param>` tags. Each `<param>` tag must contain two sub-tags: `<name>` and `<value>`. Each parameter name must match the regular expression `^LT\.[A-Z0-9_]+$`.

### Options (Mandatory)

Options allow you to adjust the way WinSSHTerm handles the Launch Tool.

In the `<options>` section, you can define multiple `<option>` tags. Each `<option>` tag must contain two sub-tags: `<name>` and `<value>`. Each parameter name must match the regular expression `^LTOPT\.[A-Z0-9_]+$`.

The following options are available and must be set:

- **`LTOPT.WINDOW_HIDE_TIME_IN_MILLIS`**: When WinSSHTerm starts a Launch Tool, a new window will be opened. Most of the time, the script will be finished in a short time, and the window will be closed again. To avoid this short popup of the window, you can set a delay time (e.g., `1000` for 1 second). A value of `0` will show the window immediately.
  
- **`LTOPT.KILL_PROCS_ON_WINDOW_CLOSE`**: If `true`, WinSSHTerm will automatically close all subprocesses opened in the script after the Launch Tool finishes. If `false`, these processes will continue to run. All subprocesses will be closed at last when WinSSHTerm is closed.

- **`LTOPT.LAUNCH_TERMINAL`**: If `true`, WinSSHTerm will launch the terminal after the Launch Tool finishes. If `false`, WinSSHTerm will take no action after the Launch Tool closes.

- **`LTOPT.LAUNCH_COPY_FILES`**: If `true`, WinSSHTerm will launch the copy files operation after the Launch Tool finishes, if the user chooses to do so. If `false`, WinSSHTerm will take no action after the Launch Tool closes, even if the user chose to copy files.

### The PowerShell Script

The script is inside the `<script>` tag. As it typically has multiple lines, the script has to be inside a `CDATA` section, e.g.:

```xml
<script><![CDATA[
# script line 1
# script line 2
...
]]></script>
```

#### Script Ending and Return Value

To signal WinSSHTerm that the script has successfully finished, a special marker line needs to be set. When WinSSHTerm reads this marker, it will stop reading the script output and wait until it exits. The marker line is a string that starts with `"WinSSHTerm_script_finished"`. You can set the marker by placing this command at the end of the script:

```powershell
Write-Host "WinSSHTerm_script_finished"
```

Optionally, you can set one or more return variables. These variables will be parsed and made available for use in WinSSHTerm with the syntax `{{LTRET.<RETURN_VAR_NAME>}}` after the Launch Tool finishes. For example, if the script contains the PowerShell variables `$myReturnVar1` and `$myReturnVar2`, you can return them like this:

```powershell
Write-Host "WinSSHTerm_script_finished;{""VAR1"":""$myReturnVar1"",""VAR2"":""$myReturnVar2""}"
```
After the Launch Tool finishes, the values of the return variables will be accessible in WinSSHTerm as
- `{{LTRET.VAR1}}` for `$myReturnVar1`
- `{{LTRET.VAR2}}` for `$myReturnVar2`

#### Connection, template and special variables
The following variables can be used in the PowerShell script:
- **`{{CON.NAME}}`**: name of the connection
- **`{{CON.HOST}}`**: hostname/ip
- **`{{CON.USER}}`**: username
- **`{{CON.PASSWD}}`**: password
- **`{{CON.PORT}}`**: port
- **`{{CON.DESC}}`**: description
- **`{{CON.CUSTOMID}}`**: a custom id of your choice
- **`{{WST.TEMPPATH}}`**: a temporary path inside your WinSSHTerm's directory, that contains the instance's PID
- **`{{WST.PUTTYPATH}}`**: the location for the **PuTTY** binary
- **`{{WST.PLINKPATH}}`**: the location for the **Plink** binary
- **`{{WST.PAGEANTPATH}}`**: the location for the **Pageant** binary
- **`{{WST.PUTTYGENPATH}}`**: the location for the **PuTTYgen** binary
- **`{{WST.WINSCPPATH}}`**: the location for the **WinSCP** binary
- **`{{WST.VCXSRVPATH}}`**: the location for the **VcXsrv** binary
- In addition you can use all your custom variables from `File->Preferences->Connections`

#### 

## Currently known Limitations

- Launch Tools do not work in Cluster Mode.
