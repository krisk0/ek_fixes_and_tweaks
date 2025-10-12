# Elder Kings fixes and tweaks

This is a modular modification of Elder Kings modification of Crusader Kings game.

Includes changes to interface, gameplay, parameters; bug fixes. You decide whether to apply a change, for each and every file.

Primary location of this project is [on github](https://github.com/krisk0/ek_fixes_and_tweaks/). Visit it to see if a new version is available.

## Legal notice

As there is no license file attached to this modification, it is public domain.

I hate to write this, but some cites including [nexus](nexusmods.com) require that other people's code can only be published with special permission. **I hereby allow you to publish anywhere code created by me found in this repository**. Don't forget to mention me in comments, like `# vampire bite event by krisk0` or `# inspired by krisk0's court_policy_whatever_effect`.

## Technical requirements

* Game version 1.17.0.1.
* Elder Kings modification version 0.16.3.1.
* To handle increased birth-rate, a very good computer is required.

**This is not a joke. If you want many children, upgrade your computer first.**

## Language support

This mod does not add any user-visible strings. Thus, all languages all supported.

## Bugs

Send bugreports and suggestions via github. If you found the mod elsewhere, you better visit primary page [on github](https://github.com/krisk0/ek_fixes_and_tweaks/).

## Interface changes

* `window_character.gui` changes main character window so fertility is displayed next to age. As a bonus, some whitespace at end-of-line was removed. File path: `ek_fixes_and_tweaks/gui/window_character.gui`.

## Bug fixes

* File `ek_yearly_events.txt`, event `ek_yearly_events.0138` contains unreachable code that is supposed to heal from lovers pox: `remove_trait = lovers_pox`.
To really enable the cure, one line was added to precondition trigger. As a bonus, some whitespace at end-of-line was removed. File path: `ek_fixes_and_tweaks/events/ek_yearly_events.txt`.
* `ep1_achievements.txt` has erroneous and incorrectly formatted code that spams `error.log` with messages
```
  Error: Invalid right side during comparison 'previous_holder'
  Script location: file: common/achievements/ep1_achievements.txt line: 235 (ep1_18_chievement)
```
This is a bug in base game in event `ep1_18_achievement`, inherited by Elder Kings. I added an extra check that appear to fix the bug. As a bonus, I fixed formatting. File path: `common/achievements/ep1_achievements.txt`.

Two bugs described above are also seen in version 0.15.1. They survived two versions change: 0.15.1 → 0.16.2.1 → 0.16.3.1. EK team, is it not time to fix?
* `lifespan_traits_inheritance_effect` has two strange `NOT = { … }` blocks with two condifions inside:
```
NOT = { has_trait = lifespan_1 has_trait = lifespan_2 }
```
Two conditions in bracers and explicitly ANDed, therefore the line means "don't have both life1 and life2". Thus the line evaluates `yes` for all characters. Obviously code author meant something else.

There is another problem the subroutine: comment `3 + 3 = 3 or 2 or 4` is out-of-sync with code: life3+life3 results in either life3 or life4, not life2.

Thus the subroutine has multiple problems, so I reimplemented it, following comments like `3 + 3 = …`, and keeping chances found in random_list blocks. File name: `common/scripted_effects/lifespan_traits_inheritance.txt`. My code is a lot shorter (172+39 visus 719), because I pre-calculate two numbers in range 0…4 and only then give longevity gene.

## Gameplay changes

* `birth_events.txt` disables dialog that lets you change name of your distant relative at birth. Changing name of your son and daughter remains as it is. As a bonus, some whitespace at end-of-line was removed. File path: `ek_fixes_and_tweaks/events/birth_events.txt`.
* `00_wet_nurse_tasks.txt` allows wet nurse to educate characters educated by liege as other children. In EK mod, wet nurse will not teach virtues to children educated by liege. File path: `ek_fixes_and_tweaks/common/court_positions/tasks/00_wet_nurse_tasks.txt`.
* `cast_spell_if_max_mana.txt` improves AI spellcasting. It forces AI character to cast spell as soon as his mana pool is maximal. Note that AI spellcasting is awkward, see subsection *AI spellcasting problems* below. If you want AI to monthly check if mana is maximal and attempt to cast a spell, keep file `ek_fixes_and_tweaks/common/on_actions/cast_spell_if_max_mana.txt`.

### Motivation to change wet nurse behavior

Liege usually educates most important children, such as future rulers or councillors. Not giving them virtues is a great disadvantage. Besides, children are supposed to have close contacts with each other, so it is natural if all are indoctrinated with the same values.

### AI spellcasting problems

1. AI character only casts spells no more often than once per 3 years.
2. AI does not attempt to heal child or father or councillor. They only heal self, soulmate, lovers, spouses, friends.
3. Non-ruler AI never casts spells at all.

Problem no. 1 is partially solved by `cast_spell_if_max_mana.txt` (see description above). To solve problem no. 3, delete all lines `is_ruler = yes` from EK file `common/scripted_effects/ek_magic_ai_effects.txt`, then add line `is_ruler = yes` where appropriate (so non-ruler character does not attempt to summon an army of undead). This requires some work.

## Perfomance optimization

ELder Kings subroutine `add_magicka` is slower than it could be — it usually evaluates scripted value `magicka_max` twice. I created code that only evaluates the script once, is functionally equivalent, and also shorter (15 lines instead of 24 lines). If you want perfomance gain, keep file `ek_fixes_and_tweaks/common/scripted_effects/add_magicka.txt`.

## Parameter changes

* `ek_defines.txt` removes upper limit on children of land owners; allows non-landed people to have five children; increases birthrate of concubines. There are also changes to reproduction age, but they do nothing since game engine does not allow children to have sex or get pregnant. As a bonus, some whitespace at end-of-line was removed. File path: `ek_fixes_and_tweaks/common/defines/ek_defines.txt`.

If you do not play on a very good computer, then do not apply the birthrate changes. You will experience a great slow-down if your computer is not top tier.

## Installation

1. Unpack .zip.
2. Remove `readme.md` and files you do not want installed. For instance, if you do not want to greatly slow-down your game, remove `ek_fixes_and_tweaks/common/defines/ek_defines.txt`.
3. Copy all remaining files into `mod` directory, where you put `elder-kings-ck3.mod` file and `elder-kings-ck3` directory.
4. Activate via launcher called `dowser.exe`.

For more details on manual mod installation, see [wiki](https://ck3.paradoxwikis.com/Modding#Installing_mods_manually).

## Note to Elder Kings team

I attempted to file a bug-report at [reddit](https://www.reddit.com/r/ElderKings/). The post was immediately deleted, in a fraction of a second. Could not be human, this was definitely a robot.

I do not know why your robot killed my post. Was it 'bugreport', my forum alias, or my IP address. I do not know where to file bugreports, or exchange opinion, or suggest improvements. If you want me to cooperate, tell me how. File bugreport here at github.

## My mods

List of my modifications for CK3, and my load order is [here](https://gist.github.com/krisk0/3c51136a877afd606c184a575400922f).
