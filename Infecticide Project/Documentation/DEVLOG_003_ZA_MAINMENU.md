# Infecticide – Development Log

**Developer(s):** Zayne Anderson, Paul Nosakowski, Laura Kozel
**Project Type:** Arena Roguelike
**Engine / Tech Stack:** Unreal Engine 5, Blueprints, UMG, Niagara
**Current Status:** Pre-Alpha

---

## Devlog Entry – 003 (File: DEVLOG_003_ZA_MAINMENU.md)

**Title:** DEVLOG_003_ZA_MAINMENU
**Date:** 2026-02-18
**Author:** Zayne Anderson

---

### Focus of This Entry

Initial development session. Primary focus was building the Main Menu UI from scratch, including visual identity, font selection, material design, and animated UI elements. Also handled repository setup and branch management for the team.

---

### Systems Implemented

- WBP_MainMenu widget with Play, Settings, and Quit buttons
- Cabin Sketch font imported and applied to title text
- Comfortaa font imported and applied to button text
- Blood flow font material using Time > Sin > Lerp (0.6–1.0) on a 2 second loop applied to title
- Button animation with scale, color pulse, and wobble on a staggered per-button basis — Settings swings left first, Play and Quit swing right first
- Outward pulse material on background plane using TextureCoordinate > Length > Time - Length > Sin node chain
- Background plane with radial red pulse material creating arterial glow effect behind buttons

---

### Systems Updated / Modified

- None this session

---

### Technical Architecture Notes

- Font switching at runtime via Set Font and Slate Font Info struct is functional but should be driven through UMG animation notifies rather than Blueprint tick or construction script logic to avoid instability
- Button animations are driven by UMG timeline animations, not Blueprint tick — this keeps them performant and easy to adjust
- Background pulse material is a world-space plane sitting behind the UI camera, not a UMG image widget — allows for future expansion to a full 3D arterial background using a cylinder with two-sided material
- Branch structure: Main > Development > User branches (one per developer)

---

### Known Issues / Risks

- Git LFS not yet configured — binary asset conflicts may become an issue as the project grows and more .uasset and .umap files are added
- Blueprint binary files cannot be merged — each Blueprint should have a single designated owner to avoid conflicts

---

### Required Setup Instructions

Before continuing work, all team members should:

- Pull latest from Development branch before starting any new work
- Do not work directly on Development or Main — all work goes on User branch first

---

### Testing Notes

- Tested: Main menu loads correctly, all three buttons present, animations playing, background pulse material running, fonts rendering correctly
- Untested: Play button level transition to Arena, Settings audio sliders (not yet built), Quit confirmation dialog

---

### Next Development Targets

- Add floating organism sprites or Niagara particles to background for arterial atmosphere
- Build Settings widget with audio sliders (Master, Music, SFX)
- Build Pause/Quit menu widget to hand off or implement alongside player controller
- Support NPC implementation once Laura and Paul have Arena and Player Controller merged to Development

---

### Additional Notes

Visual direction established this session: organic/biological aesthetic using warm reds and pinks, hand-drawn Cabin Sketch font for title, Comfortaa for UI text. Background is designed to evoke the interior of a blood vessel. All animated elements use organic timing to feel alive rather than mechanical. This visual language should be carried forward into HUD and Pause menu designs.

---

## Devlog Index

| Entry | Date | Focus |
|-------|------|-------|
| 003 | 2026-02-18 | Main Menu UI, Visual Identity, Repository Setup |
