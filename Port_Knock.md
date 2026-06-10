# Launch Tool "Port Knock"
This launch tool performs port knocking against a remote host before establishing a connection. Port knocking is a technique where a sequence of connection attempts to closed ports triggers a firewall rule to temporarily open a specific port.

The tool supports UDP knocking, custom payloads for UDP datagrams, and a pre- / post-knock verification check to confirm the destination port is already / was successfully opened.

## Parameters:
* `LT.KNOCK_PORT_SEQUENCE` `(default=<empty>)`: The sequence of ports to knock, in order. Supports UDP protocol only. Example: `3454, 3455, 3456`
* `LT.KNOCK_HOST` `(default={{CON.HOST}})` (optional): The target host for the port knocking sequence.
* `LT.KNOCK_SOURCE_PORT` `(default=0)` (optional): The source port to bind to when sending knock packets. Set to `0` to use a random port assigned by the operating system.
* `LT.KNOCK_DELAY_MS` `(default=50, 250, 2000)` (optional): An array of three timing values in milliseconds:
  1. **Knock delay**: Time to wait between each knock in the sequence (only when more knocks remain)
  2. **Post-sequence delay**: Additional wait time after all knocks complete
  3. **Check timeout**: Maximum total time for the check loop to keep retrying the destination port verification. The timeout for the verification is 500ms.
* `LT.KNOCK_PAYLOAD_B64` `(default=<empty>)` (optional): A Base64-encoded payload to send with UDP knock datagrams. If empty, a single zero byte is sent. Example: `SGVsbG8gV29ybGQ=` (Base64 for "Hello World")
* `LT.KNOCK_CHECK_DEST_HOST` `(default={{CON.HOST}})` (optional): The host to check after the port knocking sequence completes. Used to verify the destination port was successfully opened by the knock. Also used in the pre-check.
* `LT.KNOCK_CHECK_DEST_TCP_PORT` `(default={{CON.PORT}})` (optional): The TCP port to check before and after knocking. Set to `0` to skip the verification check entirely.
* `LT.KNOCK_CHECK_MAX_RETRIES` `(default=3)` (optional): Maximum number of port knock attempts in case the port verification fails.
* `LT.KNOCK_PRE_RUN_POWERSHELL_CODE_B64` `(default=<empty>)` (optional): Custom Powershell code (base64 encoded) to dynamically change the port knocking behaviour (more info available [here](https://github.com/WinSSHTerm/LaunchTools/blob/main/Common_Functions.md))

## Options:
* `LTOPT.DEBUG_MODE` `(default=false)`: Enables verbose debug output showing timing details, parsed port sequences, and check attempt progress.

## Sample usage
We want to connect to MyServer and use port knocking (UDP, sequence: `3454, 3455, 3456`) to open the SSH port 22 before we establish the SSH connection.
```
+--------+                              +---------------+
| My PC  | ----[Knock sequence]-------> |   MyServer    | 
+--------+                              +---------------+
                                        | 192.168.0.150 |
                                        | SSH-Port: 22  |
                                        | SSH-User: root|
                                        | knockd        |
```
For this scenario a new connection can be created in WinSSHTerm with following configuration:
```
+-------------------+
|   Configuration   |
+-------------------+
| Name          | MyServer              |
| Host/IP       | 192.168.0.150         |
| Port          | 22                    |
| User          | root                  |
| Password      | ********              |
| Launch Tool   | Port Knock            |
...
| L-Tool Params | {"LT.KNOCK_PORT_SEQUENCE":"3454,3455,3456"} |
+-------------------+
```
For testing purposes, you can set up `knockd` this way. Upon successful knocking it will open up the SSH port and remove the iptables rule again after 10 seconds. Make sure the three UDP ports can be reached from "My PC".
```
[options]
    UseSyslog
    Interface = eth0 # You might need to set a different device, check "ip -a"
    LogFile = /var/log/knockd.log

[openSSH]
    sequence    = 3454:udp,3455:udp,3456:udp
    seq_timeout = 5
    command     = /bin/sh -c '/sbin/iptables -I INPUT 1 -s %IP% -p tcp --dport 22 -j ACCEPT; sleep 10; /sbin/iptables -D INPUT -s %IP% -p tcp --dport 22 -j ACCEPT'
```
