# SubtleD's Useful Functions

Some useful functions by SubtleDoctor

## Overview

This is a collectoin of five (so far) high-level functions that might be useful to other people, and can improve cross-mod compatibility. The functions themselves are inside the /lib folder. This also includes a little mod with components demonstrating example uses for each function. 

The examples apply various bonuses to the Skald bard kit. If you think Skalds are a bit lackluster, you can totally use this as a little mods to improve the kit. But really the point is to give example usage of these functions for other, new uses by other modders.
 
##Features

1: Casting Arcane Spells In Armor

This changes all armors in the game to disable spellcasting by applying .EFF files instead of carrying equipping effects in the .ITM files. The .EFF files can be blocked by opcode 206 and 318/324, which means you can selectively allow anyone to escape having their spellcasting disabled. 

The .EFF files are split in three and applied based on whether the armor has an in-game equipping appearance of leather armor, chain mail, or plate armor. So an application that filters by class can allow all bards to cast spells in leather armor; and an application filtering by kit can allow Skalds in particular to cast spells in leather or chain armor. It can also be applied on an individual basis, on a temporary basis, subject to a spellstate, or what have you. The 'armor blocks spellcasting' mechanic becomes very flexible.

Additionally, this function gets out of its own way - it can be applied in myriad different ways by various different mods, and all of them will work as intended. The armor system changes in Tweaks Anthology, Item Revisions, and Scales of Balance are already compatible with the changes made by this function. Recently some mods have created similar-but-distinct systems to allow spellcasting in armor, and it is a recipe for compatibility conflicts. By settling on a standard function for this, that can be avoided.

The example here allows Skalds to cast spells in chain mail armors. As you can see in the example .tpa file, this only involves 5 lines of Weidu to achieve, so bard/wizard/sorcerer kits (or particular NPCs etc.) can use this quite easily.

2: Modify HLA Tables

This provides a simple method to modify which HLAs are available to a particular kit. The design is intended to maximize cross-mod compatibility, similar to the armored casting function above. This was inspired by the difficulty in using several mods that changed HLA tables, in particular Rogue Rebalancing and Refinements, which required very particular install-order handling. With these functions, any number of mods can modify the HLA tables of the same kits in any number of ways, and they will all work as intended.

The functions are:
 - action_add_hla
 - action_remove_hla
 - action_replace_hla
 
The variables for the action_add_hla function are named for the terms in the header row of an HLA table like LUFI0.2da. The action_remove_hla function only has a single variable, 'remove_ability' 

Because in the vanilla game many kits share HLA tables, this function first copies the table to a new .2da files named for the kit's index number in kitlist.2da. The the new table is modified per the supplied variables. Subsequent applications of the functions will find and use the new table, so a later mod using these functions will work fine. 

The example here gives the Skald kit access to the War Cry HLA, and replaces access to the Spike Trap with access to the Deathblow HLA.

3: Weapon Proficiency Dialogues

This function creates an innate ability that triggers a dialogue which allows a number of weapon proficiencies to be chosen and added to a characters total. The dialogue will abide by all restrictions for class and kit in WEAPPROF.2da, PROFS.2da, and PROFSMAX.2da, at the time the function is applied.

The 'prof_script_name' variable is to allow the creation of distinct instances of the dialogue by different mods; the 'class_dlgs' variable allows each instance to be limited to a particular class, just to reduce the amount of needed files; it can be set to 'all' or simply omitted to make the dialogue work for all classes. 

The 'script_level_one' and 'script_higher_levels' variables are for more exotic use, replicating the number of proficiency pips that are gained at level 1, and after level 1, respectively. NPC_EE removes all weapon proficiencies and uses these variables to all them all to be re-selected. In general use of this function to simply grant bonus proficiencies, omit these variables or set them to 0. 

The example here gives the Skald abilities to spend one extra proficiency point at level 1, and another at level 5.

4: IWD-style 'Effect Evasion'

This function replicates the IWD thief ability to roll an extra saving throw vs. Breath Weapon to entirely avoid the effects of certain spells like Fireball. However, this function is generalized and can be applied in a more more flexible fashion. 

The chance to avoid spell effects is applied by adding several op177 and op318 effects directly into spells and items. So instead of the hard-coded set of spells that IWD thieves can evade, you can apply this to a curated list of spells of your choosing, or alternatively create a category of spells that fit any criteria you desire. In the example here, the function is applied to all spells that use the op24 'horror' effect, so it includes the Horror and Spook wizard spells, the Wand of Fear, the Emotion: Fear IWD spell, and any similar spells or items added by other mods. The 'evade_prefix' variable should be 3 or 4 characters and serves to distinguish on instance of this 'effect evasion' from other instances added by other mods.

The criteria for who can evade the chosen effects is much more flexible as well. Instead of just applying to thieves, it can specify any class or kit; alternatively, it can specify a spellstate and you can apply that spellstate to whomever you want, under whatever circumstances you want. The example here specifies the Skald kit gets an extra chance to save against fear effects.

The function also allows you to control which saving throw is used, so for instance a chance to evade the 'Flesh to Stone' spell could use the Polymorph/Petrification saving throw instead of Breath Weapon. Alternatively, instead of granting a saving throw (saves change over the course of the game and that may not be desirable for a mod's intent), the chance to avoid the effect can be set to a static percentage value. In this example Skalds get a saving throw vs. Spells to evade fear effects.

5: Innate Ability Pools

This function was born of my multiclass sorcerers mod. It creates a small group of spells which can be cast as innate abilities, in the 'spontaneous' manner similar to how a sorcerer casts spells. Except, of course, there are not nine 'spell levels' here, just one, so it functionally operates as a group of abilities which use a shared store of points. Some tabletop/SRD games out there have a 'ki warrior' class with a pool of 'ki points' to fuel special abilities; this is like that.

This function is a bit more complicated than the others. First you define an array with 6-character ability names chosen by you corresponding to various .SPL files. Then you define the number of points a kit or character should have, by making a .2da file that looks like a simplified spell table with only one spell level. Copy that table into /override.

Then run the semi_innate_casting function with the name of the array and the name of the points table, as well as a few other variables:
 - The table_spl variable is the name of a spell that will apply the ability pool points.
 - The semi_innate_prefix variable is a 4-character prefix used in various helper spells behind the scenes. It is used to distinguish between distinct instances of these ability pools.
 - The semi_innate_stat and semi_innate_shift variable determine how the pool points are recorded for a character, and they require a bit more discussion - though you can basically ignore these if you want to.
 
The function records points in a half-byte - four bits - of one of the various weapon proficiencies. Each proficiency has four bytes, or eight half-bytes, meaning 32 bits. (With me so far?) The default value in the function records ability pool points in proficiency 109, in values shifted 20 bits beyond the first bit value of the proficiency. It is sometimes written as (109 << 20). In my experience this is a safe value that does not conflict with any game mechanics or mods. You can change it to some other value - put it at (90 << 24) for example, the 4th byte of the Longsword proficiency. There could be reasons for this - mayeb you want the Blade kit to have distinct point pools for offensive and defensive abilities, or something. Or you make a thief kit with an ability pool and give fighters an ability pool, and you want them to work when dual-classed. Just do not set the semi_innate_shift variable to less then 8, since values below 8 are used by actual game proficiencies, and try not to use values that would interfere with other mods. (In particular, SoB/MnG/FnP currently use values in proficiencies 108, 110, 115, 124, 127, and 134.)

That all sounds very technical and complicated, but in practice it's not too bad, as the example hopefully shows. In the example, I give Skalds a pool of points to use to cast Armor of Faith, Remove Fear, and Draw Upon Holy Might. Excluding the inlined .2da points table (which ordinarily could be simply copied over from a mod folder), this only involves about 13 lines of Weidu to achieve.


##Bugs 

##Change Log 

##Credits

