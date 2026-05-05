# Device Tree for OnePlus 7T Pro aka Hotdog for TWRP (this should be unified with 7T, but I cannot test since I do not own that device)
## Disclaimer - Unofficial TWRP!
These are personal test builds of mine. In no way do I hold responsibility if it/you messes up your device.
Proceed at your own risk.

### Note
Decryption is working for custom ROMs with OOS blobs, both for ROMs with sdcardfs and FBEv1 encryption.

## Setup repo tool
Setup repo tool from here https://source.android.com/setup/develop#installing-repo

## Compile

Sync TWRP manifest (twrp-11 minimal):

```
repo init -u git://github.com/minimal-manifest-twrp/platform_manifest_twrp_aosp.git -b twrp-11
```

Make a directory named local_manifest under .repo, and create a new manifest file, for example hotdog.xml
and then paste the following

```xml
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
<remote name="github"
	fetch="https://github.com/" />

<project path="device/oneplus/hotdog"
	name="EinsteinXXL/android_device_oneplus_hotdog"
	remote="github"
	revision="main" />

<project path="bootable/recovery"
	name="EinsteinXXL/android_bootable_recovery"
	remote="github"
	revision="einsteinxxl-twrp-fixes" />
</manifest>
```

Sync the sources with

```
repo sync
```

To build, execute these commands in order

```
. build/envsetup.sh
export ALLOW_MISSING_DEPENDENCIES=true
export LC_ALL=C
lunch twrp_hotdog-eng && make clean && mka adbd recoveryimage
```

To test it:

```
# To temporarily boot it
fastboot boot out/target/product/hotdog/recovery.img 

# Since 7T Pro has a dedicated recovery partition, you can flash the recovery with
fastboot flash recovery recovery.img
```

#### Working
- [X] Flashing ROMs (AOSP and OOS)
- [X] ADB - pre-decrypt and post-decrypt
- [X] all important partitions listed in mount/backup lists
- [X] MTP export
- [X] decrypt /data - OOS10, OOS11, and custom A10+A11 ROMs with FBEv1
- [X] Backup to internal/microSD (with encryption)
- [X] Restore from internal/microSD (with decryption)
- [X] F2FS/EXT4 Support, exFAT/NTFS where supported
- [X] backup/restore to/from external (USB-OTG) storage
- [X] Sideload (ROM, Magisk, Kernel via ADB)
- [X] backup/restore to/from adb

##### Credits
- CaptainThrowback for original trees
- mauronofrio for original trees
- Systemad for OOS decryption fixes
- TWRP Team
- EinsteinXXL for fork maintenance and USB gadget/encrypted backup fixes
## Applied Patches & Commits

### EinsteinXXL Custom Patches (Base)
- **ad9604c** — Refactored encrypted backup/restore – deadlock-free and fully working
- **a2b9fe1** — Additional backup/restore stability improvements
- **a7c73e3** — Tar operation resource leak & error handling fixes

### Gerrit Cherry-picks from android-12.1
| Commit | Original | Content |
|--------|----------|---------|
| 0f3298cc | 78914ca6 | sepolicy: fix avc denials for loop mount |
| 64f71764 | 0552fad6 | f2fs: fix repair |
| 17061196 | e4b807b9 | Is_Mounted: fix symlink detection |
| 77209cc7 | dbed985d | selinux context for backups/logs in /data/recovery |
| a64cfde6 | b2a1f930 | fscrypt ENOKEY: ignore error and continue |

### Gerrit Cherry-picks from android-13
| Commit | Original | Content |
|--------|----------|---------|
| 39a128d3 | b6867185 | Exclude /data/gsi directory from userdata backup (prevents huge DSU images) |
| 7c0739c0 | a8f9817b | Exclude package-restrictions.xml (fixes boot issues after restore) |
| 9c802e12 | 5ec04efc | Don't treat non-existing logical partitions as errors |

