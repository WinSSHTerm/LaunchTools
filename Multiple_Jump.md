# Launch Tool "Multiple Jump"
This launch tool allows you to connect to a remote system through multiple jump servers (bastion hosts). Make sure `plink.exe` is installed in WinSSHTerm.

The launch tool will open a minimized PowerShell window for each SSH jump. These windows define the tunnel. By default they will stay open until WinSSHTerm exits, so that the tunnel can be reused. If you encounter problems with the tunnel, you can close them and reconnect to create a new tunnel.

It will be detected if a host key of a jump server is not cached. In this case, an interactive window will open up. If you then cache the host key by entering `y`, the launch tool will retry and continue to create the tunnel.

## Sample usage
In this example, the user (`My PC`) wants to connect to the remote system 192.168.2.203 (`MyServer`) through 2 jump servers:

```
+--------+        +---------------+        +---------------+        +----------------+
| My PC  | -----> | Jump Server 1 | -----> | Jump Server 2 | -----> |    MyServer    |
+--------+        +---------------+        +---------------+        +----------------+
                  | 192.168.0.150 |        | 192.168.1.134 |        | 192.168.2.203  |
                  | SSH-Port: 22  |        | SSH-Port: 22  |        | SSH-Port: 22   |
                  | SSH-User: root|        | SSH-User: root|        |                |
```

For this scenario a new connection can be created in WinSSHTerm with following configuration:

```
+-------------------+
|   Configuration   |
+-------------------+
| Name          | MyServer              |
| Host/IP       | {{LTRET.FWD_HOST}}    |
| Port          | {{LTRET.FWD_PORT}}    |
| User          | root                  |
| Password      | ********              |
| Launch Tool   | Multiple Jump         |
| Logical Host  | 192.168.2.203:22      |
...
| L-Tool Params | {"LT.PASSWORD_LIST":"'{{MYPASS1}}','{{MYPASS2}}'","LT.JUMP_LIST":"@{JumpHost='192.168.0.150'; JumpUser='root'; JumpPort='22'; DestHost='192.168.1.134'; DestPort='22'},@{JumpHost='192.168.1.134'; JumpUser='root'; JumpPort='22'; DestHost='192.168.2.203'; DestPort='22'}"} |
+-------------------+
```
For the passwords of the jump servers there have been created 2 hidden variables under `File->Preferences->Connections->Template variables`:
```
+-------------+--------------+
|  Variable   | Value_Hidden |
+-------------+--------------+
| {{MYPASS1}} | ********     |
| {{MYPASS2}} | ********     |
+-------------+--------------+
```

## Connection configuration:
* `Host/IP`: set it to `{{LTRET.FWD_HOST}}`
* `Port`: set it to `{{LTRET.FWD_PORT}}`
* `User/Password`: enter user/password for the remote system
* `Launch Tool`: select `Multiple Jump` from the drop-down list
* `Logical Host`: set the values for the remote system `<Host/IP>:<Port>`

## Parameters:
* `LT.JUMP_LIST`: A comma-separated list of data elements that describe all jump servers and the remote system. For each jump server a data element is required. A data element has the syntax: `@{JumpHost='<Host/IP'; JumpUser='<User>'; JumpPort='<Port>'; DestHost='<Host/IP>'; DestPort='<Port>'}`. The first data element has to describe the jump server that is directly reachable.
* `LT.PASSWORD_LIST` (optional): A comma-separated list of passwords for each jump server in `LT.JUMP_LIST`. The syntax is `'<password1>'`. It is recommended to use keys and Pageant for authentication - that way there is no need to define `LT.PASSWORD_LIST`
* `LT.PLINK_PATH` `(default={{WST.PLINKPATH}})`: the path to plink.exe
