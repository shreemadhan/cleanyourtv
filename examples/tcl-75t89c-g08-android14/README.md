# TCL 75T89C (G08, Google TV, Android 14), India firmware

TCL 75" 75T89C, model "Smart TV Pro", device `G08`, product `G08_4K_GB`, firmware `UTT2.250416.001 AU02`
(security patch 2025-12-05), MediaTek `tcl9618`, about 2.4 GB RAM, Google TV home (`launcherx`). The same G08
board as [`tcl-g08-us-google-tv`](../tcl-g08-us-google-tv/), on the India build.

| File | What it is |
|---|---|
| `debloat.txt` | The 29 packages that were disabled, grouped and commented |

**How much testing is behind it:** one set, 30 October 2026, about one day of use. Disabled by hand with
`pm disable-user --user 0` in small batches with a screenshot after each; the first 26 were compared with
`pm list packages -d` before and after one reboot and were identical. Home, YouTube, JioHotstar, TiviMate and
Prime Video opened afterwards (playback was not tested). The remote's Home, Input, Settings and app buttons were pressed on the
real remote and worked with the Clean Your TV launcher as Home. Not checked: Live TV (no aerial tested), AirPlay and
Miracast sessions, Chromecast casting after the changes, and weeks of use.

## What is different from the US G08 list

- No US streaming preloads. The India set ships Netflix, YouTube, YouTube Music, Prime Video and JioHotstar; all
  were kept here by choice.
- TCL content apps feed the "TCL" tab on stock Home: `com.tcl.channelplus`, `com.tcl.waterfall.overseas` and
  `com.tcl.usercenter` are in the list and the tab went with the stock launcher.
- Kept on purpose: voice (`katniss`), Chromecast (`mediashell`), AirPlay, Miracast, the firmware updater, and the
  Source key chain `com.tcl.suspension`, `com.tcl.interactive` and `com.tcl.ttvs` (see the EU G08 list for what
  breaks without them).

## Notes for this set

- The saved Wi-Fi network belonged to `com.google.android.tungsten.setupwraith`
  (`dumpsys wifi | grep -o 'cname=[^ ]*'`). Forget it and join it again in Settings before disabling the wizard;
  after that the same command named `com.android.tv.settings`.
- `cmd package set-home-activity` printed `Success` and changed nothing while `launcherx` was enabled. Home only
  moved to the new launcher after `launcherx` and then `setupwraith` were disabled.
- Network ADB answered on port 5555 once debugging was authorised; Wireless debugging used a different port on each
  start, and its pairing-only port refuses `adb connect`.
- `settings put global adb_wifi_enabled 0` followed by `adb_enabled 0` closed the port; after the first
  `adb_wifi_enabled 0` the device went offline and 5555 still answered until `adb_enabled 0` was sent again.
- Window, transition and animator scales were already `0.0` on this firmware.
- Speed: `cmd package compile -m speed-profile -f <package>` cut JioHotstar's cold start from 2.24 s to 1.10 s
  (`am start -W`, one run each); YouTube, TiviMate and Prime Video did not change beyond noise. Warm starts were
  70 to 165 ms because the apps stayed cached after Home. `cmd package trim-caches` freed about 650 MB of storage.
- Power: `settings put system screen_off_timeout 180000` and `settings put secure sleep_timeout 600000` were
  accepted; TCL's own auto power-off keys (`tcl_app_power_off_time_delay`) were left alone, units unknown.
- `cmd dreams start-dreaming` fails with `Must be root to call Dream shell commands`, so the screensaver cannot be
  started from the shell for a test.
- The Ambient screensaver is `com.google.android.apps.tv.dreamx`; its Google Photos source needs a Google sign-in
  on the TV, done by hand.
