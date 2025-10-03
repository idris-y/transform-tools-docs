# Transform Tools Addon - Description and Documentation

---

## Description

### Transform Tools: Advanced Gizmo Control & Precision Transformations

Transform Tools enhances object manipulation in Blender using **custom 3D gizmos** for precise transformation control. It works closely with Blender's core tools.

#### Core Features:

*   **Flexible Gizmo Definition:** Define transformation origins and targets accurately. Create gizmos automatically from selections, interactively using Blender's 3D cursor, or update them based on existing elements.
*   **Dual-Gizmo Transformations:** Apply a transformation between the two gizmo states, using modes for full transform (location, rotation, scale), specific axis alignment, or location only.
*   **Independent Custom Orientation:** Use the Active gizmo as a custom transform orientation for standard Blender transform tools like Move, Rotate, Scale, etc.
*   **Constrained Transforms:** Execute transformations where selections move or rotate from the 3D Cursor position along a specified direction towards a calculated intersection point on a target line, plane, or center derived from the gizmo setup.
*   **Advanced Options:** Includes interpolation, duplication, edit-mode extrusion, axis flipping, and count repetition.
*   **Gizmo Management:** Provides tools to swap, copy/paste, undo/redo gizmo states, and control visibility.

#### Workflow:

The addon operates by **defining the 'Previous' (start) and 'Active' (end) gizmo states, then applying transformations** based on these states. This system facilitates precise pose-to-pose adjustments and complex constrained operations. It integrates with Blender's 3D Cursor for placement and initiating constrained actions.

---

## User Documentation

### Overview

Transform Tools provides enhanced transformation capabilities in Blender using custom 3D gizmos and dedicated operators. It allows for precise control over moving, rotating, and scaling objects and mesh components. The addon includes methods for:

*   Transforming elements between two explicitly defined transform states using [dual gizmos](#transformation-section).
*   Using a single gizmo as a [custom orientation](#op-cursor-pivot) for standard Blender transform tools.
*   Executing [constrained transformations](#constrained-transforms-section), where elements move or rotate from the 3D cursor position towards a computed intersection point with target geometry (a point, line, or plane) derived from the gizmos.

**Core Workflow:** The fundamental process involves:

1.  **Defining** the 'Previous' gizmo (representing the start state).
2.  **Defining** the 'Active' gizmo (representing the end state).
3.  Selecting the elements to modify.
4.  **Applying** a transformation operation that uses the defined gizmos (like [<code>Transform</code>](#op-transform), [<code>Align</code>](#op-align), [<code>Move</code>](#op-move), or [Constrained Transforms](#constrained-transforms-section)).

**Key Concepts:**

*   Gizmos definition sources: [selections](#op-get-from-selected), [interactive clicking](#op-create), [3D cursor state](#op-get-from-selected).
*   Active gizmo can serve as a custom orientation for standard Blender tools via the [<code>Cursor Pivot</code>](#op-cursor-pivot) operator.
*   Utilizes Blender's 3D Cursor for interactive placement and as the origin for [Constrained Transforms](#constrained-transforms-section).

### Installation

1.  Go to `Edit > Preferences > Add-ons`.
2.  Click `Install` and select the downloaded addon `.zip` file.
3.  Find "Transform Tools" in the list and enable the checkbox.
4.  The panel will appear in the 3D View's Sidebar (N-Panel) under the "XForm Tools" tab (or your chosen short category name).

---

### <span id="sec-cursor-snapping">Working with the 3D Cursor and Snapping</span>

The **3D Cursor** plays a vital role in several Transform Tools operations:

*   **Interactive Gizmo Creation ([`Create`](#op-create)):** Click locations are interpreted via the 3D Cursor system to determine positions in 3D space.
*   **Gizmo Definition ([`Get from selected`](#op-get-from-selected)):** When nothing is selected, the Active gizmo is defined using the 3D Cursor's current transform.
*   **[Constrained Transforms (`Move to` / `Rotate to`)](#constrained-transforms-section):** The 3D Cursor's location acts as the *starting point* for these transformations.

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

*   <span id="op-get-from-selected" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Get from selected</span>: Creates/updates gizmo states based on the current selection. The behavior is highly context-aware.

    *   **Object Mode (1 or >2 objects):** Defines the Active gizmo from the active object or the average of all selected.
    *   **Object Mode (Exactly 2 objects):** Defines Previous gizmo from the non-active and Active gizmo from the active selected object.
    *   **Edit Mode (Mesh):** The operator respects the current selection mode (Vertex, Edge, or Face) to interpret your selection.
        *   **Face:** Uses the face's normal (Z-axis), first edge (X/Y-axis), center, and area (uniform scale).
        *   **Edge:** Uses the edge's direction (X-axis), connected face normals (Z-axis), center, and length (uniform scale).
        *   **Vertex:** A sophisticated hierarchy is used to create a stable orientation:
            *   **Z-axis:** Determined by this priority: 1) Average normal of all connected faces. 2) If no faces, a normal is calculated from all connected edges. 3) If no connected edges, the object's local Z-axis is used.
            *   **X-axis:** Aligned with the direction of the first connected edge.
            *   **Scale:** Calculated from the average length of all connected edges.
        *   **Multiple Selected Elements (e.g., several faces):** The operator first calculates an individual gizmo for *each* selected element. It then computes the **average** of all these individual gizmos (average location, rotation, and scale) to create a single, final **Active Gizmo**.
    *   **Edit Mode (Curve/Armature):** Defines the Active gizmo using the location/rotation of selected points/bones.
    *   **Nothing Selected:** Defines the Active gizmo from the 3D Cursor's current transform.

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

*   <span id="op-swap" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Swap</span>: Swaps the transform components (location, rotation, scale) of the Previous and Active gizmos. Affected components depend on the [Swap/Paste Mode Dropdown](#opt-swap-paste-mode).

*   <span id="op-axes-cycling">![Axes Cycling Icon](assets/icons/Axes_Cycling.png) **Axes Cycling**</span>: Rotates the meaning of the Active gizmo's axes (X becomes Z, Y becomes X, Z becomes Y).

*   <span id="op-rotate-gizmo">![Rotate Icon](assets/icons/Rotate.png) **Rotate**</span>: Rotates the Active gizmo 90 degrees around its local X-axis.

*   <span id="op-copy" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Copy</span>: Copies the current transform components of the Active gizmo.

*   <span id="op-paste" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Paste</span>: Pastes the copied transform components onto the Active gizmo. Affected components depend on the [Swap/Paste Mode Dropdown](#opt-swap-paste-mode).

*   <span id="opt-swap-paste-mode" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Swap/Paste Mode Dropdown</span> (<code>Gizmo</code>/<code>Location</code>/<code>Rotation</code>/<code>Scale</code>): Controls which transform components (full state, location only, rotation only, or scale only) are affected by the [`Swap`](#op-swap) or [`Paste`](#op-paste) operations.

*   <span id="op-undo-redo">![Undo Icon](assets/icons/Undo.png) ![Redo Icon](assets/icons/Redo.png) **Undo/Redo Gizmos**</span>: Undo or redo changes made to the gizmo states themselves.

*   <span id="op-cursor-pivot" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Cursor Pivot</span>: Switches Blender's Transform Orientation between the Active gizmo and the previously used standard orientation. This allows standard Blender operators like Move, Rotate, Scale, etc. to use the Active gizmo's precise alignment.

*   <span id="opt-fixed-size">![Fixed Visual Size Icon](assets/icons/Fixed_Visual_Size.png) **Fixed Visual Size**</span>: Keeps the visual size of the gizmos consistent on screen as you zoom in or out. Doesn't affect transformations.

*   <span id="opt-visibility">![Gizmos Visibility All Icon](assets/icons/Show_all_gizmos.png) **Gizmos Visibility**</span>: Cycles through the visibility states for the Previous and Active gizmos. Clicking the icon toggles between: showing both gizmos ![Gizmos Visibility All Icon](assets/icons/Show_all_gizmos.png), showing only the Active gizmo ![Gizmos Visibility Active Icon](assets/icons/Show_only_the_active_gizmo.png), or hiding both gizmos ![Gizmos Visibility Hidden Icon](assets/icons/Hide_all_gizmos.png).

*   <span id="op-apply-to-selection" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Apply to Selection</span>: Applies the Active gizmo's location & rotation while preserve is average scale to a uniform scale, directly to the selected object, setting its world matrix. This is similar to `Object > Apply > All Transforms` but uses the gizmo's state instead of the world origin.

---

#### <span id="transformation-section">2. Transformation Section</span>

*   <span id="op-transform" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Transform</span>: Performs a full location and rotation transformation on the selected elements, moving them from the Previous gizmo's state to the Active gizmo's state. Behavior modified by [Options Section](#options-section) settings like [`Scale`](#opt-scale) and [`Flip`](#opt-flip).
*   <span id="op-align-xyz" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Align X / Y / Z</span>: A set of three buttons. Each transforms the selection from the specified axis of the Previous gizmo to the corresponding axis of the Active gizmo.
*   <span id="op-move" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Move</span>: Moves the selection from the location of the Previous gizmo to the location of the Active gizmo, ignoring rotation. Behavior modified by [Options Section](#options-section) settings like [`Scale`](#opt-scale) and [`Flip`](#opt-flip).*   <span id="op-rotate" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Rotate</span>: Rotates the selection at the location of the Previous gizmo to match the rotation of the Active gizmo.
*   <span id="op-scale" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Scale</span>: Scales the selection to match the proportional change between the gizmos, using the Previous gizmo's center as the pivot point.
*   *Requirement:* These buttons are disabled if Active gizmo and (Previous gizmo or multi-transform list) haven't been defined.

---

#### <span id="multi-transform-section">3. Multi-Transform</span>

This mode allows you to build a list of target gizmos and then apply a transformation sequentially to each target. Unlike the standard method, this mode uses the **Active Gizmo** as the single starting point for the entire operation.

*   <span id="op-multi-transform-toggle" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Multi-Transform</span>: A checkbox to activate this mode. When active, transformation operators will use the multi-gizmo list, **if available**, instead of the standard Previous/Active gizmo pair. If the list is empty, the addon will fall back to the standard transformation method.
*   <span id="op-multi-transform-add" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Add</span>: Adds one or more gizmos to the multi-transform list.
    *   **With a Selection:** Creates a new gizmo for each selected element (object, face, vertex, etc.) and adds it to the list.
    *   **With No Selection:** Adds a copy of the current Active Gizmo to the list.
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

#### <span id="constrained-transforms-section">3. Constrained Transforms Section</span>

*   <span id="op-move-to-rotate-to-scale-to">
        <span style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Move to</span>
        <span style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Rotate to</span>
        <span style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Scale to</span>
    </span>: Buttons to enable/disable the respective constrained transform mode.
    *   <code>Move to</code>: Moves the selection by snapping the 3D Cursor (as a handle) along the constraint to the target's intersection or closest point.
    *   <code>Rotate to</code>: Rotates the selection around the Previous Gizmo center, swinging the 3D Cursor (as a handle) along the constraint to the target's intersection or closest point.
    *   <code>Scale to</code>: Scales the selection from the Previous Gizmo's center, stretching the 3D Cursor (as a handle) along the constraint to the target's intersection or closest point.
*   <span id="op-apply-constrained" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Apply</span>: Executes the enabled constrained operation.

### Understanding the Key Roles

**1. The Target (The Destination - Always the Same):**
The target is the destination for the operation. It is **always** defined by the **Active gizmo's** full transform (location and orientation). This is consistent for all three modes.

**2. The Origin of Transformation (Where the action starts):**
This is what makes each mode different.

*   `Move to`: The transformation starts from the **3D Cursor's location**. The selection is translated from this point.
*   `Rotate to`: The transformation pivots around the **Previous gizmo's location**.
*   `Scale to`: The transformation pivots around the **Previous gizmo's location**.

**3. The Constraint (The Rule of Movement):**
The constraint defines the path or space the transformation is restricted to. It is **always** based on the **Previous gizmo's orientation** (its location is ignored for defining the direction).
*   For `Move to`, this directional rule is applied at the **3D Cursor's location**.
*   For `Rotate to` and `Scale to`, this directional rule is applied at the **Previous gizmo's location (the pivot)**.

**4. The 3D Cursor's Role:**
*   For `Move to`, it is the **starting point** of the translation.
*   For `Rotate to` and `Scale to`, it acts as a **handle** that is "swung" or "stretched" from the pivot towards the target, determining the final angle or scale.

### Defining the Constraint and Target Geometry

Both the `Constrain direction` and the `Target` geometry are configured using the same axis-selection method. Toggling the `X`/`Y`/`Z` buttons determines the geometry type.

*   **How the Geometry is Defined:**
    *   **1 axis selected (Line):** Defines a line.
    *   **2 axes selected (Plane):** Defines a plane (perpendicular to the *unselected* axis).
    *   **0 or 3 axes selected (Center / Auto):** Defines a single point.
*   <span id="opt-limit-to-line">**<code>Limit to Line</code>:**</span> If checked (and using a `Line` target), the operation stops at the ends of the line segment.
*   <span id="opt-snap-cursor-constrained">**<code>Snap Cursor</code>:**</span> This checkbox primarily affects operations within the **Constrained Transforms Section**:
    1.  **Setup Behavior:** When enabled, selecting constraint types (<code>Auto</code>/<code>Line</code>/<code>Plane</code>) or target axes/directions automatically **snaps the 3D Cursor to relevant geometry** (e.g., axis origins, target points). This helps precisely set the starting point for the [`Move To`/`Rotate To`](#op-move-to-rotate-to-scale-to) operations *before* they are applied.
    2.  **Post-Operation Behavior:** By default (when <code>Snap Cursor</code> is unchecked), the [`Move to`](#op-move-to-rotate-to-scale-to) and [`Rotate to`](#op-move-to-rotate-to-scale-to) operations **do not** update the 3D Cursor's position/rotation after execution, preserving the original starting point. However, **if <code>Snap Cursor</code> is enabled, [`Move to`](#op-move-to-rotate-to-scale-to) and [`Rotate to`](#op-move-to-rotate-to-scale-to) <em>will</em> update the 3D Cursor's location and rotation** after they complete (typically placing the cursor at the final transformed location or target point reached). This makes their cursor update behavior consistent with how other transformation operations might behave (depending on the [`3D Cursor Orientation`](#opt-cursor-orientation) setting).
*   <span id="opt-limit-to-line">**<code>Limit to Line</code>:**</span> If checked (and using <code>Line</code> target), rotation stops at the ends of the line segment defined by the gizmo axis.

---

#### <span id="options-section">4. Options Section</span>

Modify the behavior of transformation operations like [`Transform`](#op-transform), [`Align`](#op-align), [`Move`](#op-move), and the [Constrained Transforms](#constrained-transforms-section).

*   <span id="opt-scale">**<code>Scale</code>**</span>: Include the uniform scale factor difference between the gizmos in the transformation calculations for operations in the [Transformation Section](#transformation-section). If unchecked, the scale component of the gizmos is ignored, and only location and rotation differences are applied.
*   ***Note on Scale:*** Whether the scale component of the gizmos is included in the <code>Transform</code>, <code>Align</code>, and <code>Move</code> operations depends on the state of the [<code>Scale</code>](#opt-scale) checkbox in the [Options Section](#options-section), while the "Scale" operation alway active the scale option -it can be deactivate manually in the redo menu but there is no practical reason to do that-.

*   <span id="opt-flip">**<code>Flip</code>**</span>: Rotates the Previous gizmo 180 degrees around its Y-axis before applying the transformation. Primarily used when transforming between two faces (e.g., on different objects) that should end up facing each other, rather than aligned in the same direction.

*   <span id="opt-duplicate">**<code>Duplicate</code>**</span>: Duplicates the selected elements before transforming them. Works with [`Transform`](#op-transform), [`Align`](#op-align), [`Move`](#op-move), [`Move to`](#op-move-to-rotate-to-scale-to), [`Rotate to`](#op-move-to-rotate-to-scale-to).

*   <span id="redo-extrude-instance">**`Extrude` / `Instance` (Mode-Dependent Option)**</span>:
    *   **Function:** This option dynamically swaps between `Extrude` and `Instance` in the main option section and Redo menu based on the current mode.
    *   **In Edit Mode (shows `Extrude`):**  (for Mesh/Curve/Armature) Extrudes selected mesh components, curve points, or bones during the transformation instead of just moving them. Note: If both [`Extrude`](#opt-extrude) and [`Duplicate`](#opt-duplicate) are enabled, `Extrude` overrides `Duplicate`; the elements will be extruded, not duplicated then transformed. Works with [`Transform`](#op-transform), [`Align`](#op-align), [`Move`](#op-move), [`Move to`](#op-move-to-rotate-to-scale-to), [`Rotate to`](#op-move-to-rotate-to-scale-to).
    *   **In Object Mode (shows `Instance`):** Creates linked instances instead of full copies. The checkbox is visible, but only becomes active if `Duplicate` is also checked.

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

*   <span id="opt-use-instance">**<code>Use Instance</code>**</span>: When [`Duplicate`](#opt-duplicate) is checked, creates linked instances instead of full copies.

---

#### <span id="redo-last-section">6. Redo Last Menu (F9 Panel)</span>

After performing any transformation operation ([`Transform`](#op-transform), [`Move to`](#op-move-to-rotate-to-scale-to), etc.), a special menu becomes available. This menu allows you to interactively fine-tune the result of the last action without undoing and redoing the entire process.

**How to Access:**
*   Press `F9` immediately after an operation.
*   Or, click on the operator panel that appears in the bottom-left corner of the 3D Viewport.

This menu contains all the standard options from the main panel, plus several **exclusive settings** that offer even greater control.


### Exclusive Redo Last Settings

These advanced options are **only available in the Redo Last menu**:

*   <span id="redo-scale-axes">**`Individual Scale Axes (X, Y, Z)`**</span>:
    *   **Function:** These checkboxes allow you to enable or disable scaling on individual axes *after* the operation has been performed, providing precise non-uniform scaling control.
    *   **Availability:** These options become available when the `Scale` checkbox is active in the Redo Last menu. They are **enabled by default** when using the dedicated `Scale` or `Scale to` operators.
    *   **Critical Behavior Difference:** The axes they affect depend on the current mode:
        *   **In Object Mode:** Scaling is applied along the **object's own local X, Y, and Z axes**. This is standard non-uniform scaling, as an object's transform cannot be sheared directly.
        *   **In Edit Mode:** Scaling is applied along the **gizmo's custom axes**. This allows you to directly stretch and squash the selected geometry along any arbitrary orientation. Because this manipulates vertices directly, it can produce complex deformations and targeted shape changes that are impossible to achieve in Object Mode, which would require a combination of scaling and shearing.
    
*   <span id="opt-longest-arc">**<code>Longest Arc Path</code>**</span>: When checked, forces the underlying spiral path calculation (used for [`Interpolation`](#opt-interpolation-value) and repeated [`Count`](#opt-count) operations) to use the longer arc (>180 degrees) instead of the default shortest arc between the gizmo orientations.
    *   **Availability:** This setting only affects operations that include a rotational component (e.g., `Transform`, `Rotate`, `Align`, `Rotate to`). It has no effect and will be disabled for pure translation or scaling operations like `Move` or `Scale`.

*   <span id="redo-skip-last">**`Skip Last`**</span>:
    *   **Function:** Prevents the final iteration of a repeated (`Count` > 1) duplicate or extrusion from being created.
    *   **Use Case:** This is ideal for creating arrays or series of objects *between* a start and end point. For example, to place 5 fence posts between two main pillars, you would set the transformation `Count` to 6 (to define the 6 steps/gaps) and enable `Skip Last`. This creates the 5 intermediate posts without an unnecessary sixth one at the final destination.
    *   **Availability:** This checkbox is only active when either `Duplicate` or `Extrude` is enabled and the `Count` is set to a value greater than 1.

*   <span id="redo-scale-multiplier">**`Scale Multiplier`**</span>:
    *   **Function:** A slider that uniformly multiplies the final calculated scale of the entire transformation.
    *   **Details:** This acts as a global, final adjustment. It applies on top of any scale derived from the `Scale` checkbox, the `Scale`/`Scale to` operators, or even non-uniform scaling via the individual axis checkboxes.
    *   **Example:** If your operation results in a non-uniform scale of (2.0, 1.0, 1.0), setting the `Scale Multiplier` to 0.5 will result in a final scale of (1.0, 0.5, 0.5).

*   <span id="redo-complex-dup">**`Complex Dup`**</span>:
    *   **Function:** A more compatible, but slower, method for handling duplication.
    *   **When to Use:** The addon's default duplication is highly optimized for speed. However, for certain advanced scenarios, it may not correctly update relationships between the newly created objects. Enable `Complex Dup` if you encounter issues such as:
        *   Boolean modifiers on one duplicated object that need to target *another* object also being created in the same operation.
        *   Other complex modifier stacks that rely on inter-object relationships that fail to update correctly.
    *   **Performance Impact:** This mode uses Blender's standard operators to ensure maximum compatibility. This is significantly slower than the addon's default method, and the slowdown will be very noticeable when using a high `Count` or duplicating many elements at once. Use it only when necessary.

### Standard Options
The Redo Last menu also includes familiar settings like `Interpolation Value`, `Count`, `Duplicate`, `Instance`, `Flip`, etc. Changing these values will instantly update the result of your last transformation in the viewport, allowing for quick and iterative adjustments.

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

**Session Persistence:** All settings on the main panel, including those persistently set via the Redo menu, will reset to their defaults when you restart Blender.
