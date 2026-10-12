# AIC8800 SDIO WiFi driver for Orange Pi Zero 3W (A733/sun60iw2)

Out-of-tree port of the AICSemi AIC8800 BSP for the Allwinner A733 SoC,
targeting Linux 7.3-rc2 mainline tree.

## Status: WORKING (2026-10-11)

Verified end-to-end on hardware:
- SDIO bus up at 50MHz (HSSDR52/SDR25, 4-bit)
- Firmware uploaded, start_app ret=0, bootstatus=0x00
- wlan0 created, scan, association (WPA2-PSK), DHCP, ping, SSH all work

## Building

```
make -j$(nproc) ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- \
  M=drivers/net/wireless/aic8800/aic8800_fdrv modules
```

## Key fix: DBG confirm stubs

`rwnx_msg_rx.c` was missing no-op handlers for DBG_*_CFM messages.
Without them every confirm hit cb=NULL and was dropped, the command queue
latched `crashed`, and userspace saw -EBUSY. See
`aic8800_fdrv-src/rwnx_msg_rx.c` (rwnx_rx_dbg_null_handler).
