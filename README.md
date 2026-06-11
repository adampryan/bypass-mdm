# bypass-mdm

Scripts to bypass MDM enrollment on macOS devices you own that were enrolled by a previous owner or organization.

**Use case:** Bought a used Mac Mini with MDM still active. Not for bypassing MDM on devices you don't own.

---

## Quick start (Recovery Terminal)

Boot into 1TR: hold the **power button** until "Loading startup options" appears, then select **Options → Terminal**.

```bash
curl -fsSL https://raw.githubusercontent.com/adampryan/bypass-mdm/main/bypass-mdm-v2.sh -o /tmp/bypass.sh && bash /tmp/bypass.sh
```

Follow the prompts. When done, close Terminal and reboot.

---

## What the script does

1. Detects your system and data volumes automatically
2. Creates a local admin user (you choose the name/password)
3. Blocks Apple's MDM enrollment domains in `/etc/hosts` on the data volume
4. Writes the `cloudConfigProfileInstalled` / `cloudConfigRecordNotFound` markers so Setup Assistant skips DEP enrollment
5. Marks setup as complete so you land at the login screen, not Setup Assistant

## Apple Silicon note

Both scripts target the **data volume** for `hosts` and `ConfigurationProfiles` — not the sealed system volume (SSV). This matters on all Apple Silicon Macs (M1/M2/M3/M4) and Intel Macs running Catalina+, where `/etc` and `/var` are firmlinked to the data volume. Writing to the system volume would fail silently or break SSV verification.

## Activation Lock

If you see an Activation Lock screen before Setup Assistant, this script won't help — that's a separate Apple ID lock. You'll need the previous owner to release it, or contact Apple with proof of purchase.

## Scripts

| Script | Notes |
|--------|-------|
| `bypass-mdm-v2.sh` | Recommended — auto-detects volume names, input validation, error handling |
| `bypass-mdm.sh` | Legacy — assumes volume is named "Data" |

## License

MIT — original scripts by [Assaf Dori](https://github.com/assafdori/bypass-mdm), path fixes by adampryan.
