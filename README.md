# GunsNAmmo
A mod resource for starbound, offering functionality for guns that use actual crafted ammo.

This mod aims to provide everyone with ability to add ammo-using guns into their own mods.

In this repo, you will find:

##Items

###Active/Weapons

This folder includes the custom .lua files that enable the ammo functionality, and some example weapons to demonstrate that functionality.

The following lua scripts are modified:

* weaponammo.lua
* ranged/gunammo.lua
* ranged/gunfireammo.lua

The following abilities are added:

* ranged/abilities/reloadammo
* randeg/abilities/recockbolt

The following examples are provided:

* examplecrossbow
* examplerifle
* exampleboltaction
* examplerevolver
* exampleenergy

###Augments/Ammo

This folder includes the ammo.lua script that provides the ammo functionality, and some example ammo items.

###Buildscripts

This folder contains a buildscript for magazine ammo items, initializing the ammo count to allow the magazine-loading blueprints, as well as a patch to include the two secondary abilities this mod provides.

## Recipes

This folder contains all the recipes for the example items, including a recipe for magazines that shows a way to use the item parameters.

All the recipes are craftable in examplecrafting station by default. The examplecrafting station is not craftable, it's not intended to be used as anything more than a way to showcase and test the crafting part of the mod.

## Interface

The weapons and ammo items use customised tooltips, weapons are more complex than ammo.

# How to utilise this mod

##First, add this mod to your mod. Two ways of doing that -

#### Install the mod in parallel and include it in your mods "requires" field in the .metadata file as a dependency

```JSON
{
  "version"         : " ",
  "steamContentId"  : " ",
  "link"            : " ",
  "friendlyName"    : " ",
  "requires"        : ["GunsNAmmo"],
  "description"     : " ",
  "name"            : " ",
  "author"          : " "
}
```

#### Copy the contents of the mod into your mod folder and include it in your mods "includes" field in the .metadata file

```JSON
{
  "version"         : " ",
  "steamContentId"  : " ",
  "link"            : " ",
  "friendlyName"    : " ",
  "includes"        : ["GunsNAmmo"],
  "description"     : " ",
  "name"            : " ",
  "author"          : " "
}
```

#### For the sake of compatibility, do not alter the contents of this mod. Copy things you want to change, rename, and work with copies. If you wish to change everything and throw out unneeded stuff, please simply copy the .lua files and rename them.

##Second - copy a weapon or an augment you wish to change (or base your new item on)

For example, inside a weapon file you will see :

```javascript
{
  "itemName" : "examplerifle",	//this is an example weapon. Copy the folder to your mod (in appropriate location) and change "examplerifle" for the name you want to use
  "price" : 150,
  "inventoryIcon" : "examplerifle.png:armed.1",
  "maxStack" : 1,
  "rarity" : "Common",
  "description" : "Shoots Bullets. Bullets not included.",
  "shortdescription" : "Example Rifle",
  "level" : 4,
  "tooltipKind" : "ammogun",//custom tooltip, values set by the .lua (the code is spread to gunammo.lua, ammo.lua and reloadammo.lua)
  "category" : "uniqueWeapon",
  "itemTags" : ["weapon","ranged","rifle"],
  "twoHanded" : true,
  
  "ammoMax" 		: 10,			//Specifies how big the guns' magazine is.
  "ammoAmount" 		: 0,			//Holds the current amount of ammo. Mostly set dynamically
  "usesAmmo" 		: true,			//Used by ammo to determine if it should bother trying to load into the gun
  "ammoTags" 		: ["bullet"],	//Tags, specifying the kind of ammo the gun takes
  "ammoName" 		: "",			//Used by the tooltip, set by ammo
  "ammoType" 		: "",			//Holds the name of currently used ammo item
  "magazineType"	: "none",		//Holds the name of currently used magazine item or false if not using magazines (can omit this). Set to "none" for making a gun without a mag in currently.
  "ammoIcon" 		: "",			//Used by the tooltip, set by ammo	
  "ammoCasing" 		: false,		//Used by the gunfireammo ability. False for no casings, name of the casing item otherwise. Supposed to be set by the ammo item.
  "extraAmmo" 		: false,		//Used for ejecting ammo, set dynamically
  "extraAmmoList" 	: [],			//Used for ejecting ammo, set dynamically
  "reloadParam" 	: false,		//false for reloadammo secondary ejecting ammo, [reload_amount,reload_cost,cooldown] for reloadammo reloading the gun (eg, for reloading one bullet for 20 energy every 2 seconds it will look like [1,20,2])

  "animation" : "/items/active/weapons/ranged/gun.animation",
  "animationParts" : {
    "butt" : "",
    "middle" : "examplerifle.png",
    "barrel" : "",
    "muzzleFlash" : "/items/active/weapons/ranged/muzzleflash.png"
  },
  "animationCustom" : {
	"animatedParts":{
      "stateTypes" : {
        "gunState" : {
          "default" : "empty",
          "states" : {					//animations are handled by both gunammo.lua (ammo states) and gunfireammo.lua (firing state). If you want to change those, make a copy and work with that.
			"firing" :{					//gun will transition to "firing" state each time you pull the trigger
			  "frames" : 4,
			  "cycle" : 0.05,
			  "mode" : "transition",
			  "transition" : "armed"	//transition mode and animation is set up for the crossbow. may not work for everything
			},
            "armed" : {					//gun transitions to "armed" state if it has ammo
              "frames" : 1,
			  "mode":"end"
            },
            "empty" : {					//gun transitions to "empty" if it has no ammo
              "frames" : 1,
			  "mode":"end"
            },
			"reloading" : {				//reloading animation, placeholder here but can be useful if reloadejects is set to false
			  "frames" : 1,
			  "mode" : "transition",
			  "transition" : "empty"
			}
          }
        }
      },

      "parts" : {
        "middle" : {
          "properties" : {
            "image" : null
          },

          "partStates" : {
            "gunState" : {
			  "firing" : {
				"properties" : {
				  "image" : "<partImage>:firing.<frame><paletteSwaps>"
				}
			  },
              "armed" : {
                "properties" : {
                  "image" : "<partImage>:armed.<frame><paletteSwaps>"
                }
              },
              "empty" : {
                "properties" : {
                  "image" : "<partImage>:empty.<frame><paletteSwaps>"
                }
              },
			  "reloading" : {
			    "properties" : {
				  "image" : "<partImage>:empty.<frame><paletteSwaps>"
				}
			  }
            }
          }
        }
      }
	},
    "sounds" : {
      "fire" 		: [ "/sfx/gun/ar1.ogg" ],
	  "empty"		: [ "/sfx/gun/reload/machinepistol_reload_clip1.ogg" ],
      "switchAmmo" 	: [ "/sfx/gun/reload/shotgun_reload_clip3.ogg" ]
    }
  },

  "baseOffset" : [0.6, 0.15],
  "muzzleOffset" : [2.2, 0.125],

  "scripts" : ["/items/active/weapons/ranged/gunammo.lua"],

  "elementalType" : "physical",

  "primaryAbility" : {
    "scripts" : ["/items/active/weapons/ranged/gunfireammo.lua"],
    "class" : "GunFire",

    "fireTime" : 0.5,
    "baseDps" : 20,
	"bonusDps" : 0,					//the parameter responsible for extra per-shot damage, introduced by ammo. Per shot damage gets spread over all the projectiles
    "energyUsage" : false,
    "inaccuracy" : 0.02,

    "projectileCount" : 1,
    "fireType" : "semi",//"auto", //burst,
	//"burstCount" : 3,
	//"burstTime"	: 0.25,

    "projectileType" : "woodenbolt",
    "projectileParameters" : {
      "knockback" : 5
    },
    "stances" : {
      "idle" : {
        "armRotation" : 0,
        "weaponRotation" : 0,
        "twoHanded" : true,
  
        "allowRotate" : true,
        "allowFlip" : true
      },
      "fire" : {
        "duration" : 0,
        "armRotation" : 3,
        "weaponRotation" : 3,
        "twoHanded" : true,
  
        "allowRotate" : false,
        "allowFlip" : false
      },
      "cooldown" : {
        "duration" : 0.1,
        "armRotation" : 3,
        "weaponRotation" : 3,
        "twoHanded" : true,
  
        "allowRotate" : false,
        "allowFlip" : false
      }
    }
  },

  "altAbilityType" : "reloadammo",		//the alt ability that handles reloading/ejecting
  
  "builder" : "/items/buildscripts/buildunrandweapon.lua"
}

```

As you can see, the custom additions to the weapons are thoroughly commented, so hopefully you will be able to get right into it!

This mod includes two new fireTypes - bolt and semi. 

Semi shoots only once per click, holding button will not produce any additional shots, but it allows you to shoot quite rapidly (ten times faster than the set delay) when tapping fire button. 

Bolt shoots only after recockbolt secondary is used.

Inside an ammo file, you will find :

```javascript
{
  "itemName" : "simplebulletammo",
  "price" : 100,
  "rarity" : "Common",
  "tooltipKind" : "ammoitem",
  "category" : "other",
  "inventoryIcon" : "icon.png",
  "description" : "A humble conventional cartridge.",
  "shortdescription" : "Simple bullet",

  "ammoType" : "simplebulletammo",									//type of ammo, has to be same as itemname since it's used for giving the ammo back in case of unloading
  "ammoTag"  : "bullet",											//ammo tag, has to match one of the tags in weapons "ammoTags" list
  "ammoCasing" : "simplecasing",									//casing item spawned on firing the weapon
  "ammoName" : "Simple Bullet",										//used for the tooltip?
  "ammoIcon" : "/items/augments/ammo/simplebulletammo/icon.png",	//used for the tooltip?
  "ammoProjectile" : "standardbullet",								//overrides projectile for the primary attack
  "ammoProjectileCount" : 1,										//overrides projectile count for the primary. Damage is divided evenly across the projectiles
  "ammoCount" : 1,													//determines how much ammo this item restores. The excess will be returned in items specified in "ammoType"
  "ammoDamage" : 0,													//damage per shot added by this type of ammo. This damage is split between all projectiles if projectile count is not 1
  "magazineType" : false,											//type of magazine used. put name of "empty magazine" item here, it will be used for spawning one
  "ammoMax" : false,												//for use with magazines. Replace "false" with number of ammo per magazine

  "scripts" : [ "/items/augments/ammo/ammo.lua" ]
}
```

More comments than there is actual config.

Ammo uses the same mechanics as augments. Drag ammo to a gun and right click to load it into the gun. Guns that use magazines, require magazines. Guns that don't, can't use magazines. Magazines can be hand-loaded by first separating one magazine from the stack, then loading it with bullets same way you'd load bullets into a gun. Putting more than one kind of ammo into a gun makes the gun eject all the previous ammo.

Alternatively, there is the unloaderammo item available to poke ammo out of the guns, as well as the unloadammo secondary.
