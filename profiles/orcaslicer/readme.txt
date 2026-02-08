These profiles have the filament change gcode already in place.

To use these profiles:

1. On your printer - DISPLAY_OFF and SAVE_ZMOD_DATA USE_TRASH_ON_PRINT=2.
   DO NOT ATTEMPT TO USE THESE PROFILES with the native screen enabled, or with USE_TRASH_ON_PRINT set to any value other than 2.
   For USE_TRASH_ON_PRINT = 0, use your standard profile but turn "Purge in prime tower" on.
   
2. Install the stock AD5X profiles for the nozzle size(s) you intend to install

3. Copy these into your OrcaSlicer config folder - they go in user/default/machine
     On Windows: %APPDATA%\OrcaSlicer\user\default\machine
     On Mac: ~/Library/Application Support/OrcaSlicer/user/default/machine
     On Linux: Depends on distro, how you install Bambu Studio, your config, etc. If you can use Linux, you can find it.
     
4. Restart OrcaSlicer.

5. Optionally enable "Purge in prime tower" (Printer settings -> Multimaterial tab) for nopoop mode.

WARNING:
  The 0.8 profile has had no testing whatsoever, and the 0.25 and 0.6 have not been tested as extensively as the 0.4.
  And again, these profiles should only be used with USE_TRASH_ON_PRINT set to 2 and the native screen off.