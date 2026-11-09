# Windows 11 26H2 Cloud PC Image Updater

A lightweight Windows desktop tool for **Windows 365** administrators to update the gallery image on a provisioning policy and reprovision the affected Cloud PCs — without touching PowerShell or the Intune portal.


> **Download only.** This repository distributes the ready-to-run application. Source code is not published.

---

## Download

Grab the latest build from the [`dist`](dist) folder:

| File | Description |
|---|---|
| `W365ImageUpdater.exe` | Self-contained application (no .NET install required) |
| `appsettings.json` | Optional configuration (tenant / client ID) |

Place both files in the same folder, then run `W365ImageUpdater.exe`.

> Windows SmartScreen may show "Windows protected your PC" for a newly downloaded executable. Click **More info → Run anyway**.

---

## Requirements

- Windows 10 / 11 (64-bit)
- Internet access to `graph.microsoft.com` and `login.microsoftonline.com`
- A work account with permission to manage Windows 365, e.g.:
  - **Intune Administrator**, **Windows 365 Administrator**, or **Global Administrator**
- A Windows 365 licence and at least one provisioning policy in the tenant

No .NET runtime installation is needed — the executable is self-contained.

---

## Quick start

1. Launch `W365ImageUpdater.exe`.
2. Click **Connect to Microsoft Graph** in the header.
3. Sign in with your work account in the browser window that opens and consent to the requested permissions.
4. Under **1. Provisioning Policy**, pick the policy you want to change. The policy ID and its current image are shown.
5. Under **2. Windows 11 26H2 Gallery Image**, pick the target gallery image.
6. Click **Update Provisioning Policy** and confirm.
7. Optionally click **Reprovision Cloud PCs** to select which Cloud PCs on that policy should be rebuilt on the new image.

All actions are timestamped in the **Activity Log** at the bottom of the window.

---

## Permissions requested

On first sign-in you will be asked to consent to:

| Scope | Why |
|---|---|
| `CloudPC.ReadWrite.All` | Read provisioning policies and gallery images, update the policy image, trigger reprovision |
| `User.Read` | Display the signed-in account name |

If your tenant requires admin consent, an administrator must approve these scopes once.

---

## Configuration (optional)

By default the app signs in using the well-known **Microsoft Graph PowerShell** public client, and detects your tenant automatically. No setup is required.

To use your own Entra ID app registration instead, edit `appsettings.json` next to the executable:

```json
{
  "AzureAd": {
    "TenantId": "00000000-0000-0000-0000-000000000000",
    "ClientId": "00000000-0000-0000-0000-000000000000"
  }
}
```

Your app registration must be configured as follows:

- **Platform:** Mobile and desktop applications
- **Redirect URI:** `http://localhost`
- **Allow public client flows:** Yes
- **Delegated permissions:** `CloudPC.ReadWrite.All`, `User.Read`

Leave `TenantId` as `organizations` to let the tool resolve the tenant from the signed-in account.

---

## Reprovisioning notes

> ⚠️ **Reprovisioning erases the Cloud PC.** The desktop is rebuilt from the selected image and all locally stored data on that Cloud PC is lost.

- Notify users and confirm that their data is stored in OneDrive / network locations first.
- Updating the provisioning policy image alone does **not** change existing Cloud PCs — only newly provisioned ones. Use **Reprovision Cloud PCs** to move existing machines onto the new image.
- Reprovisioning runs asynchronously; progress is visible in the Intune admin center.

---

## Troubleshooting

| Message | What to do |
|---|---|
| `AADSTS500113: No reply address is registered` | Your custom app registration is missing the `http://localhost` redirect URI. Add it, or remove `appsettings.json` to fall back to the default client. |
| `403 Forbidden` | The signed-in account lacks Windows 365 / Intune administrator rights. |
| No policies or images listed | The tenant has no Windows 365 provisioning policies, or the account cannot see them. Click **Refresh Policies & Images**. |
| SmartScreen blocks the app | Click **More info → Run anyway**, or unblock via file **Properties → Unblock**. |
| Sign-in window never appears | Check that a default browser is set and that `login.microsoftonline.com` is reachable. |

---

## Disclaimer

This tool is provided as-is, with no warranty. It performs real, irreversible changes against your tenant. Always validate against a test provisioning policy before using it in production. It is not affiliated with or endorsed by Microsoft.

---

## Licence

Released under the MIT Licence — see [LICENSE](LICENSE).

© 2026 Vaishnav K · [LinkedIn](https://www.linkedin.com/in/vaishnav-k-957b0589/)
