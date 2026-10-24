Act as an expert WebGL / Three.js developer and machine learning engineer. I am providing you with a single-file, zero-dependency HTML/JS web application (`index-v001.html`) that serves as a browser-based trainer and graph workbench for an ESP32-S3 vision CNN.

### Context & Goal
Visualization without agency is just an expensive dashboard. We want to transform Section 4 ("Graph Workbench") from a passive network viewer into a **tactile, circuit-board-style control panel**. 

By leveraging the modularity of the 4 model types (Model A dense, Model B isolated islands, Model C designed bridges, Model D learned bridges), the user or an AI agent must be able to perform **direct topological surgery, gradient masking, and manual bridge weight tuning** to steer model fine-tuning.

---

### Core Enhancements Required

#### 1. Interactive Topological "Circuit Board" Surgery (3D Canvas & State Integration)
* **Add & Sever Bridges in Real Time:**
  * **Click-to-Sever:** Clicking an active 3D bridge conduit (a cross-island connection) immediately zeros its weights, sets its edge mask to 0 in `net.edge`, and marks it as severed.
  * **Drag-to-Connect:** Allow the user to drag a 3D connection line from any Conv1 channel in Island $A$ to a Conv2 filter in Island $B$. This dynamically creates a new bridge (instantiating an active $3 \times 3$ kernel slot in the topology, updating `net.edge` and `myConnMask.h`).
* **Visual Edge States:**
  * Active within-island edges: Solid color matching the island palette.
  * Active bridges: Glowing spatial conduits/arcs with visible thickness proportional to `mean |weight|`.
  * Manually disabled/severed edges: Rendered as thin gray dashed lines with a red $\times$ indicator.

#### 2. Localized "Surgical" Fine-Tuning & Gradient Masking
* **Island & Subgraph Isolation Controls:**
  * Add UI/3D controls to **Freeze / Unfreeze** individual islands or layers. 
  * When an island is frozen, set a gradient mask during backpropagation (`backward()`) so its weights receive zero updates, allowing fine-tuning to occur *only* on active bridges or specific unfrozen modules.
* **Preserving Feature Extractors:**
  * Allow setting higher learning rates for newly created bridges while keeping the underlying island feature extractors strictly locked (preventing catastrophic forgetting).

#### 3. Manual Bridge "Mixing Valves" (Gain Controls)
* **Tactile Bridge Tuning Drawer:**
  * When a bridge edge is clicked in the 3D scene, open a side control panel displaying its $3 \times 3$ kernel matrix alongside a **Gain Slider** (e.g., $0.0\times$ to $3.0\times$).
  * Adjusting the slider scales all 9 weights of that specific bridge kernel in real time (`net.w.c2w`), acting as an explicit "volume knob" for inter-island communication.
  * Include a **1-Click He-Reinit** button for that specific kernel to reset an underperforming bridge without affecting the rest of the network.

#### 4. Live Feedback Loop & Real-Time Re-evaluation
* **Instant Accuracy & Loss Delta:**
  * When a bridge is severed, added, or scaled, automatically re-run inference on the validation set in the background (or display a "Re-evaluate" shortcut button) showing the instant accuracy delta ($\Delta\text{Acc}$).
* **Dynamic Signal Flow Visualization:**
  * Maintain real-time particle pulses flowing along edges during live prediction or webcam capture. Severed edges immediately stop carrying signal pulses.

---

### Technical & Architectural Constraints
1. **Single-File Architecture:** Keep the entire application inside `index-v001.html`. Load Three.js and OrbitControls via CDN (`<script src="...">`).
2. **Preserve Hardware Export Compatibility:**
   * All structural edits (added or deleted bridges) MUST automatically sync with binary export functions (`myWeights.bin`), mask header generation (`myConnMask.h`), and configuration metadata (`graph-config.json` / `config.json`).
3. **Coding Standards:**
   * Use clean `camelCase` function and variable names starting with `my` (e.g., `my3dScene`, `mySeverBridge`, `myApplyGradientMask`, `myBridgeGainSlider`).
   * Prefer explicit `onclick` bindings and keep CSS clean inside the existing `<style>` block.

---

### Implementation Instructions
Refactor Section 4 of `index-v001.html` to integrate this Three.js interactive 3D circuit board and its companion fine-tuning control drawer. Ensure that forward pass, backward pass, gradient updates, and export buffers accurately reflect manual topological edits and gradient masks.
