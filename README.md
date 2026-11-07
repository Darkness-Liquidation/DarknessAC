<div align="center">
  <h1>Darkness Liquidation</h1>
  <p>A premium, prediction-based anti-cheat and server-protection plugin for Minecraft servers.</p>

  <p>
    <a href="https://dsc.gg/darkness-liquidation">
      <img alt="Discord" src="https://img.shields.io/discord/1380822433631834122?style=flat&label=Discord&logo=discord&color=5865F2&logoColor=white">
    </a>
    <img alt="Java 17" src="https://img.shields.io/badge/Java-17%2B-ED8B00?style=flat&logo=openjdk&logoColor=white">
    <img alt="Platform Paper and Folia" src="https://img.shields.io/badge/Platform-Paper%20%2F%20Folia-FFFFFF?style=flat&logo=papermc&logoColor=black">
  </p>

  <p>
    <a href="README.md"><b>English</b></a>
    &middot;
    <a href="README-ru.md">Русский</a>
    &middot;
    <a href="README-uk.md">Українська</a>
  </p>
</div>

---

### What is Darkness Liquidation?

Darkness Liquidation is a premium anti-cheat and protection plugin for Minecraft servers.
It combines prediction-based movement validation, packet analysis, combat checks, exploit
mitigation, and configurable moderation tools to help server staff detect and handle unfair play.

Darkness Liquidation is a fork of [GrimAC](https://github.com/GrimAnticheat/Grim). It builds on
GrimAC's prediction-based foundation and adds its own checks, client-protection modules,
administration tools, themes, punishments, and integrations.

### Requirements

- Java 17 or newer to run the plugin.
- A Paper-compatible server. Folia is supported.
- A valid Darkness license. Set it in `plugins/Darkness/config.yml` after the first start.

### Installation

1. Obtain the Darkness Liquidation plugin and a valid license through the community team.
2. Put `Darkness.jar` into your server's `plugins` directory.
3. Start the server once to generate the configuration files.
4. Open `plugins/Darkness/config.yml` and enter your license in `your_license`.
5. Review the checks, punishments, theme, logging, and integration settings.
6. Restart the server or use `/darkness reload` after changing configuration.

### Features

- Prediction-based checks for movement, combat, reach, velocity, inventory, scaffold, timer,
  elytra, vehicles, packets, and more.
- TrustFactor player reputation system with dynamic setback scaling and database persistence.
- Automatic configuration migrations preserving user comments and custom thresholds.
- Packet and exploit protection against invalid packets, crash attempts, suspicious client data,
  blink-like behaviour, and other protocol abuse.
- Information-hiding tools that can spoof selected client-visible data and help reduce cheat-client
  advantages.
- Client-brand monitoring and configurable rules for suspicious or blocked clients.
- Alerts, verbose output, player profiles, performance metrics, logs, Discord webhooks, and proxy
  alert forwarding.
- Configurable punishments, punishment waves, staff tools, themes, and English, Russian, and
  Ukrainian configuration files.
- Optional integrations with ViaVersion, ViaBackwards, Geyser, Floodgate, LuckPerms, and
  PlaceholderAPI.
- Paper and Folia support.

### Prediction engine

Darkness Liquidation is built on the prediction-based foundation of GrimAC. Instead of relying
only on simple packet limits, the engine recreates the set of movements a vanilla client could
legitimately perform and compares incoming actions against that model.

- **Vanilla movement simulation.** The engine accounts for walking, swimming, knockback, cobwebs,
  bubble columns, vehicles, elytra, and other vanilla mechanics.
- **Version-aware physics and collisions.** It handles client/server version differences, collision
  order, bounding-box changes, ViaVersion replacement blocks, and version-specific block data.
- **Per-player world replication.** The anti-cheat builds a client-view world cache from chunk,
  block-place, and block-change packets. This lets checks safely evaluate packet-side and fake
  blocks without treating them as automatic violations.
- **Latency and inventory compensation.** World updates, player states, and inventory data are
  tracked in the order they reach the client to reduce false positives caused by latency,
  ghost blocks, or desynchronisation.
- **Asynchronous architecture.** Most movement processing and listeners run around the network
  pipeline, while per-player state is designed for safe concurrent access and server scalability.
- **Security through accurate simulation.** By validating against possible vanilla behaviour,
  checks aim to make invalid movement mathematically distinguishable rather than relying on
  obscurity alone.

### TrustFactor system

Darkness tracks player trust across sessions to dynamically scale mitigation and setbacks:

- **Trust scoring.** Players hold a trust score (0 to 100) calculated inversely from accumulated
  trust points. Fewer violation points mean higher trust.
- **Trust tiers:**
  - `GREEN` (0-100 points): Trusted. Soft setbacks (`GRIM`) minimise false-positive disruption.
  - `ORANGE` (101-350 points): Suspicious. Moderate setbacks (`GRIM_AIR`) with air mitigations.
  - `RED` (> 350 points): Untrusted. Hard setbacks (`DARKNESS`) with immediate position correction.
- **Dynamic setbacks.** Checks configured with `setback_type: TRUST` automatically scale their
  setback severity to the player's active trust tier.
- **Database storage.** Trust levels persist across server restarts and network proxies via async
  batch logging to SQLite, MySQL, or PostgreSQL.
- **Placeholders.** PlaceholderAPI and punishment messages support `%trust_score%`,
  `%trust_points%`, `%trust_level%`, `%trust_color%`, `%trust_type%`, and `%trust_banned%`.

### Developer API

Darkness provides a plugin API for integrations with custom plugins and server systems. It can be
used to build extensions around Darkness events and functionality without depending on internal
implementation details.

- API repository and documentation: [1hendex/DarknessAPI](https://github.com/1hendex/DarknessAPI)

### Configuration

The main configuration is generated under `plugins/Darkness/`. The bundled defaults are located in
this repository:

- [`config/en.yml`](src/main/resources/config/en.yml) — general settings, alerts, client protection,
  Discord, logging, punishments, and integrations.
- [`checks/en.yml`](src/main/resources/checks/en.yml) — individual anti-cheat checks and thresholds.
- [`hider/en.yml`](src/main/resources/hider/en.yml) — Information Hider settings, packet-level ESP
  mitigation, and client data obfuscation.
- [`punishments/en.yml`](src/main/resources/punishments/en.yml) — violation punishment rules.
- [`themes/`](src/main/resources/themes) — alert and message themes.

English, Russian, and Ukrainian variants are included. Tune settings gradually and test changes on
your own server before using strict punishments in production.

### Full command list

The default primary command is `/darkness`. You can change the command names in `config.yml` with
`command_name`, for example `command_name: darkness,dl`; the first name becomes the primary command
and the remaining names are aliases. Restart the server after changing this setting. Arguments in
`<angle brackets>` are required; `[square brackets]` are optional.

| Command | Purpose |
| --- | --- |
| `/darkness` | Show plugin and version information. |
| `/darkness help` | Show the in-game help message. |
| `/darkness alerts` | Toggle violation alerts. |
| `/darkness verbose` | Toggle detailed violation output. |
| `/darkness brands` | Toggle client-brand notifications. |
| `/darkness profile <player>` | View a player's anti-cheat profile. |
| `/darkness topvl` | Show players with the highest violation level. |
| `/darkness cps <player>` | View a player's clicks per second. |
| `/darkness checks` | List available checks. |
| `/darkness checks enable <check>` | Enable a check. |
| `/darkness checks disable <check>` | Disable a check. |
| `/darkness debug [player]` | Toggle player debug output. |
| `/darkness consoledebug <player>` | Toggle console debug output for a player. |
| `/darkness perf` or `/darkness performance` | Toggle performance metrics. |
| `/darkness lag` | Show server resource and lag information. |
| `/darkness menu` | Open the main Darkness GUI. |
| `/darkness themes` | Open the themes selection GUI. |
| `/darkness logs [player] [page]` | View violation logs in GUI or inspect a player's logs. |
| `/darkness reload` | Reload the Darkness configuration. |
| `/darkness sendalert <message>` | Send a custom staff alert. |
| `/darkness bot [player]` | Spawn a test bot around yourself or a selected player. |
| `/darkness spectate <player>` | Start spectating a player. |
| `/darkness stopspectating [here]` | Stop spectating; `here` returns you at the current location when permitted. |
| `/darkness knockback <player> [back]` or `/darkness kb <player> [back]` | Run a knockback test. |
| `/darkness totem <player>` | Run the AutoTotem check. |
| `/darkness rotate <player>` | Rotate a player as a staff action. |
| `/darkness shuffle <player> <one/many>` | Shuffle a player's inventory slots. |
| `/darkness punish <player> [reason]` | Apply the configured punishment. |
| `/darkness kick <player> [reason]` | Kick a player. |
| `/darkness crash <player> <method>` | Run a configured client stress-test method. Available suggestions: `entity`, `explosion`, `position`, `particle`, `sound`, `loadworld`, `demo`, `fakelag`, `black`, `multi`, `inverse`, `chunk`. |
| `/darkness trust <add/remove/set/clear/info> <player> [points]` | Manage or inspect a player's TrustFactor score and level. |

### Information Hider

Information Hider is configured through its dedicated configuration file `hider.yml` (bundled as
`hider/en.yml`). It obfuscates packet data sent to clients, preventing cheat modules from gathering
extra information and concealing players behind obstacles on the network level.

| Settings | What they do |
| --- | --- |
| enabled; onlyForPlayers | Enable the module and limit it to player entities. |
| parts.health.value; random | Send a configured health value from 0 to 20, optionally randomised. Value 0 can disrupt client animations and is not recommended for normal gameplay. Set to -1 to disable. |
| parts.xp; air; saturation; absorption | Hide experience, air, food saturation, and absorption golden hearts. |
| parts.ping; gamemode; seed | Mask tab-list ping, game mode, and world-seed data. |
| parts.scoreboard.enabled; patterns | Mask health values in matching scoreboards and the tab list. Patterns identify health-related objectives; this module is disabled when health value is -1. |
| parts.enchant; item_count; item_name; item_drop; durability | Mask enchantments, quantities, names, dropped-item details, and durability. |
| parts.potion; potion_items; end_effect | Hide potion effects and potion types in hand/arrows, and send misleading effect packets to clutter cheat potion lists. |
| parts.server_brand.enabled; brand | Spoof the server brand string visible on Minecraft's F3 debug screen. |
| parts.esp.enabled; max_distance; max_fov; proximity_distance | Conceal players behind walls and obstacles at packet level to defeat ESP, Tracers, and Wallhack. Includes FOV and distance filtering. |
| parts.esp.hide_delay_ticks; interval_ticks; only_sneaking | Tune raytracing calculation intervals, corner-peeking delay, and sneaking-only filtering to optimize server performance. |
| parts.esp.effects; ignored_blocks; disabled_worlds | Customize blindness/darkness/invisibility hiding rules, transparent/passable block exceptions, and disabled worlds. |
| parts.inventory | Shuffle items while moving to disrupt AutoTotem and InvWalk-style automation. |
| parts.chest_loot | Briefly hide container contents when opened to disrupt ChestStealer-style automation. |
| parts.totem.enabled; mode; amount; spawn_tab; height | Disrupt cheat TotemPopCounters via duplicate pop packets or fake bots spawned underground. |
| parts.fake_staff.enabled; only_random_name; roles; bots | Create client-side fake staff members in tab to prevent cheats from tracking real moderators. |
| parts.spectators.allowed_worlds; hide_spectators | Restrict spectator hiding to listed worlds, and hide players with darkness.spectator permission, including those not currently spectating. |

### Support and credits

- Community and support: [Darkness Liquidation Discord](https://dsc.gg/darkness-liquidation)
- Bug reports: include the Darkness version, server software and version, Java version, relevant
  configuration, logs, and clear reproduction steps.

Darkness Liquidation is a fork of the open-source [GrimAC](https://github.com/GrimAnticheat/Grim)
project. Credit and thanks go to the GrimAC contributors for the prediction engine and the work that
made this project possible. Darkness-specific code, checks, tools, and configuration are maintained
by the Darkness Liquidation team.
