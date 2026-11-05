# Tower in Flux

A browser game built on a 3D cellular automaton. A tower grows level by level from simple local rules. Unsupported blocks fall and pile up as rubble, and the wind picks up in random gusts that break pieces off the top. Your job is to get the tower to the target height and keep it standing.

This started as a Grasshopper C# component. The web version keeps the original rules, phases and constants, turns each input into a slider, toggle or button, and adds a challenge mode on top.

**Play:** [ajithva.github.io/tower-in-flux](https://ajithva.github.io/tower-in-flux/)

## How to play

1. Set the **target height**, then tune the grid (width, length, levels per build, seed, density) and the growth rules (birth min/max, survive min).
2. Press **New game**. The tower grows level by level (*Growing*). Unsupported blocks then fall and land as rubble (*Collapsing*), and once it settles the wind starts (*Wind*).
3. While the wind blows, press **Build further** to add more levels on top. The starting level moves to the top of the tower each time it settles.
4. Survive the gusts. Every few seconds the wind gets 10–30% stronger, up to ×3. Each new build halves the extra wind.

### Winning and scoring

**To win:** reach the target height with a settled build at the site's efficiency grade. About 3 seconds after the build settles the tower is checked: still at the target and lean enough means you win. Gusts and earthquakes hit the tower between builds, so it has to survive them on the way up. When you win, a "Successful! You did it." panel lets you keep playing, play again or reset.

Points come from your moves (**New game** or **Build further**) and from winning:

- **−½ per cube** a build places (materials), and **−3 per cube** the wind blows off or a quake shakes loose (both scaled down on plots bigger than 10×10). The score can go below 0.
- **+100 per new level** above your best height so far. Rebuilding lost height scores nothing.
- **+500** the first time a settled build reaches the target.
- **+2,000 for winning**, plus **1,000 per move under the benchmark**. The benchmark is the number of builds a good player should need: target ÷ levels per build, rounded up, + 1 (3 with the default settings).
- Your best score is saved in your browser.

### Wind

The wind gets stronger with height (its push grows with height^0.4, as real wind does). Gusts make it 10–30% stronger every few seconds, up to ×3, and each new build halves the extra wind. Cubes visibly lean with the wind, never more than about a third of a cube, and when one leans too far it snaps off and falls. Rubble piles stay put, but loose blocks on top can still be blown off.

### Sites, efficiency and earthquakes

- **Sites** fix the plot (drawn as a dashed red line on the ground), the target height, the hazards and the efficiency grade you need to win. The tower can't grow past the plot boundary.

  | Site | Plot | Target | Wind | Quakes | Ideal fill | Grade needed |
  |---|---|---|---|---|---|---|
  | Town square | 12×12 | 36 | ×0.85 | 20% | 35% | B |
  | Corner plot | 10×10 | 30 | ×1 | 35% | 45% | C |
  | Harbour wall | 12×12 | 32 | ×1.35 | none | 85% | B |
  | Hilltop mast | 10×10 | 30 | ×1.45 | none | 90% | C |
  | Terrace infill | 16×7 | 28 | ×1 | 35% | 45% | B |
  | Needle site | 9×9 | 40 | ×0.9 | 35% | 50% | B |
  | Fault line | 14×14 | 30 | ×0.7 | 75% | 30% | B |
  | Open plaza | 18×18 | 36 | ×0.9 | 30% | 30% | B |
  | Custom | sliders | slider | ×1 | 35% | 45% | C |

- **Efficiency** is how well the material is used for that site: 100% − ½ × waste − ½ × over-building. *Waste* is the share of all cubes placed that are no longer standing (collapsed after a build, blown off, shaken loose). *Over-building* is how far the tower's fill (cubes ÷ height × plot area) goes past the site's ideal fill. A ≥ 80%, B ≥ 65%, C ≥ 50%, D ≥ 35%, else E. A win needs the site's grade and adds +1,500 / 1,000 / 500 for A / B / C. So a solid tower that wastes little is efficient on the windy Harbour wall, and over-built on the quake-prone Fault line.
- **Earthquakes** replace a share of the gusts (set by the site). Each floor carries the sideways pull of the cubes above it, and stiff towers pick up more of the shaking, so dense towers that resist wind suffer most.
- **Visible sway:** unstable towers rock as a whole in the wind (Shaky the most), and quakes shake them.
- **Winning:** a settled build reaches the target at the site's grade, is still standing about 3 seconds later, and the tower has survived at least 3 gusts or quakes during the game.
- The Stability and Efficiency meters wait until the tower is at least 6 levels and a quarter of the target tall.

### Stability

The tower is treated as a simplified structure, so there's more than one way to make it stand:

- **Where the cubes are, not how many:** each floor's stiffness is its second moment of area along the wind, so cubes far from the middle count most and a hollow tube can be as stiff as a solid block.
- **Connections in 3D:** a cube is braced by its neighbours on the same floor, the cubes directly above and below, and those diagonally above and below, so well-connected open frames are stiff even when mostly empty.
- **Tapering:** a tower that narrows evenly as it rises sways up to 35% less.
- **Setbacks and podiums:** where a floor shrinks to under 60% of the one below, the part above bends from there (plus 35% of the podium's height), so a slim tower on a wide base can stand.
- **Cores:** cubes on an unbroken column from the ground sway 40% less.
- **Porosity:** an open floor catches as little as 20% of the wind of a solid one.
- **Bending adds up from the top down:** top-heavy floors and thin necks hurt, and a cube with nothing directly beneath it is 30% weaker.
- **Erosion and wear:** when edge cubes snap, the cubes behind them are exposed next; cubes pushed past 85% of their strength slowly lose up to half of it.

The **Stability** meter plays out a gust on a copy of the tower and reports how many levels it would lose: **Solid** loses none even at the strongest wind (×3), **Steady** loses levels only at ×3, **Shaky** already at ×1.5. It also names the shape it sees: tapered, setback, tube, lattice, spine or top-heavy. All the constants are near the top of the script.

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
| Site, Target height, Random gusts, Earthquakes, Speed, Voxel gaps | new in the web version |
