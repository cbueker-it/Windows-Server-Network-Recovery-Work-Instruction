**Windows Server Network Recovery Work Instruction**

Windows Server work instruction for diagnosing a disabled network adapter, restoring connectivity, validating recovery, and documenting escalation.

**Introduction**

Standardized procedures are critical in IT departments because they help technicians perform technical work in a consistent and reliable way. High-quality work instructions are particularly useful for troubleshooting processes that include multiple steps, decision points, validation requirements, and clearly defined scope or escalation boundaries.

Quality work instructions also help new technicians get up and running more quickly by reducing an over-reliance on tribal knowledge. Technician experience is still valuable, but it is strongest when it is supported by official organizational documentation that is accurate, current, accessible, and easy to find.

The repository that I have done here contains a lab-based Windows Server 2022 Network Recovery Work Instruction. I created this work instruction using my personal lab and informed it through my professional experience in Infrastructure Operations Center and Command Center environments. I demonstrate how a technician can confirm a connectivity issue, troubleshoot from the local TCP/IP stack outward, restore a disabled network adapter, validate the recovery, document the outcome, and, if necessary, escalate the issue according to organizational procedures.

**Objectives**

- Create a complete Windows Server 2022 network recovery work instruction for a first-line troubleshooting scenario.
- Demonstrate a structured troubleshooting process that includes validation, decision points, remediation, and escalation boundaries.
- Show the use of screenshots, redaction, visual callouts, and a decision tree to make the procedure clear and easy to follow.
- Demonstrate how a technician can validate a reported connectivity failure, identify a disabled network adapter, restore it, and confirm recovery.
- Show how standardized work instructions can support technician onboarding, knowledge transfer, troubleshooting consistency, and incident resolution.

***

**BEGINNING OF WORK INSTRUCTION**

Document Title: Work Instruction: Windows Server Network Adapter Recovery  
Document Owner/Responsible Technician: Christopher Bueker  
Version: 1.0  
Effective Date: September 4, 2026  
Next Review Date: August 30, 2027  
Approval Status: Not Applicable  

**Purpose**

The purpose and objective of this work instruction is to provide a repeatable first-line troubleshooting process for a Windows Server 2022 system that is unreachable when a disabled network adapter may be the cause.

This procedure guides the IT technician through confirming that the server is unreachable, validating the local TCP/IP stack by pinging the loopback address, and inspecting the network adapter; it also involves performing the approved remediation, restoring the network adapter, validating that the recovery worked from the original administrative workstation, and documenting the outcome.

**Scope**

This work instruction applies to Windows Server systems where the server is reported as unreachable and is not responding to a basic ping test.

It applies when local console, hypervisor, or other approved direct access to the server remains available and when first-line technicians are authorized to inspect and re-enable a disabled network adapter.

Technical issues that go beyond this defined troubleshooting scope should be documented and escalated to the appropriate team.

**Intended Audience**

This work instruction is designed for Command Center, Infrastructure Operations Center, Service Desk, or other IT technicians responsible for initial server connectivity validation and approved remediation.

**Required Access and Prerequisites**

- Access to the originating administrative workstation.
- Known hostname or IP address of the affected Windows Server.
- Approved local, virtual, or hypervisor console access to the Windows Server.
- Administrative permissions sufficient to inspect and enable network adapters.
- An active incident or service ticket.

**Cautions**

Making changes to or modifying the status of a network adapter can interrupt remote connectivity.

Confirm that console access is available before disabling, enabling, restarting, or otherwise modifying a server network interface.

Do not perform broader network, firewall, routing, VLAN, operating system, or infrastructure changes unless they are within your authorized scope. Document the findings and escalate the issue when it goes beyond the scope of this work instruction.


**Expected Outcome**

When this procedure is completed, the technician ought be able to determine whether a disabled Windows Server network adapter caused the reported connectivity failure.

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

**Incident Documentation and Ticket Update**

- Document the original reported condition, affected server, troubleshooting steps performed, test results, identified cause, remediation, and final validation results in the incident record.
- Record that the network adapter was found disabled, was successfully restored, and that connectivity was confirmed from the original administrative workstation.
- Update the ticket status and resolution notes according to organizational procedures.

**Resolution or Escalation**

- If connectivity has been restored and the reported issue is resolved, update the incident with the completed troubleshooting and validation results and close or resolve the ticket according to organizational procedures.
- If connectivity is not restored, or if additional troubleshooting falls outside the technician's authorized scope, document all findings and escalate the incident to the appropriate team.
- Include enough diagnostic detail in the escalation so the receiving team can continue troubleshooting without repeating completed first-line steps.

**Work Instruction Completion**

- Confirm that the final connectivity test has been completed and documented.
- Confirm that the incident record contains the troubleshooting steps, remediation, validation results, and final disposition.
- If service has been restored, resolve the ticket. If the issue remains unresolved or exceeds scope, complete the required escalation and handoff.

**END OF WORK INSTRUCTION**

***

**Lessons Learned**

- Effective work instructions should define scope, access, cautions, validation, and escalation boundaries in addition to technical steps.
- Decision trees help technicians understand why each troubleshooting step is being performed and what comes next.
- Troubleshooting should be structured and methodical, beginning with confirmation of the reported issue and moving outward layer by layer.
- Remediation should match the identified problem instead of introducing broader changes that are not necessary.
- Work instructions should be accurate, current, accessible, and reviewed regularly so they remain useful to technicians.

**Summary**

The objective of my project was to create a complete Windows Server 2022 Network Recovery Work Instruction that combines initial troubleshooting, validation, documentation, and escalation procedures. The procedure walks through confirming and validating that the server is not reachable by ping, testing the loopback address, identifying a disabled network adapter, restoring the adapter, validating its status, and then confirming connectivity from the original administrative workstation.

This project also demonstrates the value of standardized technical documentation in IT operations. By including a decision tree, clear scope boundaries, approved remediation steps, validation requirements, and ticket documentation expectations, the work instruction provides a repeatable process that can support troubleshooting consistency, IT technician onboarding, knowledge transfer, and more efficient incident resolution.

**Navigation**

[`Back to GitHub Profile`](https://www.github.com/cbueker-it)
