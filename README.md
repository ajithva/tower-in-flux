# Tower in Flux

A browser game built on a 3D cellular automaton. A tower grows level by level from simple local rules. Unsupported blocks fall and pile up as rubble, and the wind picks up in random gusts that break pieces off the top. Your job is to get the tower to the target height and keep it standing.

This started as a Grasshopper C# component. The web version keeps the original rules, phases and constants, turns each input into a slider, toggle or button, and adds a challenge mode on top.

**Play:** `https://<your-username>.github.io/tower-in-flux/`

## How to play

1. Set the **target height**, then tune the grid (width, length, levels per build, seed, density) and the growth rules (birth min/max, survive min).
2. Press **New game**. The tower grows level by level (*Growing*). Unsupported blocks then fall and land as rubble (*Collapsing*), and once it settles the wind starts (*Wind*).
3. While the wind blows, press **Build further** to add more levels on top. The starting level moves to the top of the tower each time it settles.
4. Survive the gusts. Every few seconds the wind gets 10–30% stronger, up to ×3. Each new build halves the extra wind.

### Winning and scoring

**To win:** reach the target height with a settled build, then hold it through **3 gusts in a row** without building. If a gust knocks the tower below the target, the count resets. When you win, a "Successful! You did it." panel lets you keep playing, play again or reset.

Points come only from your moves (**New game** or **Build further**) and from winning:

- **+100 per new level** above your best height so far. Rebuilding lost height scores nothing.
- **+500** the first time a settled build reaches the target.
- **+2,000 for winning**, plus **1,000 per move under the benchmark**. The benchmark is the number of builds a good player should need: target ÷ levels per build, rounded up, + 1 (3 with the default settings).
- Your best score is saved in your browser.

### Wind

The wind gets stronger with height (its push grows with height^0.4, as real wind does). Gusts make it 10–30% stronger every few seconds, up to ×3, and each new build halves the extra wind. Cubes visibly lean with the wind, never more than about a third of a cube, and when one leans too far it snaps off and falls. Rubble piles stay put, but loose blocks on top can still be blown off.

### Stability

The tower is treated as a simplified cantilever, so different shapes can win:

- **Stiffness comes from where the cubes are.** Each floor's resistance to bending is its second moment of area along the wind: cubes far from the middle count most, like a tube skyscraper's frame. A hollow ring can be as stiff as a solid block.
- **Wind load** on a floor = width facing the wind × how solid the floor is (gaps let wind through) × the wind strength at that height.
- **Bending adds up from the top down.** Each floor carries the wind on everything above it, so wide bases and tapered tops help, and thin necks and top-heavy floors hurt.
- **Bracing:** a cube resting on another cube is braced by it (it counts as an extra neighbour), so open but well-connected lattices aren't brittle.
- **Spines:** cubes on an unbroken column from the ground sway 25% less.
- **Erosion:** when edge cubes snap, the cubes behind them are exposed next, so a tower erodes from the outside in.
- **Overhangs:** a cube with nothing directly beneath it is 30% weaker.
- **Fatigue:** exposed cubes slowly lose up to half their strength under repeated gusts. Cubes boxed in on six or more sides, and every cube in a Solid tower, don't wear out.

The **Stability** meter plays out a gust on a copy of the tower (edge cubes snapping, the next layer being exposed, unsupported cubes falling) and reports how many levels it would lose. **Solid** loses none even at the strongest wind (×3), **Steady** loses levels only at ×3, and **Shaky** already loses levels at ×1.5. A number like **−4** is the levels a ×3 gust would erode (or a ×1.5 gust for a Shaky tower). It also names the shape it sees: tapered, tube, lattice, spine or top-heavy. The prediction runs every 1.5 seconds while the wind blows. All the constants are near the top of the script.

In 36 simulated games across three growth rule sets, two densities, three widths and two build sizes, about 56% were won. Width mattered most (8 wide: none, 16 wide: all), every rule set won about half its games, and the labels predicted the outcome: every Steady and Solid tower held, and only a third of Shaky ones did.

### Colours

Tower is **cyan**, falling blocks are **amber** and rubble is **green**, which correspond to outputs A, B and C of the Grasshopper component. The axis icon in the bottom-right corner (X red, Y green, Z blue) turns with the camera.

### Lighting

Choose **Day**, **Night** or **Cycle** under *Lighting & display*. In Cycle the sun crosses the sky, the moon and stars come out, and after dusk random cubes on the tower's sides light up like windows (never the roof or the ground floor) and flicker on and off. The shadows are soft, and **Glow** adds a bloom effect to the lit windows. **Day length** sets how long a full day takes. The lighting is visual only and doesn't change the simulation.

## Controls and their Grasshopper inputs

| Control | Grasshopper input |
|---|---|
| New game / Reset / Clear rubble / Build further | `startGen`, `restart`, `clearRubble`, `continueGen` |
| Width, Length, Levels per build, Seed, Density | `width`, `length`, `levels`, `seed`, `density` |
| Birth min/max, Survive min, Pure birth | `birthMin`, `birthMax`, `surviveMin`, `pureBirth` |
| Build on rubble, Stop wind, Wind along Y | `useRubble`, `stopWind`, `windY` |
| Continue from level | `continueFromLevel` |
| Taper | `taper` |
| Target height, Random gusts, Speed, Voxel gaps | new in the web version |

## Differences from the Grasshopper version

- **Random numbers:** .NET's `System.Random` has been replaced with a seeded JavaScript generator. The same seed always gives the same starting level, but it won't match the Rhino output exactly.
- **Wind strength:** it is multiplied by the gust factor.
- **Rubble spread:** how far rubble can spread (the adaptive extent) is capped so the browser stays responsive.
- **Grid size:** width, length and levels per build go up to 30.

## Running locally

There is no build step. The page is a single HTML file that loads [three.js](https://threejs.org) from the jsDelivr CDN. Serve the folder rather than opening the file directly:

```bash
python3 -m http.server 8000   # then open http://localhost:8000
```
