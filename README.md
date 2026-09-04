**Windows Server Network Recovery Work Instruction**

Windows Server work instruction for diagnosing a disabled network adapter, restoring connectivity, validating recovery, and documenting escalation.

**WORK INSTRUCTION: WINDOWS SERVER NIC RECOVERY**

Document Title: WORK INSTRUCTION: Windows Server Network Adapter Recovery  
Document Owner/Responsible Technician: Christopher Bueker  
Version: 1.0  
Effective Date: September 4, 2026  
Next Review Date: August 30, 2027  
Approval Status: Not Applicable  

**Purpose**

The purpose and objective of this work instruction is to provide a repeatable first-line troubleshooting process for a Windows Server 2022 system that is unreachable when a disabled network adapter may be the cause.

The procedure that is described here guides the IT technician through confirming that the server is unreachable, validating the local TCP/IP stack by pinging the loopback address, inspecting the network adapter, performing the approved remediation, restoring the network adapter, validating that the recovery worked from the original administrative workstation, and documenting the outcome.

**Scope**

This work instruction applies to Windows Server systems where the server is reported as unreachable and is not responding to a basic ping test.

It applies when local console, hypervisor, or other approved direct access to the server remains available and when first-line technicians are authorized to inspect and re-enable a disabled network adapter.

Technical issues that go beyond this defined troubleshooting scope should be documented and escalated to the appropriate team according to organizational procedures.

**Intended Audience**

This work instruction is designed for Command Center, Infrastructure Operations Center, Service Desk, or other first-line IT personnel and IT technicians responsible for initial server connectivity validation and approved remediation.

**Required Access and Prerequisites**

- Access to the originating administrative workstation.
- Known hostname or IP address of the affected Windows Server.
- Approved local, virtual, or hypervisor console access to the Windows Server.
- Administrative permissions sufficient to inspect and enable network adapters.
- An active incident or service ticket when required by organizational procedure.

**Cautions**

Making changes to or modifying the status of a network adapter can interrupt remote connectivity.

Confirm that console access is available before disabling, enabling, restarting, or otherwise modifying a server network interface.

Do not perform broader network, firewall, routing, VLAN, operating system, or infrastructure changes unless they are within your authorized scope. Document the findings and escalate the issue when it goes beyond the scope of this work instruction.


**Expected Outcome**

When this procedure is completed, the technician should be able to determine whether a disabled Windows Server network adapter caused the reported connectivity failure.

The technician should be able to restore the adapter when authorized, validate connectivity from the original administrative workstation, and document either a successful resolution or the need for escalation.

**Network Adapter Troubleshooting Decision Tree**

- Review the decision tree before beginning troubleshooting.
- Confirm the reported condition, perform approved first-line validation, and follow the applicable branch based on the results.
- If the issue exceeds the scope of this work instruction, document the findings and escalate to the appropriate team according to organizational procedures.

![Network Adapter Troubleshooting Decision Tree](images/00%20NIC%20decision-tree.png)


**Server Unreachable**

- From the administrative workstation, test connectivity to the known Windows Server IP address.
- On a Linux workstation, run `ping -c 4 192.168.1.10`.
- Review the results. If the test returns `0 received` and `100% packet loss`, the server is not responding to the connectivity test.
- Access the affected Windows Server through an available local console, hypervisor console, or other approved direct-access method to continue troubleshooting.

![Server Unreachable](images/01%20Server%20Unreachable.png)


**Loopback Test**

- From the Windows Server console, open Command Prompt and run `ping 127.0.0.1`.
- Confirm that the loopback address responds successfully.
- A result of `Sent = 4, Received = 4, Lost = 0 (0% loss)` confirms that the local TCP/IP stack is responding.
- If loopback succeeds, continue outward in the troubleshooting process and inspect the network adapter. If loopback fails, document the findings and escalate according to organizational procedures.

![Loopback Test](images/02%20Loopback%20Test.png)


**Adapter Disabled**

- Open `PowerShell` and run `Get-NetAdapter` to review the server's available network interfaces.
- Locate the affected adapter and review the `Status` column.
- In this example, the Ethernet adapter reports `Disabled`, identifying the condition preventing normal network connectivity.
- Record the adapter name before performing remediation. The affected interface in this example is named `Ethernet`.

![Adapter Disabled](images/03%20Adapter%20Disabled.png)


**Device Status**

- Open `Control Panel` → `Network and Internet` → `View network status and tasks` → `Change adapter settings`. This opens the `Network Connections` window.
- Locate the affected Ethernet adapter and verify that it displays `Disabled`. Technicians can also open this interface directly by pressing `Windows + R`, entering `ncpa.cpl`, and pressing `Enter`.
- If the adapter is already enabled, stop this recovery procedure and perform only approved additional validation within scope before escalating if necessary.

![Device Status](images/04%20Device%20Status.png)


**Enable Device**

- In `Network Connections`, right-click the affected `Ethernet` adapter and select `Properties`. Select `Configure` to open the network adapter's device properties.
- On the General tab, review Device status.
- If Windows reports `This device is disabled. (Code 22)`, select `Enable Device`.
- Follow the device-enablement prompts by selecting `Next` when prompted and allow Windows to attempt to restore the adapter.

![Enable Device](images/05%20enable%20device.png)


**Device Enabled**

- Confirm that Windows reports `Windows successfully enabled this device`.
- Select `Finish` to complete the recovery action, then close the remaining properties windows as appropriate.
- If Windows cannot enable the adapter successfully, document the result and escalate to the appropriate team according to organizational procedures.

![Device Enabled](images/06%20Device%20Enabled.png)


**Adapter Active**

- Return to `Network Connections` and verify that the Ethernet adapter no longer displays `Disabled`.
- Confirm that the adapter now shows an active network state. The interface may also display the applicable network or domain connection after connectivity is restored.
- Do not consider the incident resolved based on the GUI alone.
- Perform an independent PowerShell validation before repeating the original connectivity test.

![Adapter Active](images/07%20Adapter%20Active.png)


**PowerShell Confirmation**

- Open `PowerShell` and run `Get-NetAdapter -Name "Ethernet"`.
- Review the `Status` field and confirm that the Ethernet adapter reports `Up`.
- As an alternative remediation method for technicians authorized and comfortable with PowerShell, a disabled adapter can be enabled with `Enable-NetAdapter -Name "Ethernet" -Confirm:$false`.

![PowerShell Confirmation](images/08%20Powershell%20Confirmation.png)


**Connectivity Restored**

- Return to the original administrative workstation and repeat the same connectivity test used at the beginning: `ping -c 4 192.168.1.10`.
- Confirm successful responses. In this example, `4 packets transmitted, 4 received, 0% packet loss` verifies that connectivity has been restored from the original workstation.
- Document the original condition, troubleshooting steps, identified cause, remediation, and successful validation in the incident record.
- Update and resolve the ticket according to organizational procedures.

![Connectivity Restored](images/09%20Connectivity%20Restored.png)

Navigation

[`Back to GitHub Profile`](https://www.github.com/cbueker-it)
