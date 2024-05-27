# Zero Touch Deployment using Kaseya VSA v9

### Overview

I worked in an environment of ~200 Windows systems where we were frequently onboarding new users. Not being a fully-AD environment, I leveraged the existing [Kaseya VSA](https://www.kaseya.com/products/vsa/) RMM system to create, as much as possible, a "zero-touch deployment" environment in order to minimize this repetitive, error-prone task.

Sidenotes:
- This environment relied on Google Workspace for overall IAM needs and, as a result, GWsp became the focal point of overall user account management and SSO functionality.
- The org's name was 'SQZ' and has since gone out of business, so any live, in-code references are non-fucntioning.

### Planning and Preparation

Mapping out every step of the process involved:

- carefully defining the entire (currently manual) process from start to finish
- identifying the requirements for two different system types (typical end user systems and those configured for used in a share laboratory space)
- defining upstream procedures to ensure that the initial system configuration would be successful (most notably, defining procedures for Google Workspace-based account creation)

### Initial Execution

Once every step was mapped out, we created an independent agent procedure for each step. This simplified troubleshooting and promoted modularity (for example: the same installation script for a given app could be repurposed outside of the 0TD focus).

Once that was done, we created two master procedures ("01. Universal System Setup" and "02. Universal User Setup") that, essentially, called the standalone procedures in a specific order in order to automatically build the system to a completed state.

Each of these two master procedures prompt the IT professional running them to ID whether or not the system is intended for 'lab' use or not; different scripts were run based on this determination.

### Actual Process

1. User accounts are created in Google Workspace (using a default password) and Egnyte
2. New (or reset) Windows computer is plugged via Ethernet and booted up
3. System is named according to defined spec and initial "admin" account is created using "IT eyes-only" password
4. Once at the desktop, all Windows OS and vendor-specific updates (including any applicable BIOS updates) are applied
5. The appropriate VSA enrollment installer is executed to enroll the system to VSA
6. From the VSA admin console, the "01. Universal System Setup" is run on the newly-enrolled system
7. Once the system reboots, the IT admin signs in using the intended user's credentials
8. Once at the desktop, the "01. Universal User Setup" procedure is run from the VSA admin console
9. Computer will restart once procedure is complete and the system is now ready for hand-off (they would be forced to reset their Google password when they initially logged in)

### Final Result

Once all procedures complete, you wind up with a standard end-user computer configured as follows:

- system's login management is controlled by [Google Credential Provider for Windows](https://tools.google.com/dlpage/gcpw) (GCPW)
- two onboard accounts: IT-only administrative account and end user-specific "Standard" account
- primary storage is BitLocker encrypted w/ keys stored in Kaseya (courtesy of [this fantastic set of BitLocker management scripts](https://community.kaseya.com/community-comstore/discussion/39659))
- core applications installed and auto-configured (Sophos A/V, Google Chrome, MS Office 365, Adobe Acrobat, Zoom, MS Teams, Egnyte, Sonicwall VPN)
- office printers and WiFi network auto-configured
- date/time configuration to auto-set based on location
- customized Windows settings to ensure max. screen resolution and screen effects turned off (to improve system response time)
- clean, branded desktop

### Pros/Cons

Pros:

- using Kaseya VSA for this process (and overall endpoint management) is significantly less expensive then using MS solutions
- significant reduction in build time
- complete consistency of system builds
- time put in to customizing app installs streamlines authentication process for end users ([Zoom](https://support.zoom.com/hc/en/article?id=zm_kb&sysparm_article=KB0064484) is a great example of this)
- simplified management going forward (complete collection of install scripts makes it easier for IT to push optional apps on an as-needed basis)
- consistent enrollment in Kaseya VSA RMM ensure improved management and reporting

Cons:

- this is not true zero-touch deployment; the lack of [AutoPilot](https://learn.microsoft.com/en-us/autopilot/windows-autopilot) enrollment still requires initial manual configuration
- the local admin account is not directly owned by the account authority (Google Workspace, in this scenario)
- Kaseya VSA v9 is a total PitA to work with; the learning curve is ludicrous and the UI is needlessly complicated
- Kaseya VSA system v10 is a complete overhaul of the agent procedure paradigm and these procedures are not fully-compliant
- VSA agent procedures a kind of twitchy; master procedures will occasionally break mid-process inexplicably. Running the individual procedure where it stopped works normally, making it difficult to ID where the issue lies (fortunately, this was a rare occurrence)

### Screenshot of Agent Procedure Collection

_Procedure names beginning with an asterisk ('*') indicate procedures that were not yet completed/didn't pass testing..._

![Agent Procedures](https://github.com/chrisjscott/kaseya-vsa9-zeroTouchDeploy/blob/main/agentProcedures-myProcedures.png)
