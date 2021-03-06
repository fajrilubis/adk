# App Data Keeper (adk)
## Copyright (C) 2018, VR25 @ xda-developers
### License: GPL v3+



---
#### DISCLAIMER

This software is provided as is, in the hope that it will be useful, but without any warranty. Always read/reread this reference prior to installing/upgrading. While no cats have been harmed, I assume no responsibility under anything which might go wrong due to the use/misuse of it.

A copy of the GNU General Public License, version 3 or newer ships with every build. Please, read it prior to using, modifying and/or sharing any part of this work.

To prevent fraud, DO NOT mirror any link associated with this project.



---
#### DESCRIPTION

This module protects select apps+data from being wiped out on a regular TWRP factory reset. Thus, greatly reducing the amount of effort necessary for setting up a new system.

Additionally, adk is a full backup solution, thanks to rsync and OpenSSH capabilities.



---
#### PRE-REQUISITES

- Magisk v15+
- Terminal emulator



---
#### CONFIG


Config file: /data/media/adk/config.txt


SYNTAX

  Incremental apps data backup frequency in hours (value must be an integer, default: 8)

      bkpFreq=8

  Include all user and *updated* system apps

      inc

  Include pkgName (this works for any app, and it's the only way to *include non-update-able system apps*)

      inc pkgName

  Exclude *user* app (overrides "inc" and "inc pkgName")

      exc pkgName

  Advanced incremental, scheduled backups (rsync -rtu --inplace $bkp_line)

      bkp <extra rsync option(s)> <SOURCE(s)> <DEST>

    Tip: use $i for internal storage and $e for external media (largest partition) as opposed to writing full paths.

    For rsync-specific details, refer to its man page.

  Wipe /data (exc. adb/, data/.*provider.*/, media/, misc/(adb/|vold/|wifi/), ssh/ and system(""|.*)/(0/accounts.*|storage.xml|sync/accounts.*|users/)) and /cache (exc. magisk.*img and magis_mount/) after apps+data migration (untested on encrypted data)

      wipe

  Do not migrate/auto-restore apps+data

      noauto


EXAMPLES

  App data protection setups

    All user and *updated* system apps, except Spotify

      inc

      exc com.spotify music

    Only stock apps matching "mail" (non-update-able)

      inc mail

    Stock terminal, plus all user & *updated* system apps, except Spotify

      inc

      inc term

      exc com.spotify.music

    All user and *updated* system apps, except *updated* Google Play Services

      inc

      exc com.google.android.gms

  Backup setups

    Full internal storage

      bkp --del $i/ $e/full_internal_bkp

    Specific data

      bkp --del $i/Download $i/Dukto $e/important_data

    Some data to some remote machine

      bkp -e "ssh -i /path/to/key" SOURCE user@host:/DESTINATION

    Sync all backed up apps and respective data to a remote machine

      bkp -e "ssh -i /path/to/ssh/key" $appBkps $appdataBkps user@host:/DESTINATION


NOTES/TIPS

  A bare "inc" affects user and updated system apps only.

  An empty/null config disables all features.

  Any line containing leading and/or trailing pounds/spaces and/or any other additional characters is ignored.

  Instead of having multiple inc/exc lines, globbing/regex patterns can be used to match multiple packages (i.e., "exc google", "inc sp.*fy|ctionary|mail", without quotes).

  Only inc'd (included) apps are backed up.

  The word "provider" matches all packages which store/provide contacts, SMSs/MMSs, call logs, etc..

  Updated system apps are treated as user apps.

  When the "wipe" feature is enabled, adb/, data/.*provider.*/, media/, misc/(adb/|vold/|wifi/), ssh/, system(""|.*)/(0/accounts.*|storage.xml|sync/accounts.*|users/) and /cache/(magisk.*img|magisk_mount/) also survive factory resets. Note that all Magisk modules are preserved across adk factory resets. WARNING: "wipe" hasn't been tested on encrypted data! Thus,it's disabled by default. Leave it alone if you don't have at least a recent FULL (inc. internal media) /data backup on a different storage device!


DEFAULT CONFIG

inc
inc term|provider



---
#### TERMINAL

Running `adk` as root launches "adk wizard". Included options are incremental backups, data restore and more.



---
#### SETUP STEPS

- Install
1. Flash the zip from Magisk Manager or TWRP.
2. Reboot.
3. Customize config.txt (optional).

- Uninstall
1. Reflash the same version from Magisk Manager or use Magisk Manager for Recovery Mode or use the Magisk Manager app itself.
2. Reboot.
3. Remove /data/media/adk and/or <external storage>/adk (optional).

- Factory reset
1. Reflash the same version from TWRP to migrate apps+data.
2. Perform the *standard* TWRP factory reset (skip this if "wipe" is enabled).
3. Install new ROM (optional)
4. Reboot.
  *Notes*: if the "wipe" feature is enabled, all Magisk modules, plus adb keys, ssh config & keys and a bunch of other system data are preserved across factory resets. Unless the 'noauto' config keyword is set, all migrated apps+data are automatically restored shortly after boot.
  *WARNING*: "wipe" hasn't been tested on encrypted data! Thus, it's disabled by default. Leave it alone if you don't have at least a recent FULL (inc. internal media) /data backup on a different storage device!



---
#### DEBUGGING & ADVANCED INFO

Apps are temporarily disabled during respective data restore.

$bkpFreq for "bkp <extra rsync option(s)> <SOURCE(s)> <DEST>" is $((bkpFreq + 3600)). That is, one hour after the set value. This prevents conflicts with bkp_appdata().

How does this data protection thing actually work? At a glance, adk migrates select apps+data to /data/media/adk/migrated_data, so that these are unaffected by the standard TWRP factory reset. The automatic restore begins shortly after boot. When the "wipe" feature is enabled, all Magisk modules, plus adb keys, ssh config & keys and a bunch of other system data also survive factory resets. WARNING: "wipe" hasn't been tested on encrypted data! Thus, it's disabled by default. Leave it alone if you don't have at least a recent FULL (inc. internal media) /data backup on a different storage device!

If /data/media/adk/config.txt is missing, $modPath/default_config.txt is automatically copied to that location.

If the file /data/.adk exists, adk reinstalls itself. No data migration/wipe is performed.

logsDir: /data/media/adk/logs

When external storage is detected, adk uses the largest partition for apps+data backups. Only the inc'd are backed up. After a factory reset, /data/media/adk/backups and/or <external storage>/adk/backups folders are renamed to backups.old. Thus, in case the automatic restore fails, backups won't be overwritten and adk wizard (option 3) can be used alternatively to restore apps+data. The automatic restore functions have a fail-safe mechanism -- run at most 3 more times as necessary. Apps usually fail to install due to missing dependencies and/or incompatible Android version.

The "Test backupd()" option in adk wizard is meant for running all scheduled backups at any time (useful for debugging).



---
#### LINKS

- [Facebook Support Page](https://facebook.com/VR25-at-xda-developers-258150974794782)
- [Git Repository](https://github.com/Magisk-Modules-Repo/App-Data-Keeper)
- [XDA Thread](https://forum.xda-developers.com/apps/magisk/magisk-module-app-data-keeper-adk-t3822278)



---
#### LATEST CHANGES

**2018.9.22 (201809220)**
- Exclude $modData and <external storage>/$MODID from media scan.
- Use legacy variable increment syntax to prevent malfunction in legacy shells.
- White-list irrelevant 'chmod' and 'chown' errors causing some manual restores to fail.

**2018.9.19 (201809190)**
- Fixed 'wrong instruction to create $modData upon install'.
- Fixed 'rsync_util(), bad i variable'
- Magisk 17.1 support
- Performance and reliability improvements
- Suppress irrelevant 'ln -s' error messages.
- Wizard, 3 -- option to choose whether already installed apps should be filtered out.

**2018.9.14 (201809140)**
- Added support for odd package suffixes (/data/app/pkgName<suffix>) causing apps+data backup/restore to fail.
- Fixed 'noauto config keyword not being properly recognized'.
- On nonzero exit code, revert changes to the recovery environment and don't leave magisk image mounted.
- Save \$Pkg symlinks to \$appDataBkps/\$Pkg.lns (formerly \$appDataBkps/\$Pkg/symlinks.list) for faster rsync update checks (faster incremental apps data backups).
- The dedicated factory reset mechanism wipes /cache as well, exc. magisk.*img (for compat. with 'f2fsfix' module) and magisk_mount/.
