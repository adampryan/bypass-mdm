# bypass-mdm

Scripts to bypass MDM enrollment on macOS devices you own that were enrolled by a previous owner or organization.

**Use case:** Bought a used Mac Mini with MDM still active. Not for bypassing MDM on devices you don't own.

---

## Quick start (Recovery Terminal)

Boot into 1TR: hold the **power button** until "Loading startup options" appears, then select **Options → Terminal**.

```bash
rm -f /tmp/bypass.sh && curl -fsSL https://raw.githubusercontent.com/adampryan/bypass-mdm/main/bypass-mdm-v2.sh -o /tmp/bypass.sh && bash /tmp/bypass.sh
```

Select **1 — Bypass MDM from Recovery**. Accept the defaults (username: `Apple`, password: `1234`) or enter your own.

When it finishes, close Terminal and reboot.

---

## After reboot

1. Sign in with the temporary credentials (`Apple` / `1234` if you used defaults)
2. Skip any Apple ID prompts — click **Set Up Later** or **Don't sign in**
3. Go to **System Settings → Users & Groups** and create your real admin account
4. Log out, sign in with your real account, then delete the temporary `Apple` account

---

## Verify the bypass held

Open Terminal and run:

```bash
sudo profiles show -type enrollment
```

A successful bypass returns something like:

```
Bad response from apsd: Connection interrupted
Error fetching Device Enrollment configuration: We can't determine if this machine is DEP enabled.
```

That means the Mac tried to reach Apple's DEP servers, couldn't (because the hosts block is working), and gave up. You're clean.

---

## How it works

1. Creates a local admin user directly in the directory services database on the data volume
2. Marks Setup Assistant as complete so the Mac boots to login instead of enrollment
3. Blocks Apple's MDM enrollment domains in `/etc/hosts` on the **data volume** — not the sealed system volume
4. Writes `cloudConfigProfileInstalled` / `cloudConfigRecordNotFound` markers so DEP enrollment is skipped

### Apple Silicon note

Both scripts target the **data volume** for `hosts` and `ConfigurationProfiles`. On Apple Silicon (and Intel Catalina+), `/etc` and `/var` are firmlinked to the data volume — writing to the system volume would fail silently or break SSV verification.

---

## Activation Lock

If you see an Activation Lock screen before Setup Assistant, this script won't help — that's a separate Apple ID lock tied to the device serial number. You'll need the previous owner to release it, or contact Apple with proof of purchase.

---

## Scripts

| Script | Notes |
|--------|-------|
| `bypass-mdm-v2.sh` | Recommended — auto-detects volume names, input validation, error handling |
| `bypass-mdm.sh` | Legacy — assumes data volume is named "Data" |

## License

MIT — original scripts by [Assaf Dori](https://github.com/assafdori/bypass-mdm), fixes by adampryan.
