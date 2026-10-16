# Fallout 4 → Starfield: a measured conversion toolchain

**Bring Fallout 4 into Starfield's Creation Engine 2 through a measured, repeatable conversion toolchain.** The goal is to preserve 
Fallout 4 gameplay while using Starfield's native physics and modern engine capabilities. Experimental interiors already load in Starfield; the full game conversion is unfinished.

Converters read **your own copies of both games** and generate local outputs. No game files or conversion pack are distributed. Existing in-game screenshots depict Bethesda-owned visuals and are excluded from the project's code license.

[Latest progress](#latest-progress--2026-10-15) · [Run an installed build](#run-an-installed-build-windows) · [Cell commands](#visit-converted-cells) · [Live updates](https://github.com/fo4sf/fo4-to-starfield/issues/32) · [Contribute](CONTRIBUTING.md)

## Latest progress — 2026-10-15

**October 14 recap: worldspace launches work again, and the observation bridge reads player position directly from the game.** Dates here use Mountain time; some evening commits appear as October 15 in UTC.

- **Claude fixed the load-order dialog and verified it in two launches.** The runner selects **Current**, checks that experiment plugins remain active, and stops if activation changes. Converted worldspaces loaded again, and all 18 terrain probes ran in daylight. The terrain verdicts remained inconclusive because of an OCR error and a misplaced landing; this is not ground-texture acceptance. [Verified run and captures](https://github.com/fo4sf/fo4-to-starfield/issues/32#issuecomment-6165570079).
- **The new observation bridge worked in an eight-minute in-game session.** It recorded 473 log lines with player position, heading and cell/world IDs. A two-second walk measured 1.64 m; the bridge's timer/state survived save/load. This verifies the telemetry bridge, not converted-game persistence. The runner then gained log-based player reads, reducing its dependence on console OCR; object-reference reads still need separate checks. [Session record](https://github.com/fo4sf/fo4-to-starfield/commit/32a5f5b61fa12b9401fb75f2deb4659cb840af90).
- **Codex and Grok tightened evidence handling.** Codex added Windows protection against archive changes during fingerprinting; Grok corrected terrain-evidence aliases and malformed-number handling. These are offline correctness improvements, with current merge/review status below.

**October 15 follow-up:** Claude reports the Sanctuary–Red Rocket walk passed in both directions on build `2470f876` + core64c, about 254 m each way, using bridge position reads. Independent native acceptance remains open. The cave door still does not load: its persistent reference reports an unmerged position, and the first proposed fix was refuted. A probe-bearing exterior-cell override also displaced the spawn, so that construction needs redesign. [Pinned walk journal](https://github.com/fo4sf/fo4-to-starfield/blob/10e7019be4e97ce8976b1f0eeb164ecb8d19b04b/docs/JOURNAL.md#L1139), [cave A/B results](https://github.com/fo4sf/fo4-to-starfield/issues/32#issuecomment-6187749682).

The consolidated runner is now on `main` through [integration #121](https://github.com/fo4sf/fo4-to-starfield/pull/121). Its individual features and unfinished acceptance gates are distinct:

| Work | Evidence and current limit |
|---|---|
| **Interiors, materials and native collision** | Vault 111, Vault 81, Red Rocket cave, Vault 114, Prydwen, Hotel Rexford, Boston Public Library and Parsons load in experimental builds. Stairs and hinged doors have recorded in-game passes; the complete interior acceptance gate remains open. |
| **Commonwealth terrain and structures** | Core64c stages 648,358 references in 1,708 populated cells across a 6.4 km extent. That is placement coverage, not tested map coverage or an engine-size guarantee. Earlier campaigns recorded 7/7 smoke, 29/30 nearby and 15/15 informational sampled height matches. The northeast +1.8939 m mismatch and four old jumps in the wrong coordinate frame remain preserved. [Completed informational run](https://github.com/fo4sf/fo4-to-starfield/issues/32#issuecomment-6154661951). |
| **Connected walking and cave** | Both-direction walking is lead-reported in-game; the cave transition remains blocked. Correct entry/exit, facing, door/frame collision and save/reload are still required before accepting the connected slice. |
| **Independent evidence checks** | The original [read-only checker](docs/testing/commonwealth-evidence.md) and [Windows archive guard, #120](https://github.com/fo4sf/fo4-to-starfield/pull/120), are merged. Codex's draft, unmerged [door/walk audit, #118](https://github.com/fo4sf/fo4-to-starfield/pull/118), passes a 414-test Windows suite (3 skips), independent QA and CI; native adapter proof is pending. Grok's [terrain oracle, #69](https://github.com/fo4sf/fo4-to-starfield/pull/69), passes 21 author tests and 14 independent controls; merge is pending. Neither review establishes native acceptance. |
| **Recoverable deployment** | Experiment recovery is on `main`. Codex's additional [bridge recovery fix, #125](https://github.com/fo4sf/fo4-to-starfield/pull/125), retains interrupted/ambiguous ownership and passes 43 focused tests, a 1,301-test Windows suite (70 skips), independent QA and CI. It is ready for integration, not deployed. [Configuration-race work, #104](https://github.com/fo4sf/fo4-to-starfield/pull/104), also remains proposed. |
| **NPC navigation and combat** | The test harness now reads combat state, target and line of sight. A vanilla hostile engaged in one attempt but not another; the converted raider did not engage or move. A forced-engagement control is planned before judging the conversion. [Native observations](https://github.com/fo4sf/fo4-to-starfield/issues/32#issuecomment-6187749682). |

The full interior queue, movable-object replanning, cave connection and broader world streaming remain unfinished. A fresh checkout of `main` does not install game assets or reproduce an experimental build automatically.
Follow [the shared update thread](https://github.com/fo4sf/fo4-to-starfield/issues/32) for exact revisions, results and corrections.

### Recent in-game route results

Earlier results reported by Claude on 2026-10-10 for one pinned test build (code `1d3d5bf`; details and limitations in [shared status #32](https://github.com/fo4sf/fo4-to-starfield/issues/32)). These are separate from the newer integration batch above:

| Test area | Result |
|---|---|
| Vault 111 | 12/12 routes passed, including stairs and a switch door from both sides |
| Parsons | 11 hinged doors passed from both sides with an `OPEN` prompt recorded |
| Vault 114 | 28 routes passed; three doors still failed on both sides and three passed on one side only |
| Hotel Rexford | 28 routes passed; one door failed on both sides and three passed on one side only |
| Prydwen | Partial run: 11 passes, 7 falls, and 14 unread results among 33 reported attempts (one attempt is unclassified in the status summary) |
| Vault 81 and Boston Public Library | Not yet run on that pinned route build |

These are reports for a particular build, not a guarantee about other builds or a finished conversion. Offline tests and screenshots are not substitutes for in-game acceptance.

### See the work

**Red Rocket after the Sanctuary walk — October 15 follow-up.** Claude's capture from the experimental core64c route; appearance and connected-cave acceptance remain unfinished.

![Red Rocket exterior at the end of the Sanctuary walk](docs/media/walk-s2rr-end-1015.jpg)

**Vault 111 during the October 14 observation-bridge session.** The bridge measurements are recorded in the linked session log above; the screenshot illustrates the scene.

![Converted Vault 111 during observation-bridge testing](docs/media/bridge-vault111-1014.jpg)

**Hotel Rexford: native hinged door**

![Hotel Rexford door opened in Starfield](docs/media/rexford-door-native.jpg)

**Parsons: both-side door evidence**

![Parsons hinged door route, side A](docs/media/route-parsons-door-bothsides-a.jpg)

[Opposite-side capture](docs/media/route-parsons-door-bothsides-b.jpg) · [First textured chair](docs/media/fo4-chair-textured-in-starfield.png) · [Vault 111 stairs](docs/media/vault111-stairs-catwalk-in-starfield.png)

More captured work: [Vault 111](docs/spikes/WP10-vault111-first-light.md) · [additional interiors](docs/spikes/WP10-more-interiors.md) · [collision](docs/spikes/S6-collision.md) · [door routes and test evidence](https://github.com/fo4sf/fo4-to-starfield/issues/32).

## What remains

The interior slice still needs reliable stairs, clear openings, both-side door operation, pushable objects and repeatable deployment. Some route failures come from test positioning; others require geometry or physics fixes. Nearby terrain has sampled evidence and continuous walking has lead-reported passes; the cave connection, independent acceptance, larger regional streaming and actor behavior remain open. Quests, dialogue, combat systems and full-game parity are unfinished. See [the issue tracker](https://github.com/fo4sf/fo4-to-starfield/issues) and [risk register](docs/RISKS.md).

## Run an installed build (Windows)

If your converted build is **already installed and enabled** in Starfield, this PowerShell command launches the Steam game:

```powershell
Start-Process "steam://rungameid/1716740"
```

Load a dedicated test save, then use the console commands below. Downloading this repository alone does not install converted cells.

For an existing converted staging folder, deploy it from the repository folder before launching. Replace the two placeholders with your local paths; see [setup](docs/SETUP.md) for prerequisites, including the Creation Kit's Archive2 tool:

```powershell
python -I scripts/deploy_starfield.py install --staging "<converted-staging>" --starfield "<Starfield-install>"
```

The installer refuses to overwrite an existing deployment. Its paired cleanup command is:

```powershell
python -I scripts/deploy_starfield.py uninstall --starfield "<Starfield-install>"
```

## Visit converted cells

Open Starfield's console with the tilde/backtick key and enter **one** command. Each destination must be included in your installed build; an unbuilt cell is unavailable.

| Destination | Starfield console command |
|---|---|
| Vault 111 | `coc FO4Port_Vault111Cryo` |
| Vault 81 | `coc FO4Port_Vault81` |
| Red Rocket cave | `coc FO4Port_RedRocket01` |
| Vault 114 | `coc FO4Port_Vault114` |
| Prydwen | `coc FO4Port_PrydwenHull01` |
| Hotel Rexford | `coc FO4Port_GoodneighborHotelRexford` |
| Boston Public Library | `coc FO4Port_BostonPublicLibrary02` |
| Parsons State | `coc FO4Port_ParsonsState01` |

These are prototype cells for testing, not a campaign start or a completed playthrough.

## Why conversion is difficult

| Format area | Fallout 4 | Starfield |
|---|---|---|
| Plugin form version | 131 | 581 |
| Shared record fields | Sampled overlap is mostly 25–60% | Different record model and fields |
| Terrain | 37,020 `LAND` records in the measured sample | `.btd` terrain and procedural planets |
| Meshes | NIF geometry | NIF metadata plus external `.mesh` geometry |
| Materials | `.bgsm` / `.bgem` | JSON `.mat`, compiled into `.cdb` |
| Collision | Havok 2014 packfile | Havok tagfile and `hknp` shapes |

These are measured research notes, not promises of complete format support. Methods and sources: [format gap](docs/FORMAT-GAP.md), [spikes](docs/spikes/), and [measurement scripts](scripts/).

## Try it

```powershell
git clone https://github.com/fo4sf/fo4-to-starfield.git
cd fo4-to-starfield
python -m pip install -r requirements-ci.txt
python -m unittest discover -s tests -v
python scripts/guard.py
```

`scripts/convert_batch.py` converts groups of statics and now prints the top failure reasons by count (with percentages) after a run and writes failure-summary.json, making common issues easier to spot and process programmatically.

To reproduce measurements, install the tools listed in [setup](docs/SETUP.md), use your own game installations, and provide local input paths to the recon scripts. Keep generated data and converted assets outside this repository. Read [contributing](CONTRIBUTING.md) before opening a pull request.

## Safety and project rules

- **No raw game assets or extracted files in Git or releases.** Do not submit `.esm`, `.ba2`, `.nif`, `.dds`, `.mesh`, `.hkx`, audio, executables, or converted outputs. The repository currently includes a limited set of screenshots captured in-game; these depict third-party visuals, are excluded from the MIT grant, and do not establish permission for reuse. The guard and CI check for raw assets and local paths.
- Converters read local files selected by the user; generated outputs stay local. The project does not distribute a conversion pack.
- Tests use synthetic fixtures. Offline passes do not prove that an asset works in-game.
- This is for offline, single-player modding research. It does not modify either game's executable.
- Do not commit credentials, personal machine paths, or private game data. Report suspected vulnerabilities using [GitHub's private reporting option](https://github.com/fo4sf/fo4-to-starfield/security/advisories/new) when available; see [SECURITY.md](SECURITY.md).

## Legal and license

This repository is unofficial and is not affiliated with or endorsed by Bethesda Softworks or ZeniMax Media. Fallout, Starfield, Creation Engine, and related names and materials belong to their respective owners. **The MIT license in this repository applies only to original project code and documentation; it does not grant rights to Bethesda software, game assets, trademarks, screenshots, or user-generated outputs.**

The project does not provide legal advice or establish that any particular extraction, conversion, or use is permitted. Check the current [Bethesda Terms of Service](https://bethesda.net/data/tos/en.html), the applicable [game EULA](https://bethesda.net/data/eula/en.html), and laws where you live; seek qualified legal advice if you need a determination for your circumstances. Avoid uploading or sharing game-derived outputs unless you have confirmed you have the necessary rights.

## Contributing and attribution

Read [CONTRIBUTING.md](CONTRIBUTING.md), choose an open issue, and use its acceptance criteria. Every change must pass the synthetic tests and asset guard. Identify whether a result is **in-game verified**, **offline tested**, or an **unverified hypothesis**.

The project is led by the repository owner. AI-assisted contributions are acknowledged by role: **Claude** (conversion pipeline and in-game testing), **Codex** (deployment, recovery, persistent identities, and output verification), and **Grok** (acceptance criteria and evidence gates). Agent labels and commit subjects identify work; these names do not represent separate human contributors or endorsements.
