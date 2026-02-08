These profiles have the filament change gcode already in place.

To use these profiles:

1. On your printer - DISPLAY_OFF and SAVE_ZMOD_DATA USE_TRASH_ON_PRINT=2.
   DO NOT ATTEMPT TO USE BAMBU STUDIO with the native screen enabled, or with USE_TRASH_ON_PRINT set to any value other than 2.
   
2. Install the Creality K1 0.4 profile (these use it as a base)

3. Copy these into your Bambu Studio config folder - they go in user/default/machine
     On Windows: %APPDATA%\BambuStudio\user\default\machine
     On Mac: ~/Library/Application Support/BambuStudio/user/default/machine
     On Linux: Depends on distro, how you install Bambu Studio, your config, etc. If you can use Linux, you can find it.
     
4. If you wish to customize nozzle_volume, do so by editing the JSON files. Bambu Studio does not allow you to edit this
   attribute in the menu.

5. Restart BambuStudio

WARNING:
  The 0.4 profile has had very limited testing.
  The other profiles have no testing whatsoever.
  Use at your own risk!
  If you want to be safe, don't use beta.
  If you want to be safe-ish, stick to OrcaSlicer.