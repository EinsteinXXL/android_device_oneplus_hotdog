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
- [X] ADB (+ sideload) - pre-decrypt and post-decrypt
- [X] all important partitions listed in mount/backup lists
- [X] MTP export
- [X] decrypt /data - OOS10, OOS11, and custom A10+A11 ROMs with FBEv1
- [X] Backup to internal/microSD (with encryption)
- [X] Restore from internal/microSD (with decryption)
- [X] F2FS/EXT4 Support, exFAT/NTFS where supported
- [X] backup/restore to/from external (USB-OTG) storage
- [X] update.zip sideload
- [X] backup/restore to/from adb

##### Credits
- CaptainThrowback for original trees
- mauronofrio for original trees
- Systemad for OOS decryption fixes
- TWRP Team
- EinsteinXXL for fork maintenance and USB gadget/encrypted backup fixes
