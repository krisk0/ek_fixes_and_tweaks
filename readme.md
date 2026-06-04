# Elder Kings fixes and tweaks

This is a modular modification of Elder Kings modification of Crusader Kings game.

Includes changes to interface, gameplay, parameters; bug fixes. You decide whether to apply a change, for each and every file.

Primary location of this project is [on github](https://github.com/krisk0/ek_fixes_and_tweaks/). Visit it to see if a new version is available.

## Technical requirements

* Game version 1.18.* (I only tested 1.18.4).
* Elder Kings modification version 0.18.0.2.
* To handle increased birth-rate, a very good computer is required.

**This is not a joke. If you want many children, upgrade your computer first.**

If you are still playing an older version of EK2, the [repository](https://github.com/krisk0/ek_fixes_and_tweaks/) might contain a fix. To get it, go back in git history. Sorry for inconvenience.

## Language support

If you install all files, only English is supported. If you however do not install my changes of convert-to-vampire scheme, all languages are support.

## Bugs

Send bugreports and suggestions via github. If you found the mod elsewhere, you better visit primary page [on github](https://github.com/krisk0/ek_fixes_and_tweaks/).

## Interface changes

* `window_character.gui` changes main character window so fertility is displayed next to age. As a bonus, some whitespace at end-of-line was removed. File path: `gui/window_character.gui`.

## Bug fixes

* File `ek_yearly_events.txt`, event `ek_yearly_events.0138` contains unreachable code that is supposed to heal from lovers pox: `remove_trait = lovers_pox`.
To really enable the cure, one line was added to precondition trigger. As a bonus, some whitespace at end-of-line was removed. File path: `events/ek_yearly_events.txt`.
* `lifespan_traits_inheritance_effect` has two strange `NOT = { … }` blocks with two condifions inside:
```
NOT = { has_trait = lifespan_1 has_trait = lifespan_2 }
```
Two conditions in bracers and explicitly ANDed, therefore the line means "don't have both life1 and life2". Thus the line evaluates `yes` for all characters. This is either a bug or obfucscation, most probably bug.

The subroutine has other problem: comment `3 + 3 = 3 or 2 or 4` mismatches code. Code analysis reveals that it should have been `3 + 3 = 3 or 4`.

I reimplemented the subroutine, following comments like `3 + 3 = …`, and mostly keeping chances found in random_list blocks. File path: `common/scripted_effects/lifespan_traits_inheritance.txt` and `common/script_values/lifespan_traits_inheritance_effect.txt`. My code is a lot shorter (172+39 visus 719) and clearer, because I pre-calculate two numbers in range 0…4 representing gene level and only then produce result.
* Success chance for convert to vampire scheme is miscalculated. It always discriminates religiously inclined characters. Suppose for instance, that Harkon is converting his zealous courtier, both believe that vampirism is a blessing. Success chance will get down, the more godly the courtier is, and -50 malus will be applied for zealous trait, completely ignoring faith tenets. File path: `common/schemes/scheme_types/ek_convert_to_vampire_scheme.txt` and `localization/`.

## Gameplay changes

* `00_wet_nurse_tasks.txt` allows wet nurse to educate characters educated by liege as other children. In EK mod, wet nurse will not teach virtues to children educated by liege. File path: `common/court_positions/tasks/00_wet_nurse_tasks.txt`.
* `cast_spell_if_max_mana.txt` improves AI spellcasting. It forces AI character to cast a spell as soon as their mana pool is maximal. Note that AI spellcasting is awkward, see subsection *AI spellcasting problems* below. If you want AI to monthly check if mana is maximal and attempt to cast a spell, keep file `common/on_actions/cast_spell_if_max_mana.txt`.
* Epidemic would not start at all if you are playing tutorial (which is impossible because tutorial is not implemented in EK as far as I know). Epidemic will not start in your lands if global variable `start_epidemic_grace` is set (which is unfair). If you agree with me that player land protection from epidemic is unfair, remove all blocks of code from file `common/script_values/06_ce1_epidemics_values.txt` from installed EK mod that look like:
```
	if = {
		limit = {
			OR = {
				has_global_variable = is_in_tutorial
        …
    }
```
* After failed vampire conversion, next attempt can only happen in 20 years. If you feel it is too long, replace lines `days = 7300` with something like `days = 1825` in file `events/ek_transformation.txt`.

### Motivation to change wet nurse behavior

Liege usually educates most important children, such as future rulers or councillors. Not giving them virtues is a great disadvantage. Besides, children are supposed to have close contacts with each other, so it is natural if all are indoctrinated with the same values.

### AI spellcasting problems

1. AI character only casts spells no more often than once per 3 years.
2. AI does not attempt to heal child or father or councillor. They only heal self, soulmate, lovers, spouses, friends.
3. When selecting hostile spell target, only far enough enemies can be hit — those outside spell_range. Spell range is larger for better-educated characters. So better-educated characters have less enemy targets than poorly-educated (or none at all). I think distance condition should be reverted to "near enough" (inside spell_range).
4. AI do not prioritize healing. If for instance an AI character has a healthy soulmate and terribly wounded friend, there is no chance to heal friend. Spell choice is completely random.

Problem no. 1 is partially solved by `common/on_actions/cast_spell_if_max_mana.txt` (see description above).

To solve problem no.3, change `value > "root.spell_range"` to `value < "root.spell_range"` in EK file `common/scripted_effects/ek_magic_ai_effects.txt`.

To solve problem no.4, major changes are required.

## Performance optimization

ELder Kings subroutine `add_magicka` is slower than it could be — it usually evaluates scripted value `magicka_max` twice. I created code that only evaluates the script once, is functionally equivalent, and also shorter (15 lines instead of 24 lines). If you want performance gain, keep file `common/scripted_effects/add_magicka.txt`.

## Unsolved problems

* Strange condition `distance > spell range` (see previous section).

* Decision `Convert Cathedral of the Golden Path` is probably broken.

The decision probably does nothing and only eats mana. At least I did not notice any good effect and was able to run this decision again and again burning 500 mana each time.

I do not know how to fix this. I suggest that you do not use the decision.

## Parameter changes

* remove upper limit on children of land owners; allows non-landed people to have five children; increases impregnation probability. File path: `common/defines/ek_fixes_and_tweaks_defines.txt`.

If you do not play on a very good computer, then do not apply the birthrate changes. You will experience a great slow-down if your computer is not top tier.

## Installation

1. Unpack .zip.
2. Remove `readme.md` and files you do not want installed. For instance, if you do not want to greatly slow-down your game, remove or modify `ek_fixes_and_tweaks/common/defines/ek_fixes_and_tweaks_defines.txt`.
3. Copy all remaining files into `mod` directory, where you put `elder-kings-ck3.mod` file.
4. Activate via launcher called `dowser.exe`.

For more details on mod installation, see [wiki](https://ck3.paradoxwikis.com/Modding#Installing_mods_manually).

## Note to Elder Kings team

I attempted to file a bug-report at [reddit](https://www.reddit.com/r/ElderKings/). The post was immediately deleted, in a fraction of a second. Could not be human, this was definitely a robot.

I do not know why your robot killed my post. Was it 'bugreport', my forum alias, or my IP address. I do not know where to file bugreports, or exchange opinion, or suggest improvements. If you want me to cooperate, tell me how. File bugreport here at github.

## My mods

List of my modifications for CK3, and my load order is [here](https://gist.github.com/krisk0/3c51136a877afd606c184a575400922f).
