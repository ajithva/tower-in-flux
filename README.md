# Tower in Flux

A browser game built on a 3D cellular automaton. A tower grows level by level from simple local rules. Unsupported blocks fall and pile up as rubble, and the wind picks up in random gusts that break pieces off the top. Your job is to get the tower to the target height and keep it standing.

This started as a Grasshopper C# component. The web version keeps the original rules, phases and constants, turns each input into a slider, toggle or button, and adds a challenge mode on top.

**Play:** `https://<your-username>.github.io/tower-in-flux/`

## How to play

1. Set the **target height**, then tune the grid (width, length, levels per build, seed, density) and the growth rules (birth min/max, survive min).
2. Press **New game**. The tower grows level by level (*Growing*). Unsupported blocks then fall and land as rubble (*Collapsing*), and once it settles the wind starts (*Wind*).
3. While the wind blows, press **Continue growing** to add more levels on top. The starting level moves to the top of the tower each time it settles.
4. Survive the gusts. Every few seconds the wind gets 10–40% stronger, up to ×4.

### Winning and scoring

**To win:** reach the target height with a settled build, then hold it through **3 gusts in a row** without building. If a gust knocks the tower below the target, the count resets. When you win, a "Successful! You did it." panel lets you keep playing, play again or reset.

Points come only from your moves (**New game** or **Continue growing**) and from winning:

- **+100 per new level** above your best height so far. Rebuilding lost height scores nothing.
- **+500** the first time a settled build reaches the target.
- **+2,000 for winning**, plus **1,000 per move under par**. Par is target ÷ levels per build, rounded up, + 1.
- Your best score is saved in your browser.

### Wind

Gusts make the wind 10–40% stronger every few seconds, up to ×4. Cubes visibly lean with the wind, never more than about a third of a cube, and when one leans too far it snaps off and falls. Rubble piles stay put, but loose blocks on top can still be blown off.

### Colours

Tower is **cyan**, falling blocks are **amber** and rubble is **green**, which correspond to outputs A, B and C of the Grasshopper component. The axis icon in the bottom-right corner (X red, Y green, Z blue) turns with the camera.

### Lighting

Choose **Day**, **Night** or **Cycle** under *Lighting & display*. In Cycle the sun crosses the sky, the moon and stars come out, and after dusk random cubes on the tower's sides light up like windows and flicker on and off. The shadows are soft, and **Glow** adds a bloom effect to the lit windows. **Day length** sets how long a full day takes. The lighting is visual only and doesn't change the simulation.

## Controls and their Grasshopper inputs

| Control | Grasshopper input |
|---|---|
| New game / Reset / Clear rubble / Continue growing | `startGen`, `restart`, `clearRubble`, `continueGen` |
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
