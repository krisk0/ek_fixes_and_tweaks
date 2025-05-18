# Elder Kings fixes and tweaks

This is a modular modification of Elder Kings modification of Crusader Kings game.

Includes changes to interface, gameplay, parameters; bug fixes. You decide whether to apply a change, for each and every file.

Primary location of this project is [on github](https://github.com/krisk0/ek_fixes_and_tweaks/). Visit it to see if a new version is available.

## Technical requirements

* Crusader Kings version Crown 1.15.0.2.

* Elder Kings modification version 0.15.1.

* To handle increased birth-rate, a very good computer is required.

**This is not a joke. If you want many children, upgrade your computer first.**

## Language support

This mod does not add any user-visible strings. Thus, all languages all supported.

## Bugs

Send bugreports and suggestions via github. If you found the mod elsewhere, you better visit primary page [on github](https://github.com/krisk0/ek_fixes_and_tweaks/).

## Interface changes

* `window_character.gui` changes main character window so fertility is displayed close to age. As a bonus, some whitespace at end-of-line was removed. File path: `ek_fixes_and_tweaks/gui/window_character.gui`.

## Bug fixes

* `ek_yearly_events.txt` contains unreachable code that is supposed to heal from lovers pox. Probably the person creating the code desired to enable love pox healing in `ek_yearly_events.0138`. To really enable the cure, one line was added to precondition trigger. As a bonus, some whitespace at end-of-line was removed. File path: `ek_fixes_and_tweaks/events/ek_yearly_events.txt`.

* `ek_travel_events.txt` has reference to non-existing value `minor_stress_impact_gloss`. Changed to `minor_stress_impact_loss`. As a bonus, some whitespace at end-of-line was removed. File path: `ek_fixes_and_tweaks/events/travel_events/ek_travel_events.txt`.

## Gameplay changes

* `birth_events.txt` disables dialog that lets you change name of your distant relative at birth. Changing name of your son and daughter remains as it is. As a bonus, some whitespace at end-of-line was removed. File path: `ek_fixes_and_tweaks/events/birth_events.txt`.

* `00_wet_nurse_tasks.txt` allows wet nurse to educate characters educated by liege as other children. In EK mod, wet nurse will not teach virtues to children educated by liege. File path: `ek_fixes_and_tweaks/common/court_positions/tasks/00_wet_nurse_tasks.txt`.

### Motivation to change wet nurse behavior

Liege usually educates most important children, such as future rulers or councillors. Not giving them virtues is a great disadvantage. Besides, childrren are supposed to have close contacts with each other, so it is natural if all are indoctrinated with the same values.

## Parameter changes

* `ek_defines.txt` removes upper limit on children of land owners, and allows non-landed people to have five children. There are also changes to reproduction age, but they do nothing since game engine does not allow children to have sex or get pregnant. As a bonus, some whitespace at end-of-line was removed. File path: `ek_fixes_and_tweaks/common/defines/ek_defines.txt`.

If you do not play on a very good computer, then do not apply the birthrate changes. You will experience a great slow-down if your computer is not top tier.

## Installation

1. Unpack.

2. Remove `readme.md` and files you do not want installed. For instance, if you do not want to greatly slow-down your game, remove `ek_fixes_and_tweaks/common/defines/ek_defines.txt`.

3. Copy all remaining files into `mods` directory, where you put `elder-kings-ck3.mod` file and `elder-kings-ck3` directory.

4. Activate via launcher called `dowser.exe`.

For more details on manual mod installation, see [wiki](https://ck3.paradoxwikis.com/Modding#Installing_mods_manually).

## Note to Elder Kings team

I attempted to file a bug-report at [reddit](https://www.reddit.com/r/ElderKings/). The post was immediately deleted, in a fraction of a second. This could not be human, this was definitely a robot.

I do not know why your robot killed my post. Was it 'bugreport', my forum alias, or my IP address. I do not know where to file bugreports, or exchange opinion, or suggest improvements. If you want me to cooperate, tell me how. File bugreport here at github.
