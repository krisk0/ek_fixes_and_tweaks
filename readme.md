# Elder Kings Fixes and Tweaks

This is a modular modification of Elder Kings modification of Crusader Kings game.

Includes changes to interface, gameplay, parameters; bug fixes. You decide whether to apply a change, for each and every file.

Primary location of this project is [on github](https://github.com/krisk0/ek_fixes_and_tweaks/). Visit it to see if a new version is available.

## Technical requirements

* Game version 1.19.0.6.
* Elder Kings modification version 0.19.0.1.
* To handle increased birth-rate, a very good computer is required.

**This is not a joke. If you want many children, upgrade your computer first.**

As of spring 2026, AMD 9800X3D is the only off-the-shelf CPU for CK3 game with vast amount of characters and scripts.

If you are playing game version 1.18.*, use [ek_fixes_and_tweaks](https://github.com/krisk0/ek_fixes_and_tweaks/) tagged 1.18.4, instead of this version.

## Language support

Only English is supported. However, there are only 5 lines to translate, should you wish to do so.

## Bugs

Send bugreports and suggestions via github. If you found the mod elsewhere, you better visit primary page [on github](https://github.com/krisk0/ek_fixes_and_tweaks/).

## Interface changes

* `window_character.gui` changes main character window so fertility is displayed next to age. Also hovering over age shows human age equivalent along with birtday. As a bonus, some whitespace at end-of-line was removed. File path: `gui/window_character.gui`.
* `00_knight_culture.txt` fixes knight names so they are always called knignts, not pelin or jaqspur. File path: `common/customizable_localization/00_knight_culture.txt`.

## Bug fixes

* File `ek_yearly_events.txt`, event `ek_yearly_events.0138` contains unreachable code that is supposed to heal from lovers pox: `remove_trait = lovers_pox`.
To really enable the cure, one line was added to precondition trigger. As a bonus, some whitespace at end-of-line was removed. File path: `events/ek_yearly_events.txt`.
* `lifespan_traits_inheritance_effect` has strange `NOT = { … }` blocks with two condifions inside:
```
NOT = { has_trait = lifespan_1 has_trait = lifespan_2 }
```
Two conditions in bracers and explicitly ANDed, therefore the expression in bracers means "don't have both life1 and life2 span". Thus the line evaluates `yes` for all characters. This is either a bug or obfucscation, most probably bug.

The subroutine has other problem: comment `3 + 3 = 3 or 2 or 4` mismatches code. Code analysis reveals that it should have been `3 + 3 = 3 or 4`.

I reimplemented the subroutine, following comments like `3 + 3 = …`, and mostly keeping chances found in random_list blocks. File path: `common/scripted_effects/lifespan_traits_inheritance.txt` and `common/script_values/lifespan_traits_inheritance_effect.txt`. My code is a lot shorter (172+39 visus 719) and clearer, because I pre-calculate two numbers in range 0…4 representing gene level and only then produce result.
* Success chance for convert to vampire scheme is miscalculated. It often discriminates religiously inclined characters. Suppose for instance, that Harkon is converting his zealous courtier, both believe that vampirism is a blessing. Success chance will get down, the more godly the courtier is, and -50 malus will be applied for zealous trait, completely ignoring faith tenets. My code respects religious beliefs and gives bonuses when appropriate. File path: `common/schemes/scheme_types/ek_convert_to_vampire_scheme.txt` and `localization/`.
* I experienced multiple errors related to file `events/pregnancy_events.txt`. `error.log` contained complaints that `scope:father` was unavailable in event `pregnancy.2002`. I fixed this error by adding a few checks. File path: `events/pregnancy_events.txt`.

## Gameplay changes

* `00_wet_nurse_tasks.txt` allows wet nurse to educate characters educated by liege as other children. In EK mod, wet nurse will not teach virtues to children educated by liege. File path: `common/court_positions/tasks/00_wet_nurse_tasks.txt`.
* The file also changes the logic of instilling virtue, making it close to what it was in version 0.18.0.2 of EK mod. In EK version 0.19.0.1, only one little courtier can get a virtue per month; in 0.18.0.2, each little one could. The file `common/court_positions/tasks/00_wet_nurse_tasks.txt` makes it so multiple children potentially update their traits in single month.
* `cast_spell_if_max_mana.txt` improves AI spellcasting. It forces AI character to cast a spell as soon as their mana pool is maximal. Note that AI spellcasting is awkward, see subsection *AI spellcasting problems* below. If you want AI to monthly check if mana is maximal and attempt to cast a spell, keep file `common/on_actions/cast_spell_if_max_mana.txt` and `events/ekfix_cast.txt`.
* Epidemic would not start at all if you are playing tutorial. Epidemic would not start in your lands if global variable `start_epidemic_grace` is set, which is unfair. If you agree with me that player land protection from epidemic is unfair, and do not mind epidemic during tutorial, remove all blocks of code from file `common/script_values/06_ce1_epidemics_values.txt` from installed EK mod that look like:
```
	if = {
		limit = {
			OR = {
				has_global_variable = is_in_tutorial
        …
    }
```
* After failed vampire conversion, next attempt can only happen in 20 years. If you feel it is too long, replace lines `days = 7300` with something like `days = 1825` in file `events/ek_transformation.txt`. I suggest that you issue `sed` command: `sed -i -e 's:days = 7300:days = 1825:' events/ek_transformation.txt`.

### Motivation to change wet nurse/virtues mechanic

Liege usually educates most important children, such as future rulers or councillors. Not giving them virtues is a great disadvantage. Besides, children are supposed to have close contacts with each other, so it is natural if all are indoctrinated with the same values.

If your wives, lovers and courties made many children, then they all should be educated and taught virtues. Having lots of children at court should not decrease chance of proper education. More than one child should be able to acquire a good trait every month, like it was in version 0.18.0.2.

### AI spellcasting problems

1. AI character only casts spells no more often than once per 3 years.
2. AI does not attempt to heal child or father or councillor. They only heal self, soulmate, lovers, spouses, friends.
3. AI do not prioritize healing, and often refuse to heal a close person. If for instance an AI character Alice has a healthy soulmate and terribly wounded friend called Bob, there is no chance for Bob to be healed by Alice.
4. When selecting hostile spell target, only far enough enemies can be hit — those outside spell_range. Spell range is larger for better-educated characters. So better-educated characters have less enemy targets than poorly-educated, or none at all. I think distance condition should be reverted to "near enough" (inside spell_range).

Problem no. 1 is partially solved by `common/on_actions/cast_spell_if_max_mana.txt`.

To solve problem no.3, change `value > "root.spell_range"` to `value < "root.spell_range"` in EK file `common/scripted_effects/ek_magic_ai_effects.txt`.

To solve problem no.4, major changes are required.

## Performance optimization

1. ELder Kings subroutine `add_magicka` is slower than it should be — it usually evaluates scripted value `magicka_max` twice. I created code that only evaluates the script once, is functionally equivalent, and also shorter (15 lines instead of 24 lines). If you want performance gain, keep file `common/scripted_effects/zz_add_magicka.txt`.
2. ELder Kings scripted value `ek_human_age_equivalent` is slower than it should be — it usually evaluates scripted value `ek_human_age_equivalent_calc` twice. My code is free from the defect, functionally equivalent and shorter. If you want performance gain, keep file `common/script_values/ek_human_age_equivalent.txt`.

## Unsolved problems

* Strange condition `distance > spell range` (see subsection *AI spellcasting problems* above).

* Decision `Convert Cathedral of the Golden Path` was probably broken in game version 0.18.0.2. I do not know if it was fixed in a later version.

When I played version 0.18.0.2, the decision did not do anything good for me. At least I did not notice any useful effect and was able to run this decision again and again burning 500 mana each time.

I suggest that you be careful with the decision. Perhaps save game before taking it.

## Parameter changes

* `ek_fixes_and_tweaks_defines.txt` removes upper limit on children of land owners; allows non-landed people to have five children; increases impregnation probability. File path: `common/defines/ek_fixes_and_tweaks_defines.txt`.

If you do not play on a very good computer, or do not want to significantly increase population, then do not apply the birthrate changes. You will experience a great slow-down if your computer is not top tier, late game might be unplayable. To handle increased population, your CPU should have a good single-tasking performance and good caches.

## Installation

I suggest that you install at least bug fixes and optimization. I think most users should install all files except parameter changes `common/defines/ek_fixes_and_tweaks_defines.txt`.

1. Unpack .zip.
2. Remove `readme.md`; remove or tweak to your liking files you do not want installed. For instance, if you need fancy knight names like jaqspur, delete `common/customizable_localization/00_knight_culture.txt`; if you do not want to greatly slow-down your game, remove or tweak `common/defines/ek_fixes_and_tweaks_defines.txt`.
3. Copy all remaining files into `mod` directory, where you put `elder-kings-ck3.mod` file.
4. Activate via launcher called `dowser.exe`.
5. Tweak `06_ce1_epidemics_values.txt` and `ek_transformation.txt` (installed by EK mod) to your taste — see subsection *Gameplay changes* for details.
6. If you want to keep regular names of objects, for instance you prefer duchy to be always called duchy and not petty kingdom or something else, delete `common/flavorization` directory of EK mod.

## Note to Elder Kings team

I attempted to file a bug-report at [reddit](https://www.reddit.com/r/ElderKings/). The post was immediately deleted, in a fraction of a second. Could not be human, this was definitely a robot.

I do not know why your robot killed my post. Was it 'bugreport', my forum alias, or my IP address. I do not know where to file bugreports, or exchange opinion, or suggest improvements. If you want me to cooperate, tell me how. File bugreport here at github.

## My mods

List of my modifications for CK3, and my load order is [here](https://gist.github.com/krisk0/3c51136a877afd606c184a575400922f).
