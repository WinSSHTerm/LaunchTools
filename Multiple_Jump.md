# Launch Tool "Multiple Jump"
This launch tool allows you to connect to a destination host through multiple jump servers (bastion hosts). Make sure `plink.exe` is installed in WinSSHTerm.

## Sample usage
In this example, the user (`My PC`) wants to connect to the remote SSH host 192.168.2.203 (`MyServer`) through 2 jump servers:

```
+--------+        +---------------+        +---------------+        +----------------+
| My PC  | -----> | Jump Server 1 | -----> | Jump Server 2 | -----> |  Destination   |
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
| Name         | MyServer              |
| Host/IP      | {{LTRET.FWD_HOST}}    |
| Port         | {{LTRET.FWD_PORT}}    |
| User         | root                  |
| Password     | ********              |
| Launch Tool  | Multiple Jump         |
| Logical Host | 192.168.2.203:22      |
...
| L-Tool Vars  | {"LT.PASSWORD_LIST":"'{{MYPASS1}}','{{MYPASS2}}'","LT.JUMP_LIST":"@{JumpHost='192.168.0.150'; JumpUser='root'; JumpPort='22'; DestHost='192.168.1.134'; DestPort='22'},@{JumpHost='192.168.1.134'; JumpUser='root'; JumpPort='22'; DestHost='192.168.2.203'; DestPort='22'}"} |
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
