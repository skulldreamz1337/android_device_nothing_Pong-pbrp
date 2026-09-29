# Patches for PBRP source (android-12.1)

These change PBRP's recovery source, not this device tree.
Apply them from the root of your PBRP source tree before building.

## recovery-unmap-inactive-slot-super.patch

Fixes **Format Data failing** with `E:Unable to check merge status`
on Virtual A/B devices (tested on Nothing Phone 2, Pong).

**Cause:** `Unmap_Super_Devices()` only unmapped the active slot's
dynamic partitions (e.g. `system_a`). The inactive slot's copies
(`system_b` etc.) stayed mapped. libsnapshot's `HandleImminentDataWipe`
then tried to map both slots, `DM_DEV_CREATE` failed with EBUSY on
`system_b`, the merge check failed, and Format Data aborted before
wiping anything.

**Fix:** also unmap the inactive slot's copy when it is mapped.
Only device-mapper mappings are removed; nothing in `super` is deleted.
Because the "Unmap Super Devices" button calls the same function,
it is fixed too.

**Apply:**

    git -C bootable/recovery apply device/nothing/Pong/patches/recovery-unmap-inactive-slot-super.patch

**Tested:** Format Data succeeded, Android re-encrypted with new keys,
PBRP decrypted with a new PIN, and a data backup restored and booted.
