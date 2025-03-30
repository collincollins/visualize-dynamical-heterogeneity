# interactive dynamical heterogeneity visualizer

## goal

an interactive, web-based tool for visualizing **dynamical heterogeneity**, a characteristic in liquids approaching the glass transition where fast and slow particles cluster into spatial regions that exchange over time.

## features

*   **interactive simulation:** control effective "temperature" and simulation speed (fps).
*   **heterogeneity visualization:** see the formation and evolution of slow (blue) and fast (red) particle clusters.
*   **particle highlighting:** fastest 5% and slowest 5% of particles are visually emphasized (larger, fully opaque).
*   **contextual view:** middle 90% of particles are shown smaller and dimmer (can be hidden).
*   **3d viewport:** rendered using Three.js.
*   **camera controls:** Standard OrbitControls (drag, zoom, pan) and subtle auto-motion.

## simulation model

the simulation uses a simplified **3d grid-based cellular automaton** model, not computationally expensive methods like molecular dynamics.

*   **representation:** a cubic grid (`gridSize` x `gridSize` x `gridSize`). each site $(i,j,k)$ represents a "particle".
*   **state:** each particle has a continuous mobility state $M_{i,j,k}(t)$ (approx. 0=slow to 1=fast).
*   **boundaries:** periodic boundary conditions (pbc).
*   **initialization:** random initial mobilities; slight random position displacements for visual amorphousness.

### math model

the mobility $M_{i,j,k}(t+1)$ is updated based on current state and neighbors' average state:

1.  **neighbor average:** calculate $\bar{M}_{neighbors}(t)$ of the 6 nearest neighbors (using pbs).

    ![Neighbor Average equation](https://latex.codecogs.com/png.latex?\color{white}\bar{M}_{neighbors}(t)=\frac{1}{6}\sum_{n}M_{n}(t))

2.  **update rule:** new mobility $M'$ is a weighted average plus noise:

    ![Update Rule equation](https://latex.codecogs.com/png.latex?\color{white}M'_{i,j,k}(t+1)=(1-C)M_{i,j,k}(t)+C\bar{M}_{neighbors}(t)+N(t))

    *   $C$: `correlationStrength` (neighbor influence).
    *   $N(t)$: uniform noise in $[-N, N]$, where $N$ is `noiseLevel`.

3.  **clamping:** result $M'$ is clamped strictly within $(\epsilon, 1 - \epsilon)$ (with $\epsilon = 10^{-6}$ ).

    ![Clamping equation](https://latex.codecogs.com/png.latex?\color{white}M_{i,j,k}(t+1)=\max(\epsilon,\min(1-\epsilon,M'_{i,j,k}(t+1))))

4.  **temperature influence:** The "temperature" slider $T$ controls $C$ and $N$:
    *   `correlationStrength` $C = 0.95 / (1 + T \times 2.5) + 0.04$ (decreases with $T$)
    *   `noiseLevel` $N = 0.001 + T \times 0.04$ (increases with $T$)
    lower $T$ implies high correlation and low noise (promoting clusters); higher $T$ implies low correlation and high noise (more homogeneous/rapid fluctuations).

## visualization

*   **rendering:** Three.js `WebGLRenderer` with `MeshStandardMaterial` spheres.
*   **colormap:** mobility mapped via coolwarm (blue=slow, white=medium, red=fast).
*   **normalization:** color scale dynamically normalized to current min/max mobility.
*   **highlighting:** fastest/slowest 5% are larger and opaque; middle 90% are smaller, dimmer, and toggleable.

## controls

*   **temp slider:** adjusts $T$, influencing $C$ and $N$.
*   **sim fps input:** target simulation steps per second.
*   **pause/play button:** toggles simulation steps.
*   **reset button:** re-initializes mobilities and visuals.
*   **hide/show average button:** toggles visibility of middle 90% particles.
*   **mouse/touch:** OrbitControls (rotate, zoom, pan).

## limitations

this is a **visualization tool**, not a physically accurate simulation.

*   **simplified model:** a grid-based cellular automaton, not particle dynamics based on physical forces.
*   **scalar state:** mobility is a single scalar, ignoring real system complexities (position, velocity, interactions).
*   **ad-hoc dynamics:** the update rule is chosen phenomenologically to produce heterogeneity.
*   **"temperature" mapping:** the slider directly controls model parameters $C$ and $N$, not a thermodynamic temperature.
*   **grid structure:** underlying topology is a fixed grid, despite visual amorphousness.
*   **discrete time:** simulation uses discrete time steps.

## tech

*   HTML5, CSS3, JavaScript (ES modules)
*   Three.js
*   neobrutalism.css stylesheet from [NeoBrutalism](https://github.com/zAlweNy26/neobrutalism.css/tree/main)

## how to run

1.  use a modern web browser supporting ES modules.
2.  open `heterogeneity_sim.html` directly.
3.  no build step or server needed.
