# MS2109 RTSP build — OpenWrt 22.03.5 / MT7621

This GitHub Actions project cross-builds `v4l2rtspserver` with ALSA enabled for:
- OpenWrt 22.03.5
- target `ramips/mt7621`
- architecture `mipsel_24kc`

No Ubuntu, WSL, Docker, or other system needs to be installed on your Windows 10 PC. GitHub Actions does the build remotely.

## Build instructions

1. On GitHub, create a new empty repository.
2. Extract this ZIP and upload its contents to the repository. Keep the `.github/workflows/build.yml` path unchanged.
3. Open the repository's **Actions** tab and enable Actions if asked.
4. Select **Build MS2109 RTSP server (OpenWrt 22.03.5)**.
5. Click **Run workflow** and wait for completion.
6. Download the artifact named `ms2109-openwrt-22.03.5-mt7621`.

The workflow downloads the official matching SDK and verifies its published SHA256 before building.

## Install on the router

Upload the generated `v4l2rtspserver_*.ipk` to `/tmp` on the router, then run:

```sh
opkg install /tmp/v4l2rtspserver_*.ipk
v4l2rtspserver -h
```

Do not start the stream until the help output contains an `ALSA options` section. If it does not, the build did not enable audio support and we should inspect the GitHub Actions log.

The router's already-tested capture modes are:
- Video: `/dev/video0`, MJPEG, 1280x720, approximately 29 fps at a requested 30 fps.
- Audio: `hw:0,0`, S16_LE, 48000 Hz, stereo.

## Intended combined stream

The upstream project documents a single RTSP session with both tracks using the device syntax `V4L2-device,ALSA-device`, for example `/dev/video0,default`. After checking the actual ALSA options in the newly built binary, we will set the exact audio parameters and launch command. The expected default RTSP URL is commonly `rtsp://ROUTER_IP:8554/unicast`; use the URL printed by the server as authoritative.

This package build does not flash firmware or change router configuration.
