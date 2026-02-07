Nopoop-New
by Namida Verasche (aka ninjamida / Trumble)

For discussion:
- https://github.com/ghzserg/zmod/discussions/443 (Nopoop improvements discussion on zMod github repo)
- https://www.facebook.com/groups/1526322885078508 (unofficial zMod Facebook group)

New features:
- New mode, activated by USE_TRASH_ON_PRINT = 2. This mode allows you to toggle nopoop on or off in the slicer.
  Additionally, when using poop in this mode, flush volumes are respected.
- Print head takes an "around the edges" route during color changes to avoid leakage getting on your prints.
- When changing colors during the first few layers in nopoop mode, there is an option to make the nozzle rest in
  the poop chute during the change, despite not producing any poop. This is not enabled by default.
- In nopoop mode, a slight "ironing" motion is performed before resuming after a color change, to help reduce the impact
  of blobs.

Fixes & Improvements:
- Less idle time during color changes.
- Length of old filament withdrawn during a color change ("nozzle_clean_length" in filament.json) more accurate. (Probably
  not quite 100%.)
- Chaning filament_drop_length in filament.json no longer breaks color change behavior.

--== INSTALL ==--

zMod's dev has indicated he intends to eventually integrate this into zMod, but in the meantime, there are three files
to install - two for the printer, one for the slicer.

"nopoop-new.cfg" should be copied into your mod_data folder. Then, you need to edit your user.cfg file, also in the mod_data
folder - if you do not have one, create it (you do not need to add anything else to it). Add the following line:
  [include nopoop-new.cfg]
  
"filament.json" should replace your existing filament.json file. However, if you have customized this and don't want
to replace it, the important things to change are (for ALL filaments):
 > filament_unload_before_cutting: Recommended value 20, don't go much higher than that. Might be safer to use a lower
                                   value, or even zero, for TPU. Higher values save filament.
 > nozzle_clean_length: Note that this parameter's name is very misleading; it actually determines how far the filament
                        is pulled back into the 4-in-1 tube after unloading. Anyway, set it to 90 if you have the 4-in-1
                        tube style without the screw, or 120 if you have the one with it (or aren't sure).

If you intend to continue using the existing USE_TRASH_ON_PRINT=0 behavior (or USE_TRASH_ON_PRINT=1, and you just want
the incidental improvements here), then you are finished installing - it's time to move on to printing!

If you intend to use the new USE_TRASH_ON_PRINT=2 behavior, you need to add some custom GCode into OrcaSlicer. In the
Printer settings, on the Machine G-Code tab, locate the "Change filament G-code" and copy-paste the entire contents of
"material_change_gcode.txt" into it. To improve the accuracy of the slicer's print time estimates, on the Multimaterial
tab, at the bottom, change both Filament load and unload time to zero, and set Tool change time to 55.

The custom G-Code for Bambu Studio is not yet tested. Use at your own risk.


--== MODES ==--

The mode of behavior for color changes is determined by the SAVE_ZMOD_DATA parameter USE_TRASH_ON_PRINT. To configure
this:
  SAVE_ZMOD_DATA USE_TRASH_ON_PRINT=x
  
When this is set to 0:
  The printer will not poop during color changes. After cutting the filament, the printer will return to the same location
  it was in before the color change, switch the filament while sitting there, and immediately continue with the print.
  (Generally, this will be on the wipe tower, unless the current print doesn't have one.) Or in other words - the printer
  skips pooping altogether. On the first few layers, the printer will still travel to the poop chute during changes, but
  will not produce any poop - this is just to catch leakage and reduce the risk of it forming blobs on the build plate.
  
When this is set to 1:
  During color changes, the printer will produce two poops, of length specified by filament_drop_length in filament.json
  (plus filament_drop_length_add if the two filaments are different materials rather than just different colors). These
  poops behave similar to the poops on stock AD5X firmware - ie: they cannot be controlled by the slicer, nor are they
  included in its time or material estimates. This is the same as zMod's behavior without nopoop.
  
When this is set to 2:
  Color change behavior can be chosen in the slicer between nopoop mode (ie: same as mode 0), or yes-poop mode. When poop
  is enabled in this mode, the poop is controlled by the slicer; as such, it will be included in estimates and will respect
  your flush volumes configured in the slicer.
  
  Please note, you can only meaningfully use "Purge to object", "Purge to infill", etc when in nopoop mode. This is a
  restriction of the slicer - although it will alter the order in which it performs actions after the color change, it will
  poop the same amount regardless of these settings. Flush volumes WILL be respected, though!
  
  It appears that it will be possible to use poop mode + purge to these other objects by using Bambu Studio instead of
  OrcaSlicer, but I am still investigating this option.
  

--== USAGE ==--

If you are already using mode 0 or mode 1, and intend to continue using that mode, you do not need to do anything different.
Enjoy the small improvements. If you are not an existing user of nopoop, but intend to use mode 1, you also do not need to
do anything different from what you already are (provided that what you're already doing works with zMod in general).

If you are using a nopoop behavior - whether it's mode 0, or mode 2 with poop disabled - you must enable the "Purge in prime
tower" option in OrcaSlicer. This is found under the printer settings on the multimaterial tab. You can additionally enable
purge to infill, purge to support, purge to an object, etc if you like, when using mode 0 or mode 2.

In the case of mode 0, failing to enable this option will likely result in insufficient purge, and thus color bleed. For
mode 2, nopoop is directly tied to this option - if this option is off, you will get poop; if it's on, you get a nopoop
print (and should have identical behavior to mode 0).

When printing without poop (either Mode 0, or Mode 2 with Purge to prime tower enabled), you can optionally have the print
head move to the trash chute during color changes. No poop will be produced, but any leakage will be sent down the chute
instead of going into the wipe tower. It is possible to configure this to happen only up to a certain height on the print.

To enable this:
  SAVE_VARIABLE VARIABLE=nopoop_trash_skip_height VALUE=5
  
This will use the trash chute until the print height reaches 5mm. Beyond that point, color changes will happen at the
wipe tower. If you want to enable this for the entire print, set the skip height to 220 or higher; if you want to disable
it altogether, set the skip height to 0.


--== RECOMMENDATIONS ==--

Poop mode (Mode 1, or Mode 2 with poop enabled) is recommended for the 0.25 nozzle and for PETG with any nozzle.

Nopoop mode (Mode 0, or Mode 2 with poop disabled) works well with PLA and ABS on a 0.4 or larger nozzle.

When using poop mode, the flush volumes from the slicer with a 0.7 multiplier are generally adequate.

When using nopoop mode, 25 should be added to these values, especially those that are <= 125.

Trash skip height is best set to 1 for PETG, and 0.6 for any other material.