# IV-Evo firmware source audit

Reviewed repository: https://github.com/Sgw32/iv-evo-fw

Reviewed commit: `3260783532b3d4ad8529b23f4e907c0a6ab03262`.

The six translated `Firmware.tex` chapters describe this revision. Installed releases can differ. The original HTML remains unchanged for traceability. Firmware source takes precedence for revision-specific software behavior; the HTML hardware safety and USB/vehicle-power prohibition remain in force. No hardware testing, firmware fixes or electrical-rating certification is implied.

| Manual detail | Evidence at the reviewed commit |
| --- | --- |
| Hold button at boot; ~10-second long press restarts; setup branch vs BLE; periodic Wi-Fi shutdown | `main/main.c`: `config_button_task`, `app_main` |
| Default AP name `IVEVO_AP`; provisioning route `/wifi` | `sdkconfig`; `components/nvs_wifi_connect/` |
| 80×160 logical display, x=0..79/y=0..159 | `main/hw_setup.h`; `main/display/st7735s.c`: `lcdInit` |
| `fillrect` arguments are inclusive corners, despite binding-local width/height names | `main/display/display_binding.c`: `lua_fillrect`; `main/display/st7735s.c`: `lcdDrawFillRect` |
| `display` exports colors, not WIDTH/HEIGHT | `main/display/display_binding.c`: `luapush_display_constant` |
| Lua integer enable flags and ADC index dispatch | `main/system/system_binding.c` |
| ADC result is volts scaled by 6.32; stored voltage/global coefficients are not applied here | `main/system/hal.c`: `read_adc_ch`; `main/hw_setup.h`: `VOLTAGE_DEVIDER` |
| `config_sleep` monitor flag exists, actual `go_sleep()` call is commented out | `main/system/system_binding.c`; `main/system/hal.c`: `GotoSleep_task` |
| Defaults: backlight 95, wifi_c_en/show_qr/ftp_en 0, NTC beta 3950 and nominal R 1000 | `main/system/hal.h`; `main/system/hal.c`: `update_hal_from_nvs` |
| App/sensor saved paths, reload, file manager, clock, debug sections | `components/frontend/src/main.js`, `index.html`; `components/backend/backend.c` |
| Web updates accept tar manifest; browser update destination is fixed to 192.168.4.1 | `components/frontend/src/main.js`: `uploadBinary`; `create_update.py` |
| Whole-storage replacement vs individual file uploads; OTA partition sizes | `components/ota/ota.c`; `components/backend/backend.c`; `partitions.csv` |
| NVS string values in `app`; `apppath` already has trailing slash | `main/nvs/nvs_binding.c`; `main/interpretator.c`: `load_lua_app` |
| DS18B20 zero-based indices and combined voltage/temperature indices | `main/onewire/onewire_hal.c`; `flash_data/sensor/t_ds18b20.lua`, `t_v_ds18b20.lua` |
| BLE peer name DigifizBLE | `main/ble/ble_client.c` |
| Current app directory names; no-reverse gear variant | `flash_data/app/volt_evo_v_l/`, `volt_iv_evo_t/`, `gear_iv_evo_norev/`, `iv_check/` |
| NTC settings read on script load; actual script uses ADC 0 plus ADC 1 reference | `flash_data/sensor/t_ntc.lua` |
| Oil script maps 0.5–2.09 V to 0–4 bar with clamps | `flash_data/sensor/oil_bar.lua` |
| Boost script uses 1.8–4.5 V to 1.013–2.5 bar; low-pressure comment disagrees | `flash_data/sensor/boost.lua` |
| Check Engine implementation uses corrected sensor.get(1) > 4 V | `flash_data/app/iv_check/app.lua` |
| volts1.lua calls a missing system.V1_pullup binding | `flash_data/sensor/volts1.lua` vs `main/system/system_binding.c` |

## Changes to examples

The prior HTML-derived sample treated `fillrect` as x/y/width/height and the first adaptation assumed a landscape coordinate space. The revised sample uses inclusive corner coordinates in the firmware's portrait space, a 16-pixel font present at `flash_data/app/fonts/H16XB.FNT`, a nil-font assertion, and a bar clamped to 60 pixels. The sample does not use nonexistent `display.WIDTH/HEIGHT` constants. It relies on an appropriate working sensor script; documenting a shipped script defect does not fix that firmware defect.

## Limits

The repository README is useful context, but implementation and binding tables were checked directly. Default configuration values are not promises that every UI setting affects every app. The oil and boost conversions are script-specific calibration choices, not proof of a universal sensor transfer curve or safe electrical range. The fallback application's presence does not establish recovery from every error. The manuals preserve the older HTML application names and installation examples as historical reference, with explicit precedence notes pointing to the firmware chapter.
