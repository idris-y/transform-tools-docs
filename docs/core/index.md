# Transform Tools (Core Version) - Description and Documentation

---

## Description

### Transform Tools: Advanced Gizmo Control & Precision Transformations

Transform Tools enhances object manipulation in Blender using **custom 3D gizmos** for precise transformation control. It works closely with Blender's core tools.

#### Core Features:

*   **Manual Gizmo Creation:** Interactively define transformation origins and targets with precision using Blender's 3D cursor.
*   **Core Transformation:** Apply a full transformation (location, rotation, scale) between two defined gizmo states.
*   **Gizmo Management:** Undo/redo gizmo states and control their visibility in the viewport.
*   **Advanced Options:** Includes options to flip the transformation axis and reverse the operation.

#### Workflow:

The addon operates by **defining the 'Previous' (start) and 'Active' (end) gizmo states, then applying transformations** based on these states. This system facilitates precise pose-to-pose adjustments.

---

## User Documentation

### Overview

Transform Tools provides enhanced transformation capabilities in Blender using custom 3D gizmos. It allows for precise control over moving, rotating, and scaling objects and mesh components.

**Core Workflow:** The fundamental process involves:

1.  **Defining** the 'Previous' gizmo (representing the start state).
2.  **Defining** the 'Active' gizmo (representing the end state).
3.  Selecting the elements to modify.
4.  **Applying** the `Transform` operation from the Operations panel.

### Installation

1.  Go to `Edit > Preferences > Add-ons`.
2.  Click `Install` and select the downloaded addon `.zip` file.
3.  Find "Transform Tools" in the list and enable the checkbox.
4.  The panel will appear in the 3D View's Sidebar (N-Panel) under the "XForm Tools" tab.

---

### Working with the 3D Cursor and Snapping

The **3D Cursor** plays a vital role in Transform Tools operations, especially for interactive gizmo creation.

**Positioning the 3D Cursor:** Accurate placement is crucial. You can position it using:

*   **Direct Placement:** `Shift + Right Mouse Button` places the cursor on the surface under the mouse.
*   **`Shift + S` Pie Menu:** Offers various snapping options like `Cursor to Selected`.
*   **Sidebar (N-Panel):** In the `View` tab, you can numerically edit the `3D Cursor` Location and Rotation.

**Using Blender's Snapping:** For precise placement when creating a gizmo:

1.  **Enable Snapping:** Click the **Magnet Icon** in the 3D Viewport header or press `Shift + Tab`.
2.  **Choose Snap Target (`Snap To`):** Select what to snap to (e.g., `Vertex`, `Edge`, `Face Center`).

*Mastering 3D Cursor placement and Blender's snapping system significantly enhances the precision you can achieve with this addon.*

---

### Panel Sections

#### 1. Operations Section

This section contains all the core tools for creating gizmos and executing the transformation.

*   <span id="op-create" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Create</span>: Interactively define the Active gizmo by clicking its origin, X-axis, and Y-axis endpoints in the 3D view.
    *   *How it works:* This process uses the 3D Cursor system to determine where your clicks land in 3D space. It respects Blender's current header snapping settings.
    *   *Cancellation:* You can cancel the placement at any step using `ESC` or `Right Mouse Button`.

*   <span id="op-undo-redo">![Undo Icon](../assets/icons/Undo.png) ![Redo Icon](../assets/icons/Redo.png) **Undo/Redo Gizmos**</span>: Undo or redo changes made to the gizmo states themselves.

*   <span id="opt-visibility">![Gizmos Visibility All Icon](../assets/icons/Show_all_gizmos.png) **Gizmos Visibility**</span>: Cycles through the visibility states for the Previous and Active gizmos (Show Both, Show Active Only, Hide All).

*   <span id="op-transform" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Transform</span>: Performs a full location, rotation, and uniform scale transformation on the selected elements, moving them from the Previous gizmo's state to the Active gizmo's state.
    *   *Requirement:* This button is disabled until both the Previous and Active gizmos have been defined.

---

#### 2. Options Section

Modify the behavior of the `Transform` operation.

*   <span id="opt-scale">**<code>Scale</code>**</span>: Include the uniform scale factor difference between the gizmos in the transformation calculation. If unchecked, only location and rotation differences are applied.

*   <span id="opt-flip">**<code>Flip</code>**</span>: Rotates the Previous gizmo 180 degrees around its Y-axis before applying the transformation.

*   <span id="opt-interpolation-value">**<code>Interpolation Value</code>**</span>: Controls the progression of the transformation.
    *   **`1.0`**: The default, full transformation to the Active gizmo.
    *   **`0.0`**: No transformation is applied.
    *   **`0.5`**: A transformation halfway between the Previous and Active states.
    *   **`-1.0`**: Reverses the transformation, effectively swapping the gizmos for the operation.

---

#### 3. Redo Last Menu (F9 Panel)

After performing the `Transform` operation, you can press `F9` to open the Redo Last menu. This allows you to interactively fine-tune the result by changing the options (like `Scale`, `Flip`, and `Interpolation Value`) without having to undo and redo the entire process.

---

### Upgrade to Lite or Pro

Enjoying the Core version? Discover more powerful features to accelerate your workflow:

*   **Lite Version:** Adds automatic gizmo creation from selections (`Get from Selected`), alignment tools, and non-uniform scaling.
*   **Pro Version:** Unlocks advanced workflows like Multi-Transform, Constrained Transforms, duplication, extrusion, and much more.

[**Click here to learn more about the full feature set!**](https://idris-y.github.io/transform-tools-docs)
