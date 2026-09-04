**Windows Server Network Recovery Work Instruction**

Windows Server work instruction for diagnosing a disabled network adapter, restoring connectivity, validating recovery, and documenting escalation.

**WORK INSTRUCTION: WINDOWS SERVER NIC RECOVERY**

**Network Adapter Troubleshooting Decision Tree**

- Review the decision tree before beginning troubleshooting. Confirm the reported condition, perform approved first-line validation, and follow the applicable branch based on the results.
- If the issue exceeds the scope of this work instruction, document the findings and escalate to the appropriate team according to organizational procedures.

![Network Adapter Troubleshooting Decision Tree](images/00%20NIC%20decision-tree.png)


**Server Unreachable**

- From the administrative workstation, test connectivity to the known Windows Server IP address. On a Linux workstation, run `ping -c 4 192.168.1.10`.
- Review the results. If the test returns `0 received` and `100% packet loss`, the server is not responding to the connectivity test.
- Access the affected Windows Server through an available local console, hypervisor console, or other approved direct-access method to continue troubleshooting.

![Server Unreachable](images/01%20Server%20Unreachable.png)


**Loopback Test**

- From the Windows Server console, open Command Prompt and run `ping 127.0.0.1`.
- Confirm that the loopback address responds successfully. A result of `Sent = 4, Received = 4, Lost = 0 (0% loss)` confirms that the local TCP/IP stack is responding.
- If loopback succeeds, continue outward in the troubleshooting process and inspect the network adapter. If loopback fails, document the findings and escalate according to organizational procedures.

![Loopback Test](images/02%20Loopback%20Test.png)


**Adapter Disabled**

- Open PowerShell and run `Get-NetAdapter` to review the server's available network interfaces.
- Locate the affected adapter and review the `Status` column. In this example, the Ethernet adapter reports `Disabled`, identifying the condition preventing normal network connectivity.
- Record the adapter name before performing remediation. The affected interface in this example is named `Ethernet`.

![Adapter Disabled](images/03%20Adapter%20Disabled.png)


**Device Status**

- Open **Control Panel** → **Network and Internet** → **View network status and tasks** → **Change adapter settings**. This opens the **Network Connections** window.
- Locate the affected Ethernet adapter and verify that it displays `Disabled`. Technicians can also open this interface directly by pressing **Windows + R**, entering `ncpa.cpl`, and pressing **Enter**.
- If the adapter is already enabled, stop this recovery procedure and perform only approved additional validation within scope before escalating if necessary.

![Device Status](images/04%20Device%20Status.png)


**Enable Device**

- In **Network Connections**, right-click the affected **Ethernet** adapter and select **Properties**. Select **Configure** to open the network adapter's device properties.
- On the **General** tab, review **Device status**. If Windows reports `This device is disabled. (Code 22)`, select **Enable Device**.
- Follow the device-enablement prompts by selecting **Next** when prompted and allow Windows to attempt to restore the adapter.

![Enable Device](images/05%20enable%20device.png)


**Device Enabled**

- Confirm that Windows reports `Windows successfully enabled this device`.
- Select **Finish** to complete the recovery action, then close the remaining properties windows as appropriate.
- If Windows cannot enable the adapter successfully, document the result and escalate to the appropriate team according to organizational procedures.

![Device Enabled](images/06%20Device%20Enabled.png)


**Adapter Active**

- Return to **Network Connections** and verify that the Ethernet adapter no longer displays `Disabled`.
- Confirm that the adapter now shows an active network state. The interface may also display the applicable network or domain connection after connectivity is restored.
- Do not consider the incident resolved based on the GUI alone. Perform an independent PowerShell validation before repeating the original connectivity test.

![Adapter Active](images/07%20Adapter%20Active.png)


**PowerShell Confirmation**

- Open PowerShell and run `Get-NetAdapter -Name "Ethernet"`.
- Review the `Status` field and confirm that the Ethernet adapter reports `Up`.
- As an alternative remediation method for technicians authorized and comfortable with PowerShell, a disabled adapter can be enabled with `Enable-NetAdapter -Name "Ethernet" -Confirm:$false`.

![PowerShell Confirmation](images/08%20PowerShell%20Confirmation.png)


**Connectivity Restored**

- Return to the original administrative workstation and repeat the same connectivity test used at the beginning: `ping -c 4 192.168.1.10`.
- Confirm successful responses. In this example, `4 packets transmitted, 4 received, 0% packet loss` verifies that connectivity has been restored from the original workstation.
- Document the original condition, troubleshooting steps, identified cause, remediation, and successful validation in the incident record. Update and resolve the ticket according to organizational procedures.

![Connectivity Restored](images/09%20Connectivity%20Restored.png)

Navigation

[`Back to GitHub Profile`](https://www.github.com/cbueker-it)
