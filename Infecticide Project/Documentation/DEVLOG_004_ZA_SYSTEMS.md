# Development Log Template

---

# Infecticide – Development Log

**Developer(s):** Zayne Anderson
**Project Type:** Arena Roguelike
**Engine / Tech Stack:** Unreal Engine 5
**Current Status:** Prototype

---

## Devlog Entry – 1

**Title:** DEVLOG_004_ZA_SYSTEMS
**Date:** 2026-02-21
**Author:** Zayne Anderson

---

### Focus of This Entry

Ability system architecture, stat upgrade framework, health/regen systems, and initial projectile ability implementation.

---

### Systems Implemented

- **AC_SpikeBody** — Offensive ability component. Cycles between an active state (player deals contact damage to enemies) and a cooldown state (player takes contact damage). Uses a self-sustaining ping-pong timer system with two separate timer handles (ActivatedTimerHandle, CooldownTimerHandle). Damage is calculated via CalculateDamage: (SpikeDamage + PlayerRef.Damage) * DamageMultiplier. Cycle timing reads from PlayerRef.Duration and PlayerRef.Cooldown and recalculates each cycle via UpdateStats. Niagara effect (NS_SpikeBody) spawns and attaches to player mesh on component BeginPlay, activates/deactivates with cycle state.
- **NS_SpikeBody** — Niagara particle system. Cyan burst particles radiating outward from player during spike active state. Spawned and managed by AC_SpikeBody.
- **BP_AntibodyProjectile** — Offensive projectile actor (untested). Orbits the player using Cos/Sin tick-based position calculation. Contains EnemyDetection sphere collision using Actor Tags ("Enemy") for target detection. Switches from orbit to fired state on enemy detection, launching via Projectile Movement Component.
- **S_Ability** — Struct defining ability data: AbilityName, AbilityDescription, AbilityIcon, AbilityClass, AbilityType.
- **S_StatUpgrade** — Struct defining stat upgrade card data: CardName, CardInfo, CardImage, Primary (E_StatType), PrimaryValue, PrimaryApplication (E_ApplicationType), Secondary (E_StatType), SecondaryValue, SecondaryApplication, RequiredAbility.
- **E_StatType** — Enum: None, Damage, Health, Regen, Speed, Defense, Cooldown, Duration.
- **E_ApplicationType** — Enum: Add, Subtract, Multiply, Divide.
- **DT_Abilities** — Data table using S_Ability. Currently contains AC_SpikeBody row.
- **DT_StatUpgrades** — Data table using S_StatUpgrade. 21 rows covering all stat types including tradeoff cards and ability-gated cards (SpikeExtension, RefractoryReduction, HyperactivResponse gated behind RequiredAbility: AC_SpikeBody).
- **WBP_AbilityCard** — Card widget with Button as root. Accepts S_Ability struct via AddCardDetails, breaks struct, and populates CardTitle, CardImage, CardInfo, and stores AbilityClass and AbilityType. Contains ED_OnCardSelected event dispatcher passing AbilityClass.
- **WBP_GameOver** — Game over screen (functional).

---

### Systems Updated / Modified

- **AC_Health** — Added regen/drain system. CanPlayerRegen function evaluates RegenRate: == 0 returns false (neutral), < 0 forces bInjured = true and returns true (drain state), > 0 returns true (regen state). RegenHealth event calls AddHealth(RegenRate) on a 1-second looping timer. Supports negative RegenRate for drain builds (CombustionCell interaction).
- **BPC_PlayerCharacter** — Added variables: Damage (Float), DamageMultiplier (Float, default 1.0), Defense (Float), Duration (Float), Cooldown (Float), BaseDuration (Float, constant), BaseCooldown (Float, constant), CurrentLevel (Integer), OnUpgradeSelected (Event Dispatcher).
- **WBP_Health** — Now displays when health drops below 100%.

---

### Technical Architecture Notes

- Damage formula centralised on BPC_PlayerCharacter: FinalDamage = (AbilityBaseDamage + Damage) * DamageMultiplier. Ability components read from PlayerRef rather than storing their own copies, ensuring stat upgrades apply universally across all abilities.
- AC_SpikeBody reads Duration and Cooldown from PlayerRef each cycle via UpdateStats, so mid-game stat upgrades take effect on the next cycle tick without requiring a restart.
- Timing upgrades use multiplier-based scaling: NewDuration = BaseDuration * DurationMultiplier, NewCooldown = BaseCooldown * CooldownMultiplier. Base values are constants that never change, preventing compounding drift.
- DT_StatUpgrades uses E_ApplicationType per stat field instead of a bSubtract bool, making the apply logic a clean switch with no special casing. Negative flat values (e.g. RefractoryReduction PrimaryValue: -1.0) handle subtraction for Add-type stats without additional flags.
- Ability-gated stat upgrade cards use a RequiredAbility (TSubclassOf ActorComponent) field in S_StatUpgrade. Filter logic in WBP_UpgradeSelection will check GetComponentByClass(RequiredAbility) on the player, skipping cards for unowned abilities automatically.
- CombustionCell (2x DamageMultiplier / -4.0 RegenRate) creates an emergent risk: players with no regen enter a drain state, effectively trading HP per second for doubled damage output. AC_Health's CanPlayerRegen change to != 0 enables this without additional logic.

---

### Known Issues / Risks

- BP_AntibodyProjectile untested — orbit and firing logic require in-engine validation.
- AC_Health regen system untested.
- DT_Abilities and DT_StatUpgrades untested — WBP_UpgradeSelection not yet wired.
- WBP_UpgradeSelection not yet built — card selection, game pause, and OnUpgradeSelected dispatcher flow not yet functional.
- BP_WaveSpawner not yet created — InitializePlay, wave loop, and dispatcher binding not implemented.
- With only one row in DT_Abilities, the card selection pool cannot return 3 unique offensive abilities. AC_AntibodyBurst and AC_ComplementChain rows needed before the upgrade screen can function.

---

### Required Setup Instructions

- Ensure AC_Defense component is created and pre-attached to BPC_PlayerCharacter before testing stat upgrades targeting Defense.
- BP_AntibodyProjectile requires enemies with the Actor Tag "Enemy" to be present in the level for detection logic to trigger.

---

### Testing Notes

- **Tested:** Player death — Success. Enemy death — Success.
- **Untested:** AC_Health regen/drain, BP_AntibodyProjectile orbit and firing, DT_StatUpgrades apply logic, DT_Abilities card pool selection, WBP_AbilityCard population.

---

### Next Development Targets

- Build WBP_UpgradeSelection — layout, random row selection from DT_Abilities/DT_StatUpgrades, card population, OnCardSelected dispatcher binding.
- Build BP_WaveSpawner — InitializePlay flow, bind OnUpgradeSelected, BeginWave enemy spawning loop.
- Create AC_AntibodyBurst component and add to DT_Abilities.
- Test BP_AntibodyProjectile orbit and firing in editor.
- Wire player capsule overlap logic for SpikeBody contact damage routing.

---

### Additional Notes

- CombustionCell design creates meaningful build tension: stacking regen before taking it is a deliberate strategic decision rather than an obvious pick. Consider communicating the drain risk clearly in the CardInfo description.
- Stat upgrade card pool currently has 21 rows. Three (SpikeExtension, RefractoryReduction, HyperactivResponse) are ability-gated and will not appear unless the player owns AC_SpikeBody.

---

## Devlog Index

| Entry | Date | Focus |
|-------|------|-------|
| 004 | 2026-02-21 | Ability system, stat upgrades, health/regen, UI cards |
