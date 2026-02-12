FURTHER DEVELOPMENT ON THIS REPO WILL BE FOR PRINT PROFILES ONLY
FUTURE UPDATES WILL BE HERE AND INTEGRATED INTO ZMOD CODE: https://github.com/ninjamida/zmod_base/tree/nopoop-new


Nopoop-New
by Namida Verasche (aka ninjamida)

For discussion:
- https://github.com/ghzserg/zmod/discussions/443 (Nopoop improvements discussion on zMod github repo)
- https://www.facebook.com/groups/1526322885078508 (unofficial zMod Facebook group)

New features:
- New mode, activated by USE_TRASH_ON_PRINT = 2. This mode allows you to toggle nopoop on or off in the slicer.
  Additionally, when using poop in this mode, flush volumes are respected. (Purge to infill / etc do not work
  in poop mode due to limitations of OrcaSlicer. It is possible to use these options in poop mode by using
  Bambu Studio, but this is very experimental.)
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

zMod's dev has indicated he intends to eventually integrate this into zMod, but in the meantime, there are two files to
install on the printer.

"nopoop-new.cfg" should be copied into your mod_data folder. Then, you need to edit your user.cfg file, also in the mod_data
folder - if you do not have one, create it (you do not need to add anything else to it). Add the following line:
  [include nopoop-new.cfg]
  
"filament.json" should replace your existing filament.json file. However, if you have customized this and don't want
to replace it, the important things to change are (for ALL filaments):
 > filament_unload_before_cutting: Recommended value 20, don't go much higher than that. Might be safer to use a lower
                                   value, or even zero, for TPU. Higher values save filament.
 > nozzle_clean_length: Note that this parameter's name is very misleading; it actually determines how far the filament
                        is pulled back into the 4-in-1 tube after unloading. Anyway, set it to 70 if you have the 4-in-1
                        tube style without the screw, or 100 if you have the one with it (or aren't sure).
 > filament_unload_speed: Double your existing values. The reason for this is that zMod's existing code doubles the value
                          when retracting with the IFS alone; since this is the majority of unload, it makes more sense
                          for this speed to be natively specified (and other values based on it to be halved, instead of
                          this one doubled).
                        
Ideally, you can use the provided printer profiles. These cover all four options - native screen, zMod-controlled poop
(USE_TRASH_ON_PRINT=1), nopoop, and slicer-controlled poop; and also contain some custom start and filament change gcode
to verify settings and improve the behavior of the color change process. (Note that custom gcode is NOT required except
when using USE_TRASH_ON_PRINT=2.)
                        
If you have adjusted filament_unload_before_cutting in your filament.json, in the Printer settings in OrcaSlicer, adjust the
"Nozzle Volume"; this enables more accurate auto-calculated flushing volumes. Calculate this via the following formula:
  144 - (filament_unload_before_cutting * 2.405)
  
The provided profiles are close to the stock AD5X profiles, with the following differences:
- Nozzle volume set to 144 (correct value if filament_unload_before_cutting is zero, and a safe value if it isn't)
- zMod start GCode in place, with additions to check the correct native screen / use_trash_on_print settings are in place
- Filament change GCode to allow max acceleration during filament swaps, and set it back afterwards
- For slicer-controlled poop (USE_TRASH_ON_PRINT=2), the filament change GCode to control this poop is in place
- Z-Hop type set to Normal

There are many debatable changes that should or shouldn't be put in as defaults. I decided to avoid that debate and skip
most of these - but you can absolutely modify them with your own changes if you like. A good way to do this is open two
instances of OrcaSlicer at the same time, edit the new profile in one and your existing profile in the other, and look
for any fields that are highlighted to indicate they're not default values.

Regarding the check for use_trash_on_print settings, unless you disable it, these will automatically be changed to the
correct ones when starting a print from these profiles. Therefore, except for the native screen vs non-native screen, you
can simply select the right profile and the start gcode will take care of switching the use_trash_on_print mode.


--== USING BAMBU STUDIO ==--

!!! BAMBU STUDIO IS ONLY SUPPORTED WITH NATIVE SCREEN OFF AND USE_TRASH_ON_PRINT=2 !!!
(You can probably make it work with other configurations, but you're on your own for that. You are welcome to use
my profiles as a starting point. The start gcode will check for this configuration if you are using the provided
profiles.)

With this version of nopoop, you can use Bambu Studio instead of OrcaSlicer if you prefer. This is very experimental.
Bambu Studio is only supported with USE_TRASH_ON_PRINT = 2, and only supports poop mode. (However, you can effectively
achieve nopoop mode by providing sufficient spare objects for all purge to go to.) One upside is that Bambu Studio
allows you to poop with custom flush volumes while also respecting purge to infill / etc.

There is no preset in Bambu Studio for the AD5X. You can use the provided printer profiles (you will need to install
the Creality K1 0.4 profile first). If you wish to configure your own instead, you will need to base it off one of
the existing printers - I suggest using a non-Bambu one, as the Bambu ones have a lot of gcode we don't want - and
edit it to suit the AD5X. A copy of the start print and material change gcodes for Bambu Studio are provided - note
that they are different from the OrcaSlicer ones!

Note that for some reason, Bambu Studio does not let you edit the nozzle volume setting. You can edit it by opening
the printer's config json file and changing it there; you will need to restart Bambu Studio afterwards.


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
  poop the same amount regardless of these settings. Flush volumes WILL be respected, though! This is a limitation of
  OrcaSlicer, and can be bypassed by using Bambu Studio instead (however, Bambu Studio does not allow for any nopoop
  mode); should OrcaSlicer fix this in a future update, it should "just work" with poop mode without any further update
  to nopoop being necessary.
  
  It appears that it will be possible to use poop mode + purge to these other objects by using Bambu Studio instead of
  OrcaSlicer, but I am still investigating this option.
  

--== USAGE ==--

The easiest way to use this is to use the provided profiles (you can alter most of the settings, just leave the start gcode
and filament change gcode alone if you don't know what you're doing; *adding* to the start gcode is fine), and leave the
option to auto-change settings enabled. This way, aside from turning the native screen on or off as applicable for the
profile you're using, you can simply choose the appropriate profile for how you want to print, and let it "just work".

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

Flush volumes from the slicer with a 0.75 multiplier should be sufficient in most cases, if you've configured your
nozzle volume as described in the install instructions.

Trash skip height is best set to 1 for PETG, and 0.6 for any other material.


--== CONFIGURATION VARIABLES ==--

These can be set via SAVE_VARIABLES VARIABLE=[name] VALUE=[value]

nopoop_trash_skip_height            | When in nopoop mode, the printer will still go to the trash when the print is below
                                    | this height, during color changes. 0 to disable, >220 for always.
------------------------------------+---------------------------------------------------------------------------------------
use_trash_on_print                  | (Existing zMod variable). Existing behavior is maintained for values of 0 and 1; new
                                    | behavior is unlocked by setting this to 2.
------------------------------------+---------------------------------------------------------------------------------------
validate_print_settings_auto_change | Determines what will happen if _VALIDATE_PRINT_SETTINGS is called with parameters that
                                    | do not match the current printer configuration. (This macro is never called by zMod or
                                    | nopoop itself; it exists for the benefit of custom slicer gcode. The provided print
                                    | profiles do use it.)
                                    |  -1 : Emit a warning to the console, but continue with the print
                                    |   0 : Abort the print and show an error
                                    |   1 : Change the settings to the correct ones (cannot change display_on/off, will
                                    |       abort the print and show an error if that mismatches)                                    