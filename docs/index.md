# Transform Tools Addon

---

## Description

### Transform Tools: Advanced Gizmo Control & Precision Transformations

Transform Tools enhances object manipulation in Blender using **custom 3D gizmos** for precise transformation control. It works closely with Blender's core tools.

#### Core Features:

*   **Flexible Gizmo Definition:** Define transformation origins and targets accurately. Create gizmos automatically from selections, interactively using Blender's 3D cursor, or update them based on existing elements.
*   **Dual-Gizmo Transformations:** Apply a transformation between the two gizmo states, using modes for full transform (location, rotation, scale), specific axis alignment, or location only.
*   **Independent Custom Orientation:** Use the Active gizmo as a custom transform orientation for standard Blender transform tools like Move, Rotate, Scale, etc.
*   **Constrained Transforms:** Execute transformations where selections move or rotate from the 3D Cursor position along a specified direction towards a calculated intersection point on a target line, plane, sphere, or center derived from the gizmo setup.
*   **Advanced Options:** Includes interpolation, duplication, edit-mode extrusion, axis flipping, and count repetition.
*   **Gizmo Management:** Provides tools to swap, copy/paste, undo/redo gizmo states, and control visibility.

#### Workflow:

The addon operates by **defining the 'Previous' (start) and 'Active' (end) gizmo states, then applying transformations** based on these states. This system facilitates precise pose-to-pose adjustments and complex constrained operations. It integrates with Blender's 3D Cursor for placement and initiating constrained actions.
<div style="display: flex; justify-content: center;">
  <video muted autoplay loop playsinline
         style="width: 800px;
                height: 400px;
                border-radius: 16px;
                object-fit: none;
                image-rendering: -webkit-optimize-contrast;
                display: block;">
    <source src="assets/videos/create_transform.webm" type="video/webm">
  </video>
</div>
---

## User Documentation

### Overview

Transform Tools provides enhanced transformation capabilities in Blender using custom 3D gizmos and dedicated operators. It allows for precise control over moving, rotating, and scaling objects and mesh components. The addon includes methods for:

*   Transforming elements between two explicitly defined transform states using [dual gizmos](#transformation-section).
*   Using a single gizmo as a [custom orientation](#op-cursor-pivot) for standard Blender transform tools.
*   Executing [constrained transformations](#constrained-transforms-section), where elements move or rotate from the 3D cursor position towards a computed intersection point with target geometry (a point, line, plane, or sphere) derived from the gizmos.

**Core Workflow:** The fundamental process involves:

1.  **Defining** the 'Previous' gizmo (representing the start state).
2.  **Defining** the 'Active' gizmo (representing the end state).
3.  Selecting the elements to modify.
4.  **Applying** a transformation operation that uses the defined gizmos (like [<code>Transform</code>](#op-transform), [<code>Align</code>](#op-align-xyz), [<code>Move</code>](#op-move), or [Constrained Transforms](#constrained-transforms-section)).

**Key Concepts:**

*   Gizmos definition sources: [selections](#op-get-from-selected), [interactive clicking](#op-create), [3D cursor state](#op-get-from-selected).
*   Active gizmo can serve as a custom orientation for standard Blender tools via the [<code>Cursor Pivot</code>](#op-cursor-pivot) operator.
*   Utilizes Blender's 3D Cursor for interactive placement and as the origin for [Constrained Transforms](#constrained-transforms-section).

### Installation

1.  Go to `Edit > Preferences > Add-ons`.
2.  Click `Install` and select the downloaded addon `.zip` file.
3.  Find "Transform Tools" in the list and enable the checkbox.
4.  The panel will appear in the 3D View's Sidebar (N-Panel) under the "TTools" tab, aka "Transform Tools".

#### IMPORTANT NOTE: Upgrading or Installing Multiple Versions

*   Please be aware that running multiple versions of the Transform Tools addon (e.g., having both "Full" and "Core" enabled) at the same time is not recommended and may cause conflicts.

*   The recommended procedure for a clean upgrade is to first **disable and REMOVE** the existing version from Blender's Add-on Preferences. **Restarting Blender** before installing the new version is also advised to ensure all modules are fully unloaded.

---

### <span id="sec-cursor-snapping">Working with the 3D Cursor and Snapping</span>

The **3D Cursor** plays a vital role in several Transform Tools operations:

*   **Interactive Gizmo Creation ([`Create`](#op-create)):** Click locations are interpreted via the 3D Cursor system to determine positions in 3D space.
*   **Gizmo Definition ([`Get from selected`](#op-get-from-selected)):** When nothing is selected, the Active gizmo is defined using the 3D Cursor's current transform.
*   **[Constrained Transforms (`Move to`, `Rotate to`, `Scale to`)](#constrained-transforms-section):** The 3D Cursor's location (and optionally its orientation) acts as the *starting point* for these transformations.

**Positioning the 3D Cursor:** Accurate placement is often crucial. You can position it using:

*   **Direct Placement:** `Shift + Right Mouse Button` places the cursor on the surface under the mouse.
*   **`Shift + S` Pie Menu:** Offers various snapping options like `Cursor to Selected`, `Cursor to World Origin`, `Cursor to Grid`.
*   **Sidebar (N-Panel):** In the `View` tab, you can numerically edit the `3D Cursor` Location and Rotation.

**Using Blender's Snapping:** For precise placement (especially during interactive [`Create`](#op-create) or when positioning the cursor before other operations):

1.  **Enable Snapping:** Click the **Magnet Icon** in the 3D Viewport header or press `Shift + Tab`.
2.  **Choose Snap Target (`Snap To`):** Select what to snap to (e.g., `Vertex`, `Edge`, `Face Center`, `Increment`).
3.  **Choose Snap Element (`Snap With`):** Determine which part snaps (e.g., `Closest`, `Center`, `Active`).

*Mastering 3D Cursor placement and Blender's snapping system significantly enhances the precision you can achieve with this addon.*

---

### Panel Sections

#### <span id="gizmo-section">1. Gizmo Section</span>
*   <span id="op-create-and-transform" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Create & Transform</span>: An Combined workflow operator that combines the definition of both gizmos with the final transformation step into a single process.

    *   **How it Works (A Step-by-Step Guide):**
        1.  **Define Start State:** The operator first prompts you to interactively [`Create`](#op-create) the first gizmo.
        2.  **Define End State:** Immediately after, it prompts you to [`Create`](#op-create) the second gizmo.
        3.  **Check Selection & Transform:** After both gizmos are defined, the operator's behavior depends on whether you have anything selected:

    *   **If a valid selection exists:**
        *   A pie menu immediately appears, offering all seven standard transformation operations (`Transform`, `Move`, `Rotate`, `Scale`, `Align X/Y/Z`).
        *   Selecting an operation from the pie menu executes it instantly, completing the workflow.

    *   **If nothing is selected:**
        *   A message appears in the Blender status bar: `Make a selection & press ENTER or Right-click to continue (or ESC to stop)`
        *   The operator then waits for your input:
            *   **Select & Continue:** You can now select the desired elements in the viewport and then press `Enter` or `Right-Click` to display the transformation pie menu.
            *   **Cancel:** Pressing `ESC` will cancel this final transformation step. The pie menu will not appear, but the two gizmos you defined will remain.

    *   **Purpose:** This operator is designed to speed up "pose-to-pose" workflows by removing the need to return to the main panel between steps.

*   <span id="op-create" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Create</span>: Interactively define the Active gizmo by clicking its origin, X-axis, and Y-axis endpoints in the 3D view.
    *   *How it works:* This process uses the 3D Cursor system to determine where your clicks land in 3D space. The behavior depends on the [<code>Snap</code>](#opt-snap) toggle below: if active, it uses the addon's specific snapping settings; if inactive, it respects Blender's current header snapping settings.
    *   *Cancellation & Validation:*
        *   You can cancel the placement of the origin or axes using `ESC` or `Right Mouse Button`.
        *   If you cancel placing the **origin**, its position reverts based on the setting below.
        *   If you cancel placing an **axis** endpoint (X or Y), or if the manually placed axis is invalid (too short or aligned with a previous axis), that axis will be **automatically created** using a reference orientation determined by the setting below.
        *   This fallback ensures a valid gizmo is always created.
    *   *Reference State for Cancellation/Auto-Creation:* The specific state used for origin cancellation revert and the orientation used for automatic axis creation depends on the [**<code>3D Cursor Orientation</code>**](#opt-cursor-orientation) setting (see description below).
    *   *Final Cursor Update:* This operation always updates the 3D cursor's location and rotation upon successful completion, regardless of other settings.
    *   ***Modifier Keys (Instant Gizmo Snapping):*** Instead of going through the full creation process, you can use modifier keys to instantly snap parts of the **Active Gizmo** to the 3D Cursor. These modifications are applied immediately without updating the Previous gizmo (even if 'Auto-Update' is enabled):
        *   **Shift + Click:** Move the **X-axis** (Red Circle) endpoint to the 3D Cursor location.
        *   **Ctrl + Click:** Align the **Y-axis** (Green Circle) to point towards the 3D Cursor location.
        *   **Alt + Click:** Move the **whole Gizmo** (origin + orientation) to the 3D Cursor location and orientation.

*   <span id="op-get-from-selected" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Get from selected</span>: Creates/updates gizmo states based on the current selection. The behavior is highly context-aware.

    *   **Default Behavior:** Smart-detects context (Average, Dual, or 3D Cursor). Edit Mode prioritizes Face > Edge > Vertex.
    *   **Object Mode (1 or >2 objects):** Defines the Active gizmo from the active object or the average of all selected.
    *   **Object Mode (Exactly 2 objects):** Defines Previous gizmo from the non-active and Active gizmo from the active selected object.
    *   **Edit Mode (Mesh):** The operator respects the current selection mode (Vertex, Edge, or Face) to interpret your selection.
        *   **Face:** Uses the face's normal (Z-axis), first edge (X/Y-axis), center, and area (uniform scale).
        *   **Edge:** Uses the edge's direction (X-axis), connected face normals (Z-axis), center, and length (uniform scale).
        *   **Vertex:** The following hierarchy is used to create a stable orientation:
            *   **Z-axis:** Determined by this priority: 1) Average normal of all connected faces. 2) If no faces, a normal is calculated from all connected edges. 3) If no connected edges, the object's local Z-axis is used.
            *   **X-axis:** Aligned with the direction of the first connected edge.
            *   **Scale:** Calculated from the average length of all connected edges.
        *   **Multiple Selected Elements (e.g., several faces):** The operator first calculates an individual gizmo for *each* selected element. It then computes the **average** of all these individual gizmos (average location, rotation, and scale) to create a single, final **Active Gizmo**.
    *   **Edit Mode (Curve/Armature):** Defines the Active gizmo using the location/rotation of selected points/bones.
    *   **Nothing Selected:** Defines the Active gizmo from the 3D Cursor's current transform.
    *   **Multi-Object Edit Mode:** When editing multiple mesh objects simultaneously, the operator treats all selected elements as a single group.
    *   ***Modifier Keys:***
        *   **Shift + Click:** Get from **3D Cursor** only (ignores selection).
        *   **Alt + Click:** Set **Previous Gizmo** only (instead of Active).
        *   **Ctrl + Click:** Force **Average** from selection (bypasses smart-detection for dual-object case).

*   <span id="opt-auto-update">![Auto-Update Gizmos Icon](assets/icons/Auto-Update_Gizmos.png) **Auto-Update Gizmos**</span>: If enabled, whenever the **Active Gizmo** is defined or updated (e.g., via [`Create`](#op-create), [`Get from selected`](#op-get-from-selected), [`Paste`](#op-paste)), the **Previous Gizmo** automatically takes the state of the **former Active Gizmo**. This ensures the two gizmos usually represent the 'before' and 'after' states of the last operation.
    *   *Note:* This setting is overridden and has no effect when using [`Get from selected`](#op-get-from-selected) with exactly 2 objects, as both gizmos are defined directly from the selected objects in that specific case.

*   <span id="opt-cursor-orientation">![3D Cursor Orientation Icon](assets/icons/3D_Cursor_Orientation.png) **3D Cursor Orientation**</span>: This option primarily controls the reference **orientation and scale** used by the interactive [`Create`](#op-create) operation when handling origin cancellation or performing automatic axis creation/alignment. The new gizmo's **origin location** will always be based on the 3D Cursor's current location if origin placement is cancelled.
    1.  *Primary Effect (Reference for <code>Create</code>'s Orientation & Scale during Auto-Creation/Cancellation):*
        *   **When Enabled:** If an axis needs to be auto-created or cancelled, the [`Create`](#op-create) operation uses the current **3D Cursor's orientation** and derives a **scale factor from the screen space zoom level** as the reference.
        *   **When Disabled:** If an axis needs to be auto-created or cancelled, the [`Create`](#op-create) operation uses the **orientation and scale** from the **former Active Gizmo** (the one active before [`Create`](#op-create) began) as the reference.
    2.  *Secondary Effect (Updating the 3D Cursor by other operations):*
        *   **When Enabled:** Allows certain other addon operations (like [`Get from selected`](#op-get-from-selected)) to update the 3D cursor's location and rotation.
        *   **When Disabled:** These other addon operations will not automatically update the 3D cursor.
        *   *(Note: The interactive [`Create`](#op-create) operation *always* updates the 3D cursor's location and rotation upon successful completion, regardless of this setting).*

*   <span id="opt-snap">![Snap Icon](assets/icons/Snap.png) **Snap**</span>: When enabled, this forces the interactive [`Create`](#op-create) operation to use a specific, pre-defined set of snapping settings, **overriding** whatever snapping settings are currently active in Blender's 3D View header. When disabled, [`Create`](#op-create) respects Blender's current header snapping settings.
    *   *Addon's Internal Snap Behavior (When this toggle is ON):*
        *   Turns snapping **ON**.
        *   Sets snapping targets to **Vertex, Edge, Face, and Edge Midpoint**.
        *   Aligns the placed point's **rotation** to the snapped surface normal.
        *   Allows snapping to the **object being edited** (self-snapping).
        *   Ensures snapping works in both **Edit Mode and Object Mode**.
    *   *Refer to the ["Working with the 3D Cursor and Snapping"](#sec-cursor-snapping) section above for general information on Blender's snapping system.*

*   <span id="op-swap" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Swap</span>: Swaps the transform components of the Previous and Active gizmos with each other.
    *   *Default Behavior:* Swap **Full Gizmo** (Location, Rotation, Scale).
    *   ***Modifier Keys:***
        *   **Shift + Click:** Swap **Location** only.
        *   **Ctrl + Click:** Swap **Scale** only.
        *   **Alt + Click:** Swap **Rotation** only.
    *   *Note:* Modifier keys are **additive**. For example, hold **Shift + Alt** to affect Rotation & Scale only (excluding Location).

*   <span id="op-axes-cycling">![Axes Cycling Icon](assets/icons/Axes_Cycling.png) **Axes Cycling**</span>: Cycles the Active gizmo's axes (X becomes Z, Y becomes X, Z becomes Y).
    *   ***Modifier Keys:***
        *   **Shift + Click:** Swap the gizmo pivot with the X-axis (X → C, C → X). The Y-axis direction remains the same, while the Z-axis is reversed.
        *   **Ctrl + Click:** Swap the gizmo pivot with the Y-axis (Y → C, C → Y). The X-axis direction remains the same, while the Z-axis is reversed.
        *   **Alt + Click:** Applies the cycle or swap operation to the Previous Gizmo instead.
    *   *Note:* The Alt key is **additive**. (e.g., Hold **Shift + Alt** to swap the Previous Gizmo pivot with its X-axis).

*   <span id="op-global-align">![Global Align Icon](assets/icons/Global_Align.png) **Global Align**</span>: Resets the rotation of the Active Gizmo, aligning its local axes with the world's global X, Y, and Z axes. The gizmo's origin (location) and scale are not affected.

*   <span id="op-copy" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Copy</span>: Copies the current transform components of the Active gizmo.

*   <span id="op-paste" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Paste</span>: Pastes the copied transform components onto the Active gizmo.
    *   *Default Behavior:* Paste **Full Gizmo** (Location, Rotation, Scale).
    *   ***Modifier Keys:***
        *   **Shift + Click:** Paste **Location** only.
        *   **Alt + Click:** Paste **Rotation** only.
        *   **Ctrl + Click:** Paste **Scale** only.
    *   *Note:* Modifier keys are **additive**. For example, hold **Shift + Alt** to affect Rotation & Scale only (excluding Location).

*   <span id="op-undo-redo">![Undo Icon](assets/icons/Undo.png) ![Redo Icon](assets/icons/Redo.png) **Undo/Redo Gizmos**</span>: Undo or redo changes made to the gizmo states themselves.
    *   **Undo:**
        *   **Click:** Step back one state.
        *   **Shift + Click:** Jump to History Start (Original State).
    *   **Redo:**
        *   **Click:** Step forward one state.
        *   **Shift + Click:** Jump to History End (Newest State).

*   <span id="op-cursor-pivot" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Cursor Pivot</span>: Switches Blender's Transform Orientation between the Active gizmo and the previously used standard orientation. This allows standard Blender operators like Move, Rotate, Scale, etc. to use the Active gizmo's precise alignment.

*   <span id="opt-fixed-size">![Fixed Visual Size Icon](assets/icons/Fixed_Visual_Size.png) **Fixed Visual Size**</span>: Keeps the visual size of the gizmos consistent on screen as you zoom in or out. Doesn't affect transformations.

*   <span id="opt-visibility">![Gizmos Visibility All Icon](assets/icons/Show_all_gizmos.png) **Gizmos Visibility**</span>: Cycles through the visibility states for the Previous and Active gizmos. Clicking the icon toggles between: showing both gizmos ![Gizmos Visibility All Icon](assets/icons/Show_all_gizmos.png), showing only the Active gizmo ![Gizmos Visibility Active Icon](assets/icons/Show_only_the_active_gizmo.png), or hiding both gizmos ![Gizmos Visibility Hidden Icon](assets/icons/Hide_all_gizmos.png).

*   <span id="op-gizmo-to-object" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Gizmo to Object</span>: Transfers gizmo transform data to objects in the scene. 
    *   **The Scale Rule:** Empties *always* inherit the gizmo's full matrix (Location, Rotation, and **Scale**). Meshes *never* inherit the scale, to preserve the object's uniform sizing.
    *   **Single Mesh Selected (Default Click):** Aligns the object's location and orientation to the Active Gizmo *while preserving the visual appearance of the geometry*. **(Note: EXPERIMENTAL)** This effectively changes the object's origin without moving the mesh itself. *This can affect modifiers or constraints.*
    *   **Single Empty Selected (Default Click):** Instantly moves and aligns the existing Empty to match the Active Gizmo's full transform.
    *   **Nothing Selected:** Automatically creates new Empty objects for your currently defined gizmos (one for the Active Gizmo if it exists, and one for the Previous Gizmo if it exists).
    *   ***Modifier Keys (Overrides Selection & Creates Empties):***
        *   **Shift + Click:** Forces the creation of a new Empty matching the **Active Gizmo's** full transform, completely ignoring any currently selected objects.
        *   **Alt + Click:** Forces the creation of a new Empty matching the **Previous Gizmo's** full transform, completely ignoring any currently selected objects.

*   <span id="op-reset-settings" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Reset Settings</span>: Resets all addon settings to their default values.

*   <span id="op-save-settings" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Save Settings</span>: Saves the current addon settings as the new default for future Blender sessions.

---

#### <span id="transformation-section">2. Transformation Section</span>

*   <span id="op-transform" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Transform</span>: Performs a full location and rotation transformation on the selected elements, moving them from the Previous (Faded) gizmo's state to the Active (Bright) gizmo's state. Behavior modified by [Options Section](#options-section) settings like [`Scale`](#opt-scale) and [`Flip`](#opt-flip).
    *   ***Modifier Keys:***
        *   **Ctrl + Click:** **Duplicate & Transform** (creates a copy before transforming).
        *   **Shift + Click:** Transform **3D Cursor only** (instead of selection).
        *   **Alt + Click:** **Reverse Transform** (Active → Previous direction).
    *   *Note:* These shortcuts also work for Move, Rotate, Scale, and Align operations.

*   <span id="op-align-xyz" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Align X / Y / Z</span>: A set of three buttons. Each transforms the selection from the specified axis of the Previous (Faded) gizmo to the corresponding axis of the Active (Bright) gizmo. Supports the same modifier keys as Transform.

*   <span id="op-move" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Move</span>: Moves the selection from the location of the Previous (Faded) gizmo to the location of the Active (Bright) gizmo, ignoring rotation. Behavior modified by [Options Section](#options-section) settings. Supports the same modifier keys as Transform.

*   <span id="op-rotate" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Rotate</span>: Rotates the selection at the location of the Previous (Faded) gizmo to match the rotation of the Active (Bright) gizmo. Supports the same modifier keys as Transform.

*   <span id="op-scale" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Scale</span>: Scales the selection to match the proportional change between the gizmos, using the Previous (Faded) gizmo's center as the pivot point. Supports the same modifier keys as Transform.

*   *Requirement:* These buttons are disabled if Active gizmo and (Previous gizmo or multi-transform list) haven't been defined.

---

#### <span id="multi-transform-section">3. Multi-Transform</span>

This mode allows you to build a list of target gizmos and then apply a transformation sequentially to each target. Unlike the standard method, this mode uses the **Active Gizmo** as the single starting point for the entire operation.

*   <span id="op-multi-transform-toggle" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Multi-Transform</span>: A checkbox to activate this mode. When active, transformation operators will use the multi-gizmo list, **if available**, instead of the standard Previous/Active gizmo pair. If the list is empty, the addon will fall back to the standard transformation method.

*   <span id="op-multi-transform-add" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Add</span>: Adds one or more gizmos to the multi-transform list.
    *   **Default Behavior:**
        *   **With a Selection:** Creates a new gizmo for each selected element (object, face, vertex, etc.) and adds it to the list.
        *   **With No Selection:** Adds a copy of the current Active Gizmo to the list.
    *   ***Modifier Keys:***
        *   **Shift + Click:** Add the **3D Cursor** only (as a gizmo).
        *   **Ctrl + Click:** Add the **Active Gizmo** only (ignores selection).
        *   **Alt + Click:** **Report** the current count of matrices in the multi-transform list (displays in status bar).

*   <span id="op-multi-transform-clear" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Clear</span>: Empties the entire multi-transform list.

*   **Workflow:**
    1.  Enable the `Multi-Transform` checkbox.
    2.  Build the target list by repeatedly selecting elements and clicking `Add`.
    3.  Define the **Active Gizmo**. This will serve as the **single starting point** for the entire sequence. The **Previous Gizmo is ignored** in this mode.
    4.  Select the final object(s) you wish to transform.
    5.  Click a transformation operator like [`Transform`](#op-transform). The addon will apply the transformation from the Active Gizmo to the first target, then from that new position to the second target, and so on.

*   **Key Difference between `Add` and `Get from selected`:**
        While `Get from selected` averages multiple elements into **one** final gizmo, the [`Add`](#op-multi-transform-add) button creates a **separate gizmo for each selected element** and adds them all individually to the multi-transform list.

*   **Visuals and Mode Priority:**
    *   **Appearance:** Gizmos in the multi-transform list are displayed as thin, blue lines representing their Z-axis, drawn at one-third of the normal gizmo size.
    *   **Visibility Logic:** When `Multi-Transform` is enabled **and the list is not empty**, the display changes to make the current mode clear:
        *   The multi-gizmo list (thin blue lines) becomes visible.
        *   The **Previous Gizmo is automatically hidden**.
    *   **Priority Override:** Activating any [Constrained Transform](#constrained-transforms-section) mode will **always take priority**. It will hide the multi-gizmo display and show the standard Previous and Active gizmos, as they are required for that mode's calculations.

*   **Note:** This mode does **not** work with [Constrained Transforms](#constrained-transforms-section).

---

#### <span id="constrained-transforms-section">4. Constrained Transforms Section</span>

*   <span id="op-move-to-rotate-to-scale-to">
        <span style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Move to</span>
        <span style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Rotate to</span>
        <span style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Scale to</span>
    </span>: Buttons to enable/disable the respective constrained transform mode.
    *   <code>Move to</code>: Moves the selection by snapping the 3D Cursor (as a handle) along the constraint to the target's intersection or closest point.
    *   <code>Rotate to</code>: Rotates the selection around the Previous Gizmo center, swinging the 3D Cursor (as a handle) along the constraint to the target's intersection or closest point.
    *   <code>Scale to</code>: Scales the selection from the Previous Gizmo's center, stretching the 3D Cursor (as a handle) along the constraint to the target's intersection or closest point.
    *   ***Modifier Keys (for Apply button):***
        *   **Ctrl + Click:** Translate to the calculated **optimal point** on the source line/plane (uses internal intersection calculation).
        *   **Shift + Click:** Apply to **3D Cursor only** (instead of selection).
        *   **Alt + Click:** **Reverse** (Target → Source direction).

*   <span id="op-apply-constrained" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Apply</span>: Executes the enabled constrained operation.

*   **Real-Time Viewport Information Display:** During constrained transform operations, real-time text feedback is displayed in the viewport. The text always begins with the resulting **`distance`**, followed by specific data about the operation performed.
    *   **Understanding `distance`:** This value (`distance: 1.2345`) represents the actual 3D spatial gap remaining between your transformed source geometry and the calculated closest point on the target geometry *after* the operation.
        *   If the operation results in a perfect intersection or perfectly "lands" on the target, this distance will be **`0.0000`**.
        *   If the geometries cannot physically intersect (e.g., a line was rotated to be perfectly parallel to a plane, but they are still offset in space), this value represents the shortest remaining gap between them.
    *   **`Move to`** (e.g., `distance: 0.0000, translation: 5.6789`)
        *   **`translation`**: The total length of the movement vector applied to the selection.
    *   **`Scale to`** (e.g., `distance: 0.0000, scale: 2.3456`)
        *   **`scale`**: The final calculated scale factor multiplier applied to stretch the source to the target.
    *   **`Rotate to`** (e.g., `distance: 0.0000, angle: 45.00`)
        *   **`angle`**: The total rotation applied to the selection, in degrees.
        *   **`aligned`** (e.g., `distance: 0.0000, angle: 45.00, aligned`): This text is automatically appended if the addon successfully triggered a special geometric alignment (such as a line perfectly landing flush on a plane, or achieving sphere tangency).

### Understanding the Key Roles

**1. The Target (The Destination - Always the Same):**
The target is the destination for the operation. It is **always** defined by the **Active gizmo's** full transform (location and orientation). This is consistent for all three modes.

**2. The Source (The Starting Geometry):**
The source defines how the 3D Cursor is interpreted for the transformation. The **Source Type** selector (three buttons with vertex/edge/face icons) determines this:

*   **POINT (Vertex icon):** Use only the 3D Cursor's **Location** as a single point. This is the default behavior.
*   **LINE (Edge icon):** Use the 3D Cursor's **Location and Z-Axis** direction as a line. This enables more complex operations like rotating a line until it becomes tangent to a sphere.
*   **PLANE (Face icon):** Use the 3D Cursor's **Location and Z-Axis** as the plane normal. This enables plane-based calculations.

*   <span id="opt-reverse_roles">![Reverse Roles Icon](assets/icons/reverse_roles.png) **Reverse Roles**</span>: Instantly exchanges the setup of your constrained transformation by swapping the starting point and the destination. This is ideal for quickly testing bidirectional transformations without manually rebuilding your setup. When clicked, it performs the following simultaneously:
    *   **Transforms:** Moves the Active Gizmo to the 3D Cursor's exact location and orientation, and snaps the 3D Cursor to the former Active Gizmo's state.
    *   **Geometry Types:** Swaps your active **Source Type** (Point, Line, or Plane) with your **Target Type** (0, 1, or 2 axes selected). *(Note: If the Target is a Sphere (3 axes), it will be treated as Point).*
*   <span id="opt-align_3d_ursor">![Align 3D Cursor Icon](assets/icons/align_3d_cursor.png) **Align 3D Cursor**</span>: Align the 3D Cursor to use as the **Source** for constrained transformations. This operation shares the same underlying logic as [`Create`](#op-create) and [`Get from selected`](#op-get-from-selected), but skips gizmo updates (only affects the cursor).
    *   ***Modifier Keys:***
        *   **Click:** Snap and align the 3D Cursor **Z-axis** to the selection.
        *   **Shift + Click:** Interactively snap the 3D Cursor to Vertices, Edges or Faces based on selected Source Type.

**3. The Origin of Transformation (Where the action starts):**
This is what makes each mode different.

*   `Move to`: The transformation starts from the **3D Cursor's location** (interpreted according to Source Type). The selection is translated from this point.
*   `Rotate to`: The transformation pivots around the **Previous gizmo's location**.
*   `Scale to`: The transformation pivots around the **Previous gizmo's location**.

**4. The Constraint (The Rule of Movement):**
The constraint defines the path or space the transformation is restricted to. It is **always** based on the **Previous gizmo's orientation** (its location is ignored for defining the direction).
*   For `Move to`, this directional rule is applied at the **3D Cursor's location**.
*   For `Rotate to` and `Scale to`, this directional rule is applied at the **Previous gizmo's location (the pivot)**.

**5. The 3D Cursor's Role:**
*   For `Move to`, it is the **starting point** of the translation (optionally with orientation for LINE/PLANE source types).
*   For `Rotate to` and `Scale to`, it acts as a **handle** that is "swung" or "stretched" from the pivot towards the target, determining the final angle or scale.

### Defining the Constraint and Target Geometry

Both the `Constrain direction` (Previous Gizmo) and the `Target` geometry (Active Gizmo) are configured using the same axis-selection method. Toggling the `X`/`Y`/`Z` buttons determines how the geometry behaves.

**1. How the Constraint is Defined (Previous Gizmo)**
The Previous Gizmo acts as the "Rule of Movement." Its behavior changes depending on the operation mode:

#### <span style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Move to</span>
*   **0 or 3 axes selected (Free):** Acts as unconstrained free movement in 3D space.
*   **1 axis selected (Line):** Strictly translates the selection along this axis.
*   **2 axes selected (Plane):** Translates freely, but strictly *within* the 2D constraint plane.

#### <span style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Rotate to</span>
*   **0 or 3 axes selected (Free):** Acts as unconstrained free rotation in 3D space.
*   **1 axis selected (Line):** Strictly pivots the selection *around* this specific axis.
*   **2 axes selected (Plane):** Calculates a free rotation towards the target, but restricts it by forcing the new rotation axis to lie flat on the constraint plane. 
    *   ***Important Exception:** This planar rotation constraint is only used if at least the Source or Target is a Point. If both are complex geometry (e.g., Line to Plane, Plane to Sphere), altering the rotation axis would destroy the mathematical tangency or alignment. In these cases, the addon automatically ignores the 2-axis constraint and uses **Free Rotation**.*

#### <span style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Scale to</span>
*   **0 axes selected (Uniform):** Acts as a true **Uniform Scale** (scales all axes equally until the source reaches the target).
*   **1 axis selected (Line):** Acts as **Non-Uniform Scale** constrained to the single selected axis. The source will only stretch along this specific axis to meet the target.
*   **2 axes selected (Plane):** Acts as **Non-Uniform Scale** constrained to the two selected axes. It will not scale along the unselected (normal) axis, behaving similarly to a `Shift + [Axis]` operation in standard Blender.
*   **3 axes selected (Full Non-Uniform):** Unlocks non-uniform scaling across all three axes. It calculates the precise translation vector needed to reach the target, but applies it by scaling the source point along each individual axis until it perfectly stretches to the target.

---

**2. How the Target Geometry is Defined (Active Gizmo)**
The Active Gizmo serves as the destination for your transformation.

*   **0 axes selected (Point):** Defines a single point at the gizmo origin.
*   **1 axis selected (Line):** Defines an infinite line along the selected axis.
*   **2 axes selected (Plane):** Defines an infinite plane (its normal is the *unselected* axis).
*   **3 axes selected (Sphere):** Defines a sphere centered at the gizmo origin. Its radius is explicitly defined by the distance between the gizmo's center (white circle) and any of its axis endpoints (e.g., the red X-axis circle).

### Special Geometric Calculations

Because mathematically projecting infinite lines and planes can result in immediate or unhelpful intersections, the addon uses smart alignment and tangency logic for specific Source-to-Target combinations:

**1. Smart Alignments (`Rotate to` only):**
*   **Line to Plane & Plane to Line:**
    *   **Free Rotation (0, 2*, or 3 constraint axes):** The addon rotates the source so it perfectly aligns with and "lands" on the target (e.g., the line becomes fully flush and part of the plane). If a full landing is geometrically impossible, it will simply align them to be parallel.
    *   **Constrained Rotation (1 constraint axis):** The addon rotates the source strictly around the constraint axis until it aligns with the target. If alignment is geometrically impossible from that constrained axis, the operation does nothing.
*   **Plane to Plane:**
    *   **Free Rotation (0, 2*, or 3 constraint axes):** Rotates the source plane to perfectly align and become completely flush with the target plane.
    *   **Constrained Rotation (1 constraint axis):** Full alignment is ignored. Instead, the addon finds the specific point on the source plane that is closest to the pivot point, and rotates until *that specific point* lands on the target plane.
*(Note: *2 constraint axes will automatically fall back to Free Rotation for Line/Plane alignments, as detailed in the Constraint Definition section).*

**2. Sphere Tangency (Line/Plane to Sphere):**
When the target is a Sphere (3 axes) and the source is a Line or Plane, the operation calculates a tangency rather than targeting the sphere's center. It stops exactly when the source geometry becomes completely flush with the sphere's outer surface.
*   **`Move to` & `Rotate to`:** Always calculate tangency to the sphere's surface.
*   **`Scale to`:** Calculates tangency *only* if using free/non-uniform scale (3 constraint axes selected). If using uniform scaling, it targets the sphere's center.
*   **Opposite Side Tangency:** Enabling the [`Alternate Solution`](#opt-alternate-solution) toggle forces the operation to use the tangency point on the far/opposite side of the sphere (e.g., switching from external to internal tangency).

**3. Viewport Feedback ("aligned") for `Rotate to` only:**
Whenever a valid smart alignment or tangency is successfully calculated during a `Rotate to` operation, the real-time viewport information text will append an alignment indicator to the display (e.g., `distance: 0.0000, angle: 45.00, aligned`). This text indicator is exclusive to `Rotate to` because rotation operations do not inherently guarantee alignment (especially with Line and Plane targets), so it serves as confirmation of a successful flush landing. 
*(Note: `Move to` and `Scale to` do not display this "aligned" text, but they still successfully align and achieve tangency when used with Sphere targets).*

*   <span id="opt-alternate-solution">**<code>Alternate Solution</code>:**</span> Toggle to use the alternate intersection point instead of the closest or shortest one.
    *   **For Sphere Targets:** Toggles between the near side (entry point) and far side (exit point) of the sphere intersection.
    *   **For Plane Targets:** Toggles between the two possible intersection points when applicable.
    *   **For Line Targets:** Toggles between the two possible intersection points.

*   <span id="opt-xform-offset">**<code>Offset</code>:**</span> Offsets the constrained transformation result. The effect depends on the operation type:
    *   **Rotate to:** Offsets the rotation angle (in degrees).
    *   **Move to:** Offsets the translation distance.
    *   **Scale to:** Offsets the scale factor.

*   <span id="opt-snap-cursor-constrained">**<code>Snap Cursor</code>:**</span> This checkbox primarily affects operations within the **Constrained Transforms Section**:
    1.  **Setup Behavior:** When enabled, selecting constraint types or target axes/directions automatically **snaps the 3D Cursor to relevant geometry** (e.g., axis origins, target points). This helps precisely set the starting point for the [`Move To`/`Rotate To`](#op-move-to-rotate-to-scale-to) operations *before* they are applied.
    2.  **Post-Operation Behavior:** By default (when <code>Snap Cursor</code> is unchecked), the [`Move to`](#op-move-to-rotate-to-scale-to) and [`Rotate to`](#op-move-to-rotate-to-scale-to) operations **do not** update the 3D Cursor's position/rotation after execution, preserving the original starting point. However, **if <code>Snap Cursor</code> is enabled, [`Move to`](#op-move-to-rotate-to-scale-to) and [`Rotate to`](#op-move-to-rotate-to-scale-to) <em>will</em> update the 3D Cursor's location and rotation** after they complete (typically placing the cursor at the final transformed location or target point reached). This makes their cursor update behavior consistent with how other transformation operations might behave (depending on the [`3D Cursor Orientation`](#opt-cursor-orientation) setting).

---

#### <span id="options-section">5. Options Section</span>

Modify the behavior of transformation operations like [`Transform`](#op-transform), [`Align`](#op-align-xyz), [`Move`](#op-move), and the [Constrained Transforms](#constrained-transforms-section).

*   <span id="opt-scale">**<code>Scale</code>**</span>: Include the uniform scale factor difference between the gizmos in the transformation calculations for operations in the [Transformation Section](#transformation-section). If unchecked, the scale component of the gizmos is ignored, and only location and rotation differences are applied.
*   ***Note on Scale:*** Whether the scale component of the gizmos is included in the <code>Transform</code>, <code>Align</code>, and <code>Move</code> operations depends on the state of the [<code>Scale</code>](#opt-scale) checkbox in the [Options Section](#options-section), while the "Scale" operation alway active the scale option -it can be deactivate manually in the redo menu but there is no practical reason to do that-.

*   <span id="opt-flip">**<code>Flip</code>**</span>: Rotates the Previous gizmo 180 degrees around its Y-axis before applying the transformation. Primarily used when transforming between two faces (e.g., on different objects) that should end up facing each other, rather than aligned in the same direction.

*   <span id="opt-duplicate">**<code>Duplicate</code>**</span>: Duplicates the selected elements before transforming them.

*   <span id="opt-instance">**<code>Instance</code>**</span>: *(Object Mode Only)* When [`Duplicate`](#opt-duplicate) is checked, creates linked instances instead of full copies.

*   <span id="opt-extrude">**<code>Extrude</code>**</span>: *(Edit Mode Only for Mesh/Curve/Armature)* Extrudes selected mesh components, curve points, or bones during the transformation instead of just moving them. *Note*: If both [`Extrude`](#opt-extrude) and [`Duplicate`](#opt-duplicate) are enabled, `Extrude` overrides `Duplicate`; the elements will be extruded, not duplicated then transformed.

*   ***Note:*** The `Instance` and `Extrude` options are contextual and replace each other in the UI. The option displayed depends on whether you are in **Object Mode** or **Edit Mode**.

*   <span id="opt-interpolation-value">**<code>Interpolation Value</code>**</span>: Controls the progression along the calculated transformation path between the Previous and Active gizmo states. The addon calculates the smooth spiral path required to transform from the Previous to the Active state (using the shortest arc by default).
    *   The slider provides a range from **-1.0 to 1.0**, with a **default value of 1.0**.
    *   **`1.0`**: Represents the full transformation to the Active gizmo state.
    *   **`0.0`**: Represents the initial state (no transformation applied).
    *   **`0.5`**: Represents the transformation halfway between the Previous and Active states.
    *   **`-1.0`**: Reverses the transformation, effectively equivalent to swapping the Previous and Active gizmos.
    *   Values between -1.0 and 1.0 interpolate the transformation along the calculated path.
    *   Values outside the **-1.0 to 1.0** range (e.g., 10.00 or -1.62) extrapolate the transformation further along the calculated path in the respective direction and must be **entered manually** into the value field.

*   <span id="opt-lock-interpolation">![Lock Interpolation Icon](assets/icons/Lock_Interpolation.png) **Lock Interpolation**</span>: If checked, the final transformation respects the [`Interpolation Value`](#opt-interpolation-value) even when [`Count`](#opt-count) > 1. If unchecked, `Count` repeats the <em>full</em> (1.0) transformation multiple times.

*   <span id="opt-count">**<code>Count</code>**</span>: Repeats the calculated transformation multiple times. Works with interpolation respecting the [Lock Interpolation](#opt-lock-interpolation) setting.

---

#### <span id="redo-last-section">6. Redo Last Menu (F9 Panel)</span>

After performing any transformation operation ([`Transform`](#op-transform), [`Move to`](#op-move-to-rotate-to-scale-to), etc.), a special menu becomes available. This menu allows you to interactively fine-tune the result of the last action without undoing and redoing the entire process.

**How to Access:**
*   Press `F9` immediately after an operation.
*   Or, click on the operator panel that appears in the bottom-left corner of the 3D Viewport.

This menu contains all the standard options from the main panel, plus several **exclusive settings** that offer even greater control.

### Exclusive Redo Last Settings

These advanced options are **only available in the Redo Last menu**:

*   <span id="redo-operation-type">**`Operation Type`**</span>: A dropdown menu that allows you to change the fundamental type of the transformation you just performed, without needing to undo and re-run the operator from the main panel. Use this to iterate on a transformation.
    *   **Use Cases:**
        *   **Switching Modes:** If you executed a full [`Transform`](#op-transform) but realize you only wanted to apply the location difference, you can select `Move` from this menu to instantly see the updated result.
        *   **Testing Alignments:** After performing an `Align X` operation, you can use this dropdown to instantly preview the result of `Align Y` or `Align Z` to find the correct orientation without re-running the operator.
    *   **Available Operations:**
        *   `Transform` (Full transform)
        *   `Move` (Location only)
        *   `Rotate` (Rotation only)
        *   `Scale` (Scale only)
        *   `Align X`, `Align Y`, `Align Z` (Axis-specific alignment)
    *   **Availability:** This menu is only available for the standard transformation operators in the [Transformation Section](#transformation-section). It will **not** appear when using [Constrained Transforms](#constrained-transforms-section), as those are interactive operations with their own distinct logic.
    
*   <span id="opt-longest-arc">**<code>Longest Arc Path</code>**</span>: When checked, forces the underlying spiral path calculation (used for [`Interpolation`](#opt-interpolation-value) and repeated [`Count`](#opt-count) operations) to use the longer arc (>180 degrees) instead of the default shortest arc between the gizmo orientations.
    *   **Availability:** This setting only affects operations that include a rotational component (e.g., `Transform`, `Rotate`, `Align`, `Rotate to`). It has no effect and will be disabled for pure translation or scaling operations like `Move` or `Scale`.

*   <span id="redo-skip-last">**`Skip Last`**</span>:
    *   **Function:** Prevents the final iteration of a repeated (`Count` > 1) duplicate or extrusion from being created.
    *   **Use Case:** This is ideal for creating arrays or series of objects *between* a start and end point. For example, to place 5 fence posts between two main pillars, you would set the transformation `Count` to 6 (to define the 6 steps/gaps) and enable `Skip Last`. This creates the 5 intermediate posts without an unnecessary sixth one at the final destination.
    *   **Availability:** This checkbox is only active when either `Duplicate` or `Extrude` is enabled and the `Count` is set to a value greater than 1.

*   <span id="redo-chain-transform">**`Chain Transform`**</span>: This checkbox changes the behavior of the Multi-Transform operation. It **only has an effect when `Duplicate` is enabled and the `Count` is greater than one.** It switches the duplication logic between two distinct patterns: Independent and Sequential.

    *   **Unchecked (Default): Independent "Star" Pattern**
        *   **Behavior:** The transformation originates from the single **Active Gizmo** and is applied independently to *each* target gizmo in the multi-transform list.
        *   **Analogy:** This creates a star-like or "octopus" pattern, where all transformation arrays radiate outwards from the central starting point.
        *   **Transformation Path:**
            *   `Start → Target 1`
            *   `Start → Target 2`
            *   `Start → Target 3`

    *   **Checked: Sequential "Chain" Pattern**
        *   **Behavior:** The transformations are applied sequentially in a continuous chain. The result of transforming to the first target becomes the starting point for the transformation to the second target, and so on.
        *   **Analogy:** This creates a continuous, snake-like "python" or linked-chain pattern, where each segment follows the last.
        *   **Transformation Path:** `Start → Target 1 → Target 2 → Target 3`

*   ***Note:*** The `Chain Transform` and `Skip Last` options are contextual and replace each other in the UI. The option displayed depends on whether [`Multi-Transform`](#op-transform) mode is active.

*   <span id="redo-scale-multiplier">**`Scale Multiplier`**</span>:
    *   **Function:** A slider that uniformly multiplies the final calculated scale of the entire transformation.
    *   **Details:** This acts as a global, final adjustment. It applies on top of any scale derived from the `Scale` checkbox, the `Scale`/`Scale to` operators, or even non-uniform scaling via the individual axis checkboxes.
    *   **Example:** If your operation results in a non-uniform scale of (2.0, 1.0, 1.0), setting the `Scale Multiplier` to 0.5 will result in a final scale of (1.0, 0.5, 0.5).

*   <span id="redo-complex-dup">**`Complex Dup`**</span>:
    *   **Function:** A more compatible, but slower, method for handling duplication.
    *   **When to Use:** The addon's default duplication is highly optimized for speed. However, for certain advanced scenarios, it may not correctly update relationships between the newly created objects. Enable `Complex Dup` if you encounter issues such as:
        *   Boolean modifiers on one duplicated object that need to target *another* object also being created in the same operation.
        *   Other complex modifier stacks that rely on inter-object relationships that fail to update correctly.
    *   **Performance Impact:** This mode uses Blender's standard operators to ensure maximum compatibility. This is significantly slower than the addon's default method, and the slowdown will be noticeable when using a high `Count` or duplicating many elements at once. Use it only when necessary.

### Standard Options
The Redo Last menu also includes familiar settings like `Interpolation Value`, `Count`, `Duplicate`, `Instance`, `Flip`, etc. Changing these values will instantly update the result of your last transformation in the viewport, allowing for quick and iterative adjustments.

*   **On-the-fly Gizmo Adjustments:** The **Axes Cycling** and **Rotate** buttons are also available directly in the Redo Last menu for extra control. This allows you to interactively reorient your gizmos mid-operation and instantly see how the new axis alignment affects your final transformation, without needing to undo, adjust the gizmo manually, and re-run the tool.

---

### Option Behavior in the Redo Last Menu

The Redo Last menu (F9) offers two types of settings: those that update the addon's main panel for future operations, and those that are temporary adjustments for the last action only.

**1. Persistent Overrides (Stateful)**

When you change these settings in the Redo Last menu, the new value is **saved back to the main addon panel**. This becomes the new default for your next operation.

This behavior applies to the following options:
*   `Scale`
*   `Flip`
*   `Duplicate`
*   `Instance`
*   `Extrude`
*   `Complex Dup`

**Example:** If you enable `Duplicate` in the Redo menu, the `Duplicate` checkbox on the main panel will also become enabled.

**2. Temporary Adjustments (Instance-Only)**

These settings start with the current value from the main panel, but changing them in the Redo Last menu **does not affect the main panel**. This allows you to fine-tune the last operation without altering your default setup for the next one.

This behavior applies to all other options, including:
*   `Interpolation Value`
*   `Lock Interpolation`
*   `Count`
*   `Scale Multiplier`
*   `Skip Last`
*   `Longest Arc Path`

**Example:** If `Count` is 4 on your main panel, you can change it to 10 in the Redo menu to create 10 copies. After you are done, the `Count` on the main panel will still be 4 for your next operation.

---

**Session Persistence:** All settings on the main panel, including those persistently set via the Redo menu, will reset to their defaults when you restart Blender (unless saved using the [Save Settings](#op-save-settings) button).