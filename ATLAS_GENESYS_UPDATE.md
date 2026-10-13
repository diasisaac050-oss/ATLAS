ATLAS — GENESYS UPDATE (device control + widget)

Added:
- BrightnessManager: raises/lowers screen brightness via Settings.System,
  requesting the WRITE_SETTINGS permission screen the first time it's needed.
- AtlasBluetoothManager: turns Bluetooth on directly when BLUETOOTH_CONNECT
  is granted; falls back to opening Bluetooth settings otherwise.
- RestartManager: best-effort "su -c reboot". Works only on rooted phones -
  stock Android gives no app a public way to reboot itself. On failure
  CommandManager reports this honestly instead of pretending it worked.
- New CommandParser rules: brightness up/down, "turn on bluetooth", "restart
  the phone" (pt/en/es/it variants).
- AtlasWidgetProvider: new 4x4 home screen widget. RemoteViews can't run an
  AnimationDrawable directly (it lives in the launcher process), so the
  animation is a self-rescheduling AlarmManager tick (every 1.2s) that swaps
  the core image between atlas_core_solid / atlas_core_glow / atlas_ring -
  a breathing pulse using drawables already in the project. Tapping the
  widget opens MainActivity. Alarm chain starts on first placement
  (onEnabled) and stops when the last instance is removed (onDisabled), so
  it costs nothing when no ATLAS widget is on a home screen.

Manifest / permissions:
- Added BLUETOOTH_ADMIN, BLUETOOTH_CONNECT, WRITE_SETTINGS.
- Registered .widget.AtlasWidgetProvider as a receiver with
  @xml/atlas_widget_info (4x4 grid, resizable, home_screen category).

Strings:
- New resp_brightness_*, resp_bluetooth_*, resp_restart_* and
  widget_status_active keys added to every locale (values, de, es, fr, it,
  pl, pt-rBR, pt-rPT).
- novidades_body (en + pt-rBR, the only locales that define it) got the new
  bullets appended - pack name unchanged, so nothing existing was removed.

Version:
- versionCode 4 -> 5, versionName 1.1.0 -> 1.2.0.

Note:
- Same build limitation as previous update packs: no Gradle wrapper in this
  environment, so a full assembleDebug could not be run here. Changes were
  checked at the XML/source level (brace balance, resource references,
  manifest registration) rather than compiled.
