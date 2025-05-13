# ASUS RT-AX86U Pro Router Tweaks

> This repository is a fork of [francoism90/asus-router](https://github.com/francoism90/asus-router) specifically adapted for the ASUS RT-AX86U Pro router model.

# Disclaimer

> Following instructions are provided without any warranty, and may even get you in trouble legally.<br>
> The instructions are provided for testing, learning, preventing e-waste, and should be use with care.<br>
> We (including contributers + commentators) are not responsible for any damage to your device(s) or any legal issues.

## Introduction

This repository provides information on how to enable additional or change WiFi-channels, and TX-power on the ASUS RT-AX86U Pro router. It also offers some tweaks for AiMesh nodes if you're using them with this router model.

The purpose is to adjust your router to the legal state of your country (e.g. you bought an ASUS router in one region, and want to re-use it in another). The current method focuses on exposed nvram variables, and overrules them when a service or event has been restarted or dispatched.

Please note adjustments may cause connectivity issues and may have a performance impact. It's advisable to test each value (overwrite), check your router's temperatures and validate if it actually works for you.

The adjusted nvram settings in this repository have been tested specifically on the ASUS RT-AX86U Pro with Asuswrt-Merlin firmware.

> Note: Please do not ask for any support on the SNBForums, ASUS or any (official) Merlin related projects about these tweaks. See the Troubleshooting section to reset any changes, or remove any adjustments first if you need to ask for support/help.

> Note: Most modern wireless devices use something called Location Aware Regulatory (LAR), and other hidden signals to check the origin of the country.
> This means that even changing/forcing your router's WiFi-settings, your clients may choose to ignore it (use a fallback-mode) or do not connect at all. See troubleshooting if this happens in your case.

A massive shoutout goes out to the contributes on the original [gist](https://gist.github.com/francoism90/3dede7973354d067c41bff5e54203fe9/), and members of the [SNBForums](https://www.snbforums.com/) for findings and information about certain parameters.

## Getting started

> See <https://www.htpcguides.com/enable-ssh-asus-routers-without-ssh-keys/> for instructions. The guide explains both the usage of a password and SSH-keys.

Login into router using SSH (password or key):

1. Run `nvram dump > dump.txt`
2. Use `scp` to copy it to your working machine or copy the contents of `nvram dump` to somewhere safe.
3. Make sure `Enable JFFS custom scripts and configs` is enabled and working in System settings.
4. Reboot router

### User-scripts

> Tip: If your router or AP doesn't support Asuswrt-Merlin, checkout [asuswrt-scripts](https://github.com/jacklul/asuswrt-scripts/).

1. Follow <https://github.com/RMerl/asuswrt-merlin.ng/wiki/User-scripts>.

1. Make sure JFFS has been enabled:

```bash
nvram set jffs2_on=1
nvram set jffs2_scripts=1
nvram commit
reboot
```

1. Create/update the required `/jffs/scripts` files, see [given example](https://github.com/francoism90/asus-router/tree/main/jffs/scripts) for details.

1. Update your device `nvram` overwrites into the `/jffs/scripts/wlupdate` file (uncomment lines starting with `#`).

1. Update `/jffs/scripts/wlboost` if you want to force more channel steering (uncomment lines starting with `#`).

1. Make sure scripts are executable:

```bash
chmod a+rx /jffs/scripts/*
```

1. Apply nvram overwrites, and register the cronjob:

```bash
/jffs/scripts/wlupdate
```

1. If the overrules work, you can make them persistent on reboots:

```bash
nvram commit
reboot
```

## Testing

To validate the wireless settings, you may want to use [com.vrem.wifianalyzer](https://play.google.com/store/apps/details?id=com.vrem.wifianalyzer) or any other wireless tool.

To restart the wireless service, use `service restart_wireless` or by using the scMerlin addon.

The script `/jffs/scripts/wlboost` may be useful to force channel steering. While the ASUS firmware uses nvram settings, it may choose to ignore them. This script forces wireless interfaces to follow certain parameters, but may cause your wireless to go down for a few seconds.

You may also want to use _System Log_ for diagnostics:

![image](https://github.com/user-attachments/assets/3775cac7-58e0-4333-b0a9-4dcec775c9fc)

## Troubleshooting

It's possible to restore factory nvram settings by using a hard reset:

- <https://www.asus.com/support/faq/1039077/>
- <https://www.asus.com/support/faq/1039078/>

This will clear all overwrites and restores factory defaults.
