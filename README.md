# GraphRunner
![GraphRunner](https://github.com/dafthack/GraphRunner/assets/2296229/d9b6843c-8c69-4d9d-bed4-38e5e2269574)


GraphRunner is a post-exploitation toolset for interacting with the Microsoft Graph API. It provides various tools for performing reconnaissance, persistence, and pillaging of data from a Microsoft Entra ID (Azure AD) account. 

It consists of three separate parts: 
* A PowerShell script where the majority of modules are located
* An HTML GUI that can leverage an access token to navigate and pillage a user's account
* A simple PHP redirector for harvesting authentication codes during an OAuth flow

****

## Main Features
* Search and export email
* Search and export SharePoint and OneDrive files accessible to a user
* Search all Teams chats and channels visible to the user and export full conversations
* Deploy malicious apps
* Discover misconfigured mailboxes that are exposed
* Clone security groups to carry out watering hole attacks
* Find groups that can be modified directly by your user or where membership rules can be abused to gain access
* Search all user attributes for specific terms
* Leverage a GUI built on the Graph API to pillage a user's account
* Dump conditional access policies
* Dump app registrations and external apps including consent and scope to identify potentially malicious apps
* Tools to complete OAuth flow during consent grant attacks
* GraphRunner doesn't rely on any third-party libraries or modules
* Works with Windows and Linux
* Continuously refresh your token package
* Custom ClientID, Device, and Browser parameters with User-Agent spoofing across all Graph API functions

****

## User-Agent Spoofing & Custom ClientID Support

All functions that interact with the Microsoft Graph API now support custom **ClientID**, **Device**, and **Browser** parameters. This enables User-Agent spoofing across every request, which is useful for:

* **Bypassing Conditional Access Policies (CAPs)** — Device-based CAPs can be evaded by spoofing the User-Agent to match an allowed device/browser combination (e.g., Windows + Edge, iPhone + Safari).
* **Avoiding 429 Rate Limiting** — Microsoft's rate limiting is applied per-client. By rotating the ClientID and varying the User-Agent across requests, you can distribute traffic across multiple client identities and reduce the likelihood of hitting rate limits during enumeration or data exfiltration.
* **Blending In** — Custom User-Agents make requests appear as legitimate browser traffic, reducing the chance of detection by security monitoring tools.

### Parameters

| Parameter | Description | Example Values |
|-----------|-------------|----------------|
| `-ClientId` | The application (client) ID to use for requests. Defaults to the Microsoft Office client ID. | `d3590ed6-52b3-4102-aeff-aad2292ab01c` |
| `-Device` | The device type to spoof in the User-Agent string. | `Mac`, `Windows`, `AndroidMobile`, `iPhone` |
| `-Browser` | The browser to spoof in the User-Agent string. | `Android`, `IE`, `Chrome`, `Firefox`, `Edge`, `Safari` |

### Example

```PowerShell
# Read inbox while spoofing an iPhone Safari User-Agent
Get-Inbox -Tokens $tokens -userid user@target.com -Device iPhone -Browser Safari

# Enumerate Teams channels with a custom ClientID and Windows Chrome UA
Get-TeamsChannels -Tokens $tokens -ClientId "1fec8e78-bce4-4aaf-ab1b-5451cc387264" -Device Windows -Browser Chrome

# Search mailbox with Android device spoofing
Invoke-SearchMailbox -Tokens $tokens -SearchTerm "password" -Device AndroidMobile -Browser Chrome
```

### Updated Functions

The following 14 functions support these parameters:

1. **Get-Inbox** — Mailbox reading
2. **Get-TeamsApps** — Teams app enumeration
3. **Get-TeamsChannels** — Teams channel enumeration
4. **Get-ChannelUsersEnum** — Channel user enumeration
5. **Invoke-InviteGuest** — Guest user invitations
6. **Get-SharePointSiteURLs** — SharePoint site discovery
7. **Invoke-SearchMailbox** — Mailbox search
8. **Invoke-InjectOAuthApp** — OAuth app injection
9. **Invoke-DeleteOAuthApp** — OAuth app deletion
10. **Invoke-GraphOpenInboxFinder** — Open inbox discovery
11. **Get-ChannelEmail** — Channel email management
12. **Find-ChannelEmails** — Channel email discovery
13. **Get-Webhooks** — Webhook enumeration
14. **Create-Webhook** — Webhook creation

****
## Usage

As GraphRunner is a post-exploitation tool most of the modules rely on having authenticated access tokens. To assist with this there are multiple modules for obtaining and working with both user and application (service principal) tokens. 

A good starting place is to import the PowerShell script and run the Get-GraphTokens module.

```PowerShell
Import-Module .\GraphRunner.ps1
Get-GraphTokens
```
Next, check out the [wiki](https://github.com/dafthack/GraphRunner/wiki) for the full user guide and information about individual modules. 
