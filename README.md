# The Last Light Building System

**Modular base building for Arma Reforger**

> [!IMPORTANT]
> **Work in progress.** Placement rules, material balance and integrations can change. Local Workbench tests cover core gameplay and save restoration. Dedicated-server restart and remote-client testing remain pending.

## Overview

**TLL_BuildingSystem** is an Arma Reforger addon by **XirisX** for The Last Light, Project Ashfall and custom survival scenarios. Claim a building area, construct a modular base, authorize teammates with a PIN, and upgrade your structures from Wood to Titanium. Harvested materials, wreck salvage and crafting stations supply the building economy.

[Arma Reforger Workshop](https://reforger.armaplatform.com/workshop/6AB65B2ACF03D981-TLL_BuildingSystem) · [Workshop changelog](https://reforger.armaplatform.com/workshop/6AB65B2ACF03D981-TLL_BuildingSystem/changelog) · [Project Ashfall Discord](https://discord.gg/GH6vQzQ7en)

- [Install](#install)
- [Claim a base and start building](#claim-a-base-and-start-building)
- [PIN access, lockers and garage](#pin-access-lockers-and-garage)
- [Harvest, salvage and craft](#harvest-salvage-and-craft)
- [Construction costs and upgrades](#construction-costs-and-upgrades)
- [Maintenance and persistence](#maintenance-and-persistence)
- [Scenario setup and administration](#scenario-setup-and-administration)
- [Compatibility and verification](#compatibility-and-verification)

## Install

- **Players:** Enable TLL_BuildingSystem and its dependencies, then join a scenario that supplies the building kits and economy resources.
- **Server owners:** Add the BuildingSystem entry to the existing `game.mods` array and use compatible dependency versions for your scenario. [Bohemia Interactive server config reference](https://community.bistudio.com/wiki/Arma_Reforger%3AServer_Config)
- **Scenario authors:** Open `addon.gproj` in Enfusion Workbench to inspect the project, or add TLL_BuildingSystem as a dependency of your scenario.

```json
{
  "modId": "6AB65B2ACF03D981",
  "name": "TLL_BuildingSystem"
}
```

The current project ID is `TLLBuildingSystem`. Its project file declares base Arma Reforger (`58D0FB3206B6F859`), **TLL_LootSystem** (`6A4F61ED4143DA79`) and **TLL_VirtualGarage** (`6A6E805B44DBDA09`). LootSystem provides harvesting tools and shared resources. VirtualGarage provides the garage system used by the base garage integration. BuildingSystem handles its own base saves.

## Claim a base and start building

1. Equip the portable **Base Console** kit. Choose a valid spot, rotate the combined preview in 15° steps, and deploy. The kit creates a console and a normal Wooden square foundation at no extra material cost. The kit is consumed after the placement is saved.
2. The console establishes a **50 m horizontal building zone**. The server checks your access, your position and the structure's footprint. One owner can have one active base console.
3. Equip the **Building Manual**. Use the mouse wheel or D-pad up/down to select a piece. Position its translucent preview and confirm with the native interaction binding, normally **F** on keyboard.
4. Select the snapping or rotation option in the manual to adjust alignment. Hold **Sprint** and scroll or use D-pad up/down to adjust the height of a new square or triangle foundation in **0.1 m** steps, from **−0.5 m to +3 m**.

The manual supports square and triangular foundations, ceilings, full and half walls, staircases, doorways and window walls. Foundations can align to nearby foundations, walls and openings can snap to square or triangular edges, and ceilings can extend from compatible supports. The server checks placement, support, access and materials before committing the piece.

## PIN access, lockers and garage

### Base and door PINs

The console owner can set a four-digit **base PIN**. Players who enter it gain permission to build and access base storage. Changing the PIN revokes existing guest permissions.

Doorways have their own **door PIN** and lock state. The doorway owner can set or change the four-digit PIN, including codes with leading zeroes. Authorized guests can operate locked doors. Changing the door PIN revokes that door's guest access. Five wrong attempts trigger a 30-second request cooldown on the player controller.

### Storage lockers

Equip the matching portable notebook kit, preview and rotate the locker, then confirm placement inside an authorized base. The kit is spent after the locker is successfully saved.

| Locker | Accepted contents |
| --- | --- |
| Weapon Locker | Weapons, ammunition and supported weapon accessories |
| Military Equipment Locker | Items classified as military equipment, including canteens |
| General Locker | Other supported supplies and general items |

Storage filters also check nested items. A bag must satisfy the applicable locker rules along with its contents. Owners and console-authorized builders can access the base's lockers.

### Base garage

The VirtualGarage notebook can deploy **one garage console per base** inside an authorized building zone. The console is saved as part of that base and uses base access permissions. Vehicle and cargo records are handled by VirtualGarage.

## Harvest, salvage and craft

| Source | Default input or action | Output |
| --- | --- | --- |
| TLL LootSystem Old Workbench | 1 Log | 4 Planks |
| Rock / Stone Deposit | 5 valid pickaxe hits | 3 Stone per yield |
| Repair Modification Bench | 4 Stone | 1 Brick |
| Repair Modification Bench | 20 Scrap Metal | 1 box of 20 Nails |
| Repair Modification Bench | 3 full boxes of 20 Nails | 5 Construction Bolts |
| Repair Modification Bench | 60 Scrap Metal | 5 Construction Bolts |
| Furnace | 6 matching Ore | 1 Copper, Iron, Titanium or Silver Ingot, or 1 Gold Bar |
| Supported vehicle wreck | Search within about 3 m | 45% chance of 1–4 Scrap Metal |

Stone deposits yield four times by default and respawn after 10 minutes. Put away the Building Manual to use **Search car wreck**. Supported wreck families include UAZ, S105, S1203, M151, M998, Ural and M923. Each searched wreck has a shared **15-minute** cooldown, including searches that find nothing.

Crafting stations use carried inputs. They stage output before consuming ingredients and leave the product on the ground if inventory insertion fails. Partial nail boxes retain and display their remaining count. The bolt recipe that uses nail boxes requires three full boxes.

## Construction costs and upgrades

Each Wood module starts with the same base count of **Planks and Nails**.

| Wood module | Planks | Nails |
| --- | ---: | ---: |
| Square foundation | 10 | 10 |
| Triangle foundation | 6 | 6 |
| Ceiling | 8 | 8 |
| Wall | 8 | 8 |
| Half wall | 4 | 4 |
| Staircase | 12 | 12 |
| Doorway | 12 | 12 |
| Window wall | 10 | 10 |

For higher tiers, multiply the Wood count by the destination tier's multiplier for both planks and fasteners. Add the material in the final column.

| Destination tier | Multiplier | Fastener | Additional material |
| --- | ---: | --- | --- |
| Wood | ×1 | Nails | — |
| Stone | ×2 | Nails | Bricks equal to twice the Wood count |
| Copper | ×3 | Construction Bolts | 6 Copper Ingots, or 3 for a half wall |
| Iron | ×4 | Construction Bolts | 6 Iron Ingots, or 3 for a half wall |
| Titanium | ×5 | Construction Bolts | 4 Titanium Ingots, or 2 for a half wall |

For example, upgrading a square foundation to Copper costs **30 Planks, 30 Construction Bolts and 6 Copper Ingots**. Wood and Stone use nails. The three metal tiers use bolts and the matching ingot.

Use the manual's **Upgrade aimed piece** option for one piece. Manual building and upgrading use carried materials first, then loose materials within **3 m** of the player. For batch upgrades, open **Base Upgrade** within **3.5 m** of the console as an owner or authorized builder. **Upgrade All** targets eligible pieces at the base's lowest tier. **Upgrade Selected** lets you choose structure types. Console upgrades can use loose supplies within **10 m** of the console in addition to carried supplies. Both paths exclude items stored in other inventories.

The base must finish its lowest tier before pieces can advance beyond the next tier. For example, finish all Wood pieces before upgrading Stone pieces to Copper. The server recalculates the costs and preserves position, orientation, ownership and door PIN state on a successful upgrade.

## Maintenance and persistence

Use **Refresh Base - 7 Days** at the console to reset the deadline to seven real days from the refresh. Owners and authorized builders can refresh before expiry, and the deadline continues while players are offline. Expiry removes the console and its bound structures, lockers and stored contents. Saved base membership determines what gets removed, so neighboring bases are preserved.

Building saves are mission-specific and stored under `$profile:TLL_BuildingSystem/`. They track supported structures, transforms, tiers, ownership, base membership, PIN and access state, expiry and locker contents. Storage records cover nested items, magazine ammunition, item health where applicable and remaining canteen water. Older records without a water field load the item's prefab default. Save validation and backup handling are part of BuildingSystem. Player inventory saves belong to TLL_Persistence, and garage cargo uses VirtualGarage's own storage.

## Scenario setup and administration

- **Supply kits:** Make the Building Manual, portable Base Console and locker notebooks available through your scenario, traders or loot catalogs. Use the VirtualGarage notebook if the scenario offers vehicle storage.
- **Place resource sources:** Add the TLL_LootSystem Stone Deposit and Old Workbench, plus the BuildingSystem Furnace and Repair Modification Bench. Existing supported vehicle wrecks can provide salvage.
- **Tune the economy:** Edit `Assets/Building/Economy/Configs/TLL_Economy.conf` for tier multipliers, configured recipe quantities and wreck rewards. The current bolt recipes use fixed quantities in `Scripts/Game/TLL_BuildEconomy.c`.
- **Inspect sample worlds:** Open `Assets/Building/Worlds/TLL_BuildingShowcase.ent` for building pieces or `Assets/Building/Economy/Worlds/TLL_EconomyShowcase.ent` for stations and economy items.
- **Admin Gun:** Spawn `E_TLL_AdminGun.et` through Game Master. The server requires the native **ADMINISTRATOR** role and validates the aim trace. Firing at a supported structure within 100 m requests saved removal of that piece. Targeting the base console removes the entire bound base and its locker contents.

### Server-side API

Trusted Enfusion mods can use **TLL_BaseAPI v1** after BuildingSystem persistence initializes. Use `ListBases` or `GetBase` to read owner identity, console position, expiry and piece counts. The API provides expiry-only removal by default, explicit removal under a server policy, and an administrator-checked route. It requires audit information and rejects stale snapshots. See `Docs/BuildingAPI.md` in the source project for signatures and result codes.

## Compatibility and verification

The current project was validated locally with **Arma Reforger 1.9.0.37**. The recorded Workbench run compiled and passed **217 checks** covering locker categories, permissions, placement, saved-content restoration in a fresh play session, console and starter foundation deployment, expiry and the base destruction API. Separate local runs covered base upgrades and the Admin Gun. Dedicated-server restart, remote transfers, real administrator login and physical console hardware still need verification. The source report is `Docs/Compatibility_1_9_20261106.md`.

## Credits and license

**XirisX** develops TLL_BuildingSystem for The Last Light and Project Ashfall. Thanks to **NewbKodii**, creator of Building System 2, for inspiring the translucent placement preview.

The Workshop lists the release under the **Arma Public License (APL)**. Individual asset credits are recorded in `Assets/Building/Economy/ATTRIBUTION.txt` and `Assets/Building/Models/NailBox/ATTRIBUTION.txt`. The Low Poly Nail Box is by **Myers2860**. [Workshop listing](https://reforger.armaplatform.com/workshop/6AB65B2ACF03D981-TLL_BuildingSystem)
