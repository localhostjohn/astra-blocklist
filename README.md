# Astra DNS Blocklist

A conservative, custom DNS-filtering list for my Astra home lab, designed for AdGuard Home. This repository documents a small part of my infrastructure learning: DNS filtering, change control, troubleshooting and maintaining a repeatable configuration.

## What this project contains

The [`astra-blocklist.txt`](./astra-blocklist.txt) file contains AdGuard/Adblock-style domain rules. The list is intended to supplement, rather than replace, AdGuard Home's standard protection. It avoids deliberately blocking major streaming, operating-system and CDN services, although any DNS filter can still cause false positives.

The file includes rules for advertising and tracking networks, optional rules that are disabled by default, and example allowlist syntax. The current list is a home-lab configuration, not a professionally validated threat-intelligence feed or a guarantee of malware protection.

## Add the list to AdGuard Home

1. Open your own AdGuard Home administration interface.
2. Go to **Filters → DNS blocklists** and choose **Add blocklist → Add a custom list**.
3. Enter a name such as `Astra Custom Blocklist` and use the following URL:

```text
https://raw.githubusercontent.com/localhostjohn/astra-blocklist/main/astra-blocklist.txt
```

4. Save the list and allow AdGuard Home to download it.
5. Check the filter status and test normal browsing, sign-in and streaming on your own devices.

Do not expose your AdGuard administration interface publicly or publish its credentials. Use your approved management network or private remote-access solution.

## Troubleshooting and change control

If a site or application stops working after a filter change, check **Query Log** for blocked requests around the time of the failure. Temporarily disable the custom list to determine whether it is responsible, then re-enable it and add the narrowest appropriate allowlist rule if necessary. Avoid broadly allowing entire services when a single domain is sufficient.

For a new rule, document the reason for blocking, test it on the lab, and verify that it does not interfere with expected services. Keep changes small so they can be reviewed and reverted. A Git commit provides a useful record of what changed and why.

DNS filtering cannot reliably remove in-stream advertising from services such as YouTube, and it does not replace browser security, endpoint protection, patching or network segmentation.

## Infrastructure skills demonstrated

- DNS resolution and network-wide filtering concepts.
- Managing a service configuration as version-controlled text.
- Troubleshooting false positives through logs and controlled changes.
- Applying least-privilege principles to management access.
- Maintaining documentation without publishing private network details.

## Status and limitations

This is an evolving personal lab project. The repository contains the blocklist and its documentation; it is not a complete AdGuard Home deployment, automated test suite or production change-management system. Rule effectiveness and compatibility should be assessed in the environment where the list is used.

## References

- [AdGuard Home](https://github.com/AdguardTeam/AdGuardHome)
- [AdGuard Home Wiki](https://github.com/AdguardTeam/AdGuardHome/wiki)
- [AdGuard DNS filtering syntax](https://adguard-dns.io/kb/general/dns-filtering-syntax/)

---

Maintained by John Weekes as part of the Astra home lab. All examples and documentation are intended for personal learning; no employer infrastructure or internal configuration is represented here.
