# Launch Tools

The **Launch Tool** is a feature available for each connection in **WinSSHTerm** since version **2.39.0**. It integrates **PowerShell scripts** into WinSSHTerm, making it much more versatile.

A **Launch Tool** is defined by an **XML document** that contains parameters, options, and a PowerShell script. You can use the XML documents provided in this repository or create your own.

To configure the Launch Tool in the connection settings of WinSSHTerm, you can either:

1. Set the path to the XML file via **Connection → Launch Tool**, or
2. Encode the content of the XML file with **Base64** (as a single line) and save the encoded string into **Connections → L-Tool Base64**.

To use the Launch Tool, the Windows installation on which you run WinSSHTerm must support **ConPTY** (supported since **Windows 10 version 1809**, released in October 2018).
