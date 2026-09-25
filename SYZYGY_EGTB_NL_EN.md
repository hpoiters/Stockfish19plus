# Stockfish19+ — Syzygy Endgame Tablebases (EGTB’s)

Release 2026-09-25 · Nederlands / English

## Nederlands

### Wat is er veranderd?

Stockfish19+ kan Syzygy EGTB’s zelfstandig gebruiken via de engine-instellingen. De nieuwe **Syzygy Direct**-afhandeling kan bij een volledig gedekte eindspelstelling meteen een zet leveren, zonder eerst de normale zoekloop te doorlopen. Dit is ook bij **zeven stukken, inclusief beide koningen**, in echte Fritz 20 / Playchess Engine Room-partijen vastgesteld.

De enginecode van deze release is gelijk aan de positief beoordeelde RC1-bron. De promotie verandert alleen de verpakking, bestandsnamen, bouwondersteuning en documentatie. De korte interne namen blijven **Stockfish19+ AVX2** en **Stockfish19+ AVX-512**.

### Snel instellen in Fritz 20

1. Laad de geschikte nieuwe `.exe` en open de engine-instellingen.
2. Vink **Use Syzygy** aan. Standaard staat deze optie uit.
3. Vul bij **Syzygy Engine Path** de werkelijke map met je Syzygy EGTB-bestanden in. Voor meerdere Windows-mappen gebruik je puntkomma’s, bijvoorbeeld `H:\Syzygy;J:\Syzygy7`.
4. Laat **SyzygyProbeLimit = 7**, **SyzygyProbeDepth = 1** en **Syzygy50MoveRule = aan** staan voor normaal partijspel met een verzameling tot zeven stukken.
5. Controleer in de engine-uitvoer of de bestanden worden gevonden. De complete verzameling uit de praktijktests werd herkend als **1511 WDL + 1511 DTZ**. Dit aantal is geen vereiste voor kleinere, wel volledig gedekte eindspelen.

Je hoeft Fritz’ eigen EGTB-functie hiervoor niet aan te zetten. Je mag die ook aan laten staan: met beide routes aan is op beide RC-versies geen conflict vastgesteld.

| Engine-instelling | Betekenis | Gewoonlijk instellen |
|---|---|---|
| `Use Syzygy` | Schakelt Stockfish19+’s eigen Syzygy EGTB-gebruik in of uit, inclusief Direct en probes tijdens zoeken. | Aan wanneer je deze functie wilt gebruiken. |
| `Syzygy Engine Path` | Zichtbaar tekstveld voor de Syzygy EGTB-mappen, ook wanneer Fritz de standaardoptie zelf beheert. | Je eigen mappen invullen. |
| `SyzygyPath` | Standaard UCI-pad voor andere schaakprogramma’s. Een niet-leeg `Syzygy Engine Path` heeft voorrang; leegmaken herstelt gebruik van `SyzygyPath`. | In Fritz bij voorkeur het zichtbare `Syzygy Engine Path` gebruiken. |
| `SyzygyProbeLimit` | Bovengrens voor het aantal stukken bij Syzygy EGTB-gebruik; bereik 0–7. Nul schakelt probes uit. | 7 voor een verzameling tot zeven stukken. |
| `SyzygyProbeDepth` | Minimale resterende zoekdiepte voor bepaalde probes tijdens normaal zoeken; bereik 1–100. Dit is geen wachttijd voor Direct. | Standaard 1 laten staan. |
| `Syzygy50MoveRule` | Houdt bij de Syzygy EGTB-beoordeling rekening met de 50-zettenregel. | Aan voor normaal partijspel. |

Syzygy EGTB’s worden niet meegeleverd. Voor Direct zijn de benodigde WDL-bestanden (`.rtbw`) én DTZ-bestanden (`.rtbz`) nodig. Alleen een geldig pad of alleen WDL-dekking garandeert geen Direct-zet.

### Engine Syzygy EGTB en Fritz EGTB zijn afzonderlijke mechanismen

De Fritz-optie **Tablebases gebruiken (GUI)** is hier bij haar eigen schermnaam genoemd. Zij bestuurt Fritz’ eigen EGTB-afhandeling. `Use Syzygy` bestuurt de Syzygy EGTB-afhandeling van Stockfish19+.

| Engine `Use Syzygy` | Fritz EGTB | Wat kun je verwachten? |
|---|---|---|
| UIT | UIT | Geen EGTB-gebruik via deze twee routes. |
| UIT | AAN | Alleen Fritz’ eigen EGTB-route is actief. Dit schakelt Stockfish19+ Direct niet in; bij de zevenstukkenproef bleef de engine normaal rekenen. |
| AAN | UIT | Stockfish19+ gebruikt zelfstandig Syzygy EGTB’s en kan bij volledige dekking Direct spelen. |
| AAN | AAN | Beide routes zijn beschikbaar. Uitgebreid getest met AVX2 en AVX-512; daarbij is geen conflict vastgesteld. |

**Fritz EGTB uitzetten schakelt Engine Syzygy EGTB niet uit**, zolang `Use Syzygy` aan staat, het enginepad geldig is en de vereiste dekking beschikbaar is. Fritz EGTB aanzetten activeert omgekeerd niet de Direct-functie wanneer `Use Syzygy` uit staat.

De Fritz-instelling is dus niet nutteloos: zij kan nog steeds Fritz’ eigen EGTB-informatie en zetafhandeling beïnvloeden. Het precieze GUI-effect hangt af van Fritz en de spelsituatie. De tests tonen samenwerking in de onderzochte configuraties, geen universele garantie voor iedere GUI.

### Syzygy Direct in een partij

Bij volledige dekking van de actuele stelling kan de engine de zet rechtstreeks aan de wortel van de zoekboom bepalen. In de gerapporteerde praktijktests verscheen bijvoorbeeld:

```text
Syzygy Direct: ROOT HIT, 7 pieces
bestmove a7a1
```

De normale zoekloop werd voor die zet overgeslagen. Dit werd herhaaldelijk waargenomen bij verdere stellingen met zeven, zes en vijf stukken. De winst is het vermijden van normale zoektijd in gedekte stellingen; het lezen van bestanden kan nog wel schijftijd kosten. Er wordt geen gemeten Elo-winst geclaimd.

Direct is bedoeld voor gewone zetopdrachten tijdens partijspel. De bevroren RC-code gebruikt deze snelle afhandeling niet tijdens ponder, oneindige analyse (`go infinite`) of een specifieke matzoekopdracht (`go mate`). Daar kan normaal zoeken dus verwacht worden.

### Buiten de dekking

Met acht stukken en Syzygy EGTB’s tot zeven stukken blijft de actuele stelling buiten de Direct-dekking en rekent Stockfish normaal. Tijdens die zoekactie kunnen kleinere eindspelstellingen eventueel al worden geraadpleegd. Zodra de werkelijke bordstelling een volledig gedekte zevenstukkenstelling wordt, kan Direct automatisch overnemen.

Ontbreekt een vereiste EGTB, mislukt een probe of is alleen WDL-informatie beschikbaar, dan blijft normaal Stockfish-zoeken de terugvalroute. Niet elke stelling met hoogstens zeven stukken is automatisch volledig gedekt.

### Geteste versies en eerdere opties

De releasegoedkeuring is gebaseerd op de door de gebruiker gemelde praktijktests van **Stockfish19+ AVX2** en **Stockfish19+ AVX-512** in Fritz 20 / Playchess Engine Room, inclusief lange partijen met zowel Engine Syzygy EGTB als Fritz EGTB aan. De broncontrole voor deze publicatie bevestigt dat de functionele RC-code behouden is. De bouwherkomst van de definitieve binaries staat in `BUILD_PROVENANCE.md`.

Eerdere Stockfish19+-opties blijven behouden: `Slow Mover` (standaard 110), `Minimum Thinking Time` (standaard 0 ms), optioneel `Use ChessDB` en optioneel `Use BIN Book`. Beide boekfuncties staan standaard uit. Dit zijn openingsfuncties, geen Endgame Tablebases (EGTB’s). Een Fritz CTG-boek wordt door Fritz bestuurd. De persoonlijke testwaarde 100 ms voor `Minimum Thinking Time` is geen nieuwe standaard.

## English

### What changed?

Stockfish19+ provides independent **Syzygy Endgame Tablebases (EGTB’s)** access through its engine settings. **Syzygy Direct** can return a move from a fully covered endgame position without entering the normal search loop. Practical Fritz 20 / Playchess Engine Room tests confirmed this with **seven pieces, including both kings**, and subsequently with six and five pieces.

This release retains the approved RC1 engine code. Promotion changes packaging, filenames, build support and documentation only. Internal UCI names remain **Stockfish19+ AVX2** and **Stockfish19+ AVX-512**.

### Quick setup in Fritz 20

1. Load the appropriate new executable and open its engine settings.
2. Enable **Use Syzygy**; its default is off.
3. Enter your actual Syzygy EGTB directories in **Syzygy Engine Path**. Separate multiple Windows directories with semicolons, for example `H:\Syzygy;J:\Syzygy7`.
4. For normal play with EGTB coverage up to seven pieces, keep **SyzygyProbeLimit = 7**, **SyzygyProbeDepth = 1** and **Syzygy50MoveRule enabled**.
5. Check that the engine reports finding the files. The complete collection used in the practical tests contained **1511 WDL + 1511 DTZ** files. Smaller fully covered endgames do not require that entire collection.

Fritz’s own EGTB option is not required. It may remain enabled: tests with both routes enabled found no conflict on either RC build.

| Engine option | Meaning | Normal use |
|---|---|---|
| `Use Syzygy` | Enables the engine’s own Syzygy EGTB access, both Direct and search probes. | Enable to use this feature. |
| `Syzygy Engine Path` | Visible directory field even when Fritz manages the standard UCI path itself. | Enter your EGTB directories. |
| `SyzygyPath` | Standard UCI directory option. A non-empty `Syzygy Engine Path` takes precedence; clearing it restores `SyzygyPath`. | In Fritz, prefer the visible `Syzygy Engine Path`. |
| `SyzygyProbeLimit` | Maximum piece count for Syzygy EGTB probing, from 0 to 7; zero disables probing. | 7 for coverage up to seven pieces. |
| `SyzygyProbeDepth` | Minimum remaining search depth for certain normal-search probes, from 1 to 100; not a Direct delay. | Leave at 1. |
| `Syzygy50MoveRule` | Accounts for the fifty-move rule when assessing Syzygy EGTB results. | Leave enabled for normal games. |

Syzygy EGTB files are not included. Direct requires the necessary WDL (`.rtbw`) and DTZ (`.rtbz`) files; a valid path or WDL-only coverage does not by itself guarantee a Direct move.

### Engine Syzygy EGTB versus Fritz EGTB

Fritz’s **Use Tablebases (GUI)** setting (its interface label) controls Fritz’s own EGTB handling. Stockfish19+’s **Use Syzygy** controls the engine’s separate Syzygy EGTB route.

| Engine `Use Syzygy` | Fritz EGTB | Expected behaviour |
|---|---|---|
| OFF | OFF | Neither of these EGTB routes is active. |
| OFF | ON | Only Fritz’s EGTB mechanism is active. It does not enable Stockfish19+ Direct; normal engine search continued in the seven-piece practical test. |
| ON | OFF | Stockfish19+ independently accesses Syzygy EGTB’s and can play Direct when fully covered. |
| ON | ON | Both mechanisms are available. Extensive AVX2 and AVX-512 tests found no conflict. |

**Disabling Fritz EGTB does not disable engine Syzygy EGTB**, provided `Use Syzygy` remains enabled, the engine path is valid and the required coverage exists. Conversely, enabling Fritz EGTB alone does not activate Stockfish19+ Direct.

Fritz’s setting is not ineffective: it still controls Fritz’s own EGTB information and move handling. The precise GUI effect depends on Fritz and the game situation. The reported tests establish coexistence in those configurations, not a guarantee for every GUI.

### Direct moves and fallback

A fully covered current position can yield `Syzygy Direct: ROOT HIT, 7 pieces`, followed immediately by `bestmove`, without normal search for that move. Repeated hits were reported through seven-, six- and five-piece positions. EGTB file access can still take disk time; no measured Elo gain is claimed.

The frozen RC code intentionally does not take this Direct route during pondering, infinite analysis (`go infinite`) or a mate-search command (`go mate`). Normal search can therefore continue in those modes.

With eight pieces and EGTB’s up to seven pieces, Stockfish searches normally. It may still probe smaller endgame positions reached within its search. When the actual board reaches a fully covered seven-piece position, Direct can take over automatically. Missing required EGTB files, failed probes or WDL-only coverage retain normal Stockfish search as the fallback. Piece count alone does not prove coverage.

### Validation and other options

Release approval is based on the user’s reported practical tests of **Stockfish19+ AVX2** and **Stockfish19+ AVX-512**, including long Fritz 20 / Playchess games with both EGTB routes enabled. Source comparison for publication confirms that functional RC code is unchanged. See `BUILD_PROVENANCE.md` for the final executables’ build provenance.

Earlier options remain: `Slow Mover` (default 110), `Minimum Thinking Time` (default 0 ms), optional `Use ChessDB` and optional `Use BIN Book`. Both book sources default to off; they are opening features, not Endgame Tablebases (EGTB’s). Fritz controls its own CTG book. The personal 100 ms minimum-thinking-time test setting is not a new default.
