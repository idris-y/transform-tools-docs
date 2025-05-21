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
*   Using a single gizmo as a [custom orientation](#op-toggle-orientation) for standard Blender transform tools.
*   Executing [constrained transformations](#constrained-transforms-section), where elements move or rotate from the 3D cursor position towards a computed intersection point with target geometry (a point, line, or plane) derived from the gizmos.

**Core Workflow:** The fundamental process involves:

1.  **Defining** the 'Previous' gizmo (representing the start state).
2.  **Defining** the 'Active' gizmo (representing the end state).
3.  Selecting the elements to modify.
4.  **Applying** a transformation operation that uses the defined gizmos (like [<code>Transform</code>](#op-transform), [<code>Align</code>](#op-align), [<code>Move</code>](#op-move), or [Constrained Transforms](#constrained-transforms-section)).

**Key Concepts:**

*   Gizmos definition sources: [selections](#op-get-from-selected), [interactive clicking](#op-create), [3D cursor state](#op-get-from-selected).
*   Active gizmo can serve as a custom orientation for standard Blender tools via the [<code>Toggle Orientation</code>](#op-toggle-orientation) operator.
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

*   <span id="op-create" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Create</span>: Interactively define the Active gizmo by clicking its origin, X-axis, and Y-axis endpoints in the 3D view.
    *   *How it works:* This process uses the 3D Cursor system to determine where your clicks land in 3D space. The behavior depends on the [<code>Snap</code>](#opt-snap) toggle below: if active, it uses the addon's specific snapping settings; if inactive, it respects Blender's current header snapping settings.
    *   *Cancellation & Validation:*
        *   You can cancel the placement of the origin or axes using `ESC` or `Right Mouse Button`.
        *   If you cancel placing the **origin**, its position reverts based on the setting below.
        *   If you cancel placing an **axis** endpoint (X or Y), or if the manually placed axis is invalid (too short or aligned with a previous axis), that axis will be **automatically created** using a reference orientation determined by the setting below.
        *   This fallback ensures a valid gizmo is always created.
    *   *Reference State for Cancellation/Auto-Creation:* The specific state used for origin cancellation revert and the orientation used for automatic axis creation depends on the [**<code>3D Cursor Orientation</code>**](#opt-cursor-orientation) setting (see description below).
    *   *Final Cursor Update:* This operation always updates the 3D cursor's location and rotation upon successful completion, regardless of other settings.

*   <span id="op-get-from-selected" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Get from selected</span>: Creates/updates gizmo states based on the current selection. The behavior differs depending on the mode and selection count:
    *   **Object Mode (1 object selected OR >2 objects selected):**
        *   Defines the **Active gizmo** using the transform of the single active object (if 1 selected) or the average transform (location, rotation, uniform scale factor) of all selected objects (if >2 selected).
        *   The **Previous gizmo** is *only* updated if the [<code>Auto-Update Gizmos</code>](#opt-auto-update) option is enabled.
    *   **Object Mode (Exactly 2 objects selected):**
        *   Defines **both gizmos directly**, regardless of the [`Auto-Update Gizmos`](#opt-auto-update) setting:
            *   The **Previous gizmo** is defined using the transform of the non-active selected object (or the first object selected if neither is the active object).
            *   The **Active gizmo** is defined using the transform of the active selected object (or the second object selected if neither is the active object).
    *   **Edit Mode (Mesh):**
        *   Defines the **Active gizmo** based on the selected component(s).
        *   *Face:* Uses normal (Z), first edge (X/Y), center, and area (uniform scale).
        *   *Edge:* Uses direction (X), connected face normals (Z), center, and length (uniform scale).
        *   *Vertex/Multiple Elements:* Uses average location/normal and default uniform scale.
    *   **Edit Mode (Curve/Armature):**
        *   Defines the **Active gizmo** using location/rotation of selected points/bones. Scale factor from screen space.
    *   **Nothing Selected:**
        *   Defines the **Active gizmo** using the **3D Cursor's current transform state**. *Tip: Position the 3D Cursor precisely first (using [snapping](#sec-cursor-snapping) if needed) for accurate gizmo definition.*

*   <span id="opt-auto-update">![Auto-Update Gizmos Icon](assets/icons/Auto-Update_Gizmos.png) **Auto-Update Gizmos**</span>: If enabled, whenever the **Active Gizmo** is defined or updated (e.g., via [`Create`](#op-create), [`Get from selected`](#op-get-from-selected), [`Paste`](#op-paste)), the **Previous Gizmo** automatically takes the state of the **former Active Gizmo**. This ensures the two gizmos usually represent the 'before' and 'after' states of the last operation.
    *   *Note:* This setting is overridden and has no effect when using [`Get from selected`](#op-get-from-selected) with exactly 2 objects, as both gizmos are defined directly from the selected objects in that specific case.

*   <span id="opt-snap">![Snap Icon](assets/icons/Snap.png) **Snap**</span>: When enabled, this forces the interactive [`Create`](#op-create) operation to use a specific, pre-defined set of snapping settings, **overriding** whatever snapping settings are currently active in Blender's 3D View header. When disabled, [`Create`](#op-create) respects Blender's current header snapping settings.
    *   *Addon's Internal Snap Behavior (When this toggle is ON):*
        *   Turns snapping **ON**.
        *   Sets snapping targets to **Vertex, Edge, Face, and Edge Midpoint**.
        *   Aligns the placed point's **rotation** to the snapped surface normal.
        *   Allows snapping to the **object being edited** (self-snapping).
        *   Ensures snapping works in both **Edit Mode and Object Mode**.
    *   *Refer to the ["Working with the 3D Cursor and Snapping"](#sec-cursor-snapping) section above for general information on Blender's snapping system.*

*   <span id="opt-cursor-orientation">![3D Cursor Orientation Icon](assets/icons/3D_Cursor_Orientation.png) **3D Cursor Orientation**</span>: This option primarily controls the reference **orientation and scale** used by the interactive [`Create`](#op-create) operation when handling origin cancellation or performing automatic axis creation/alignment. The new gizmo's **origin location** will always be based on the 3D Cursor's current location if origin placement is cancelled.
    1.  *Primary Effect (Reference for <code>Create</code>'s Orientation & Scale during Auto-Creation/Cancellation):*
        *   **When Enabled:** If an axis needs to be auto-created or cancelled, the [`Create`](#op-create) operation uses the current **3D Cursor's orientation** and derives a **scale factor from the screen space zoom level** as the reference.
        *   **When Disabled:** If an axis needs to be auto-created or cancelled, the [`Create`](#op-create) operation uses the **orientation and scale** from the **former Active Gizmo** (the one active before [`Create`](#op-create) began) as the reference.
    2.  *Secondary Effect (Updating the 3D Cursor by other operations):*
        *   **When Enabled:** Allows certain other addon operations (like [`Get from selected`](#op-get-from-selected)) to update the 3D cursor's location and rotation.
        *   **When Disabled:** These other addon operations will not automatically update the 3D cursor.
        *   *(Note: The interactive [`Create`](#op-create) operation *always* updates the 3D cursor's location and rotation upon successful completion, regardless of this setting).*

*   <span id="op-swap" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Swap</span>: Swaps the transform components (location, rotation, scale) of the Previous and Active gizmos. Affected components depend on the [Swap/Paste Mode Dropdown](#opt-swap-paste-mode).

*   <span id="op-axes-cycling">![Axes Cycling Icon](assets/icons/Axes_Cycling.png) **Axes Cycling**</span>: Rotates the meaning of the Active gizmo's axes (X becomes Z, Y becomes X, Z becomes Y).

*   <span id="op-rotate-gizmo">![Rotate Icon](assets/icons/Rotate.png) **Rotate**</span>: Rotates the Active gizmo 90 degrees around its local X-axis.

*   <span id="op-copy" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Copy</span>: Copies the current transform components of the Active gizmo.

*   <span id="op-paste" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Paste</span>: Pastes the copied transform components onto the Active gizmo. Affected components depend on the [Swap/Paste Mode Dropdown](#opt-swap-paste-mode).

*   <span id="opt-swap-paste-mode" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Swap/Paste Mode Dropdown</span> (<code>Gizmo</code>/<code>Location</code>/<code>Rotation</code>/<code>Scale</code>): Controls which transform components (full state, location only, rotation only, or scale only) are affected by the [`Swap`](#op-swap) or [`Paste`](#op-paste) operations.

*   <span id="op-undo-redo">![Undo Icon](assets/icons/Undo.png) ![Redo Icon](assets/icons/Redo.png) **Undo/Redo Gizmos**</span>: Undo or redo changes made to the gizmo states themselves.

*   <span id="op-toggle-orientation" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Toggle Orientation</span>: Switches Blender's Transform Orientation between the Active gizmo and the previously used standard orientation. This allows standard Blender operators like Move, Rotate, Scale, etc. to use the Active gizmo's precise alignment.

*   <span id="opt-fixed-size">![Fixed Visual Size Icon](assets/icons/Fixed_Visual_Size.png) **Fixed Visual Size**</span>: Keeps the visual size of the gizmos consistent on screen as you zoom in or out. Doesn't affect transformations.

*   <span id="opt-visibility">![Gizmos Visibility All Icon](assets/icons/Show_all_gizmos.png) **Gizmos Visibility**</span>: Cycles through the visibility states for the Previous and Active gizmos. Clicking the icon toggles between: showing both gizmos ![Gizmos Visibility All Icon](assets/icons/Show_all_gizmos.png), showing only the Active gizmo ![Gizmos Visibility Active Icon](assets/icons/Show_only_the_active_gizmo.png), or hiding both gizmos ![Gizmos Visibility Hidden Icon](assets/icons/Hide_all_gizmos.png).

#### <span id="transformation-section">2. Transformation Section</span>

*   <span id="op-transform" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Transform</span>: Performs a full location and rotation transformation on the selected elements, moving them from the Previous gizmo's state to the Active gizmo's state. Behavior modified by [Options Section](#options-section) settings like [`Scale`](#opt-scale) and [`Flip`](#opt-flip).
*   <span id="op-align" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Align</span>: Transforms the selection using only the location and the selected axis (<code>X</code>/<code>Y</code>/<code>Z</code>) of the gizmos. Select the axis to align with using the dropdown. Behavior modified by [Options Section](#options-section) settings like [`Scale`](#opt-scale) and [`Flip`](#opt-flip).
*   <span id="op-move" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Move</span>: Moves the selection from the location of the Previous gizmo to the location of the Active gizmo, ignoring rotation. Behavior modified by [Options Section](#options-section) settings like [`Scale`](#opt-scale) and [`Flip`](#opt-flip).
*   ***Note on Scale:*** Whether the scale component of the gizmos is included in the <code>Transform</code>, <code>Align</code>, and <code>Move</code> operations depends on the state of the [<code>Scale</code>](#opt-scale) checkbox in the [Options Section](#options-section).
*   *Requirement:* These buttons are disabled if both a Previous and Active gizmo haven't been defined.

#### <span id="constrained-transforms-section">3. Constrained Transforms Section</span>

Perform transformations where selections move or rotate along defined directions towards a target, originating **from the 3D Cursor's current position**.

*   *How it works:* These operations calculate the transformation starting from the 3D Cursor's location. The direction of movement/rotation and the target geometry (point, line, plane) are determined by the settings chosen in this section, which reference the Previous and Active gizmo states.
    *   ***Important:*** *Ensure the 3D Cursor is positioned correctly **before** applying these operations. Refer to the ["Working with the 3D Cursor and Snapping"](#sec-cursor-snapping) section near the beginning of the documentation for positioning techniques.*

*   <span id="op-move-to-rotate-to">
        <span style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Move to</span>
        <span style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Rotate to</span>
    </span>: Buttons to enable/disable the respective constrained transform mode.
    *   <code>Move to</code>: Moves the selection starting from the 3D Cursor location along the [`Constrain direction`](#opt-constrain-direction) until it hits or reaches the closest possible point to the [`Target`](#opt-target).
    *   <code>Rotate to</code>: Rotates the selection starting from the 3D Cursor location around the specified [`Constrain direction`](#opt-constrain-direction) axis (relative to the Previous gizmo) until it hits or reaches the closest possible point to the [`Target`](#opt-target).
*   <span id="op-apply-constrained" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Apply</span>: Executes the enabled [`Move to`](#op-move-to-rotate-to) or [`Rotate to`](#op-move-to-rotate-to) operation. Behavior modified by [Options Section](#options-section) settings.

*   <span id="opt-constrain-direction">**Constrain direction:**</span> Defines the direction (for <code>Move to</code>) or the axis of rotation (for <code>Rotate to</code>) originating from the 3D Cursor.
    *   *Type (<code>Auto</code>/<code>Line</code>/<code>Plane</code>):* Defines how the constraint direction/axis is determined. Based on the <strong>Previous</strong> gizmo's orientation.
        *   **<code>Auto</code>:** Automatically calculates the optimal path in 3D space to reach the target geometry, or the closest possible point to it:
            *   For <code>Move to</code>, calculates the <strong>direction</strong> resulting in the <strong>shortest distance</strong> to the target. This path **guarantees hitting the target** (or closest reachable point).
            *   For <code>Rotate to</code>, calculates the <strong>rotation axis</strong> resulting in the <strong>smallest angle</strong> of rotation needed to reach the target, or the closest possible state to it.
        *   <code>Line</code>: Constrains movement/rotation *along* the selected <code>Axis</code> of the Previous gizmo.
        *   <code>Plane</code>: Uses the selected <code>Axis</code> as the normal vector for the constraint plane. Automatically calculates the optimal path confined to this plane:
            *   For <code>Move to</code>, calculates the direction **within the plane** (perpendicular to the normal <code>Axis</code>) that results in the shortest distance to reach the target, **or the closest point to it**.
            *   For <code>Rotate to</code>, calculates the optimal **rotation axis lying within the plane** (perpendicular to the normal <code>Axis</code>) that results in the **smallest angle** of rotation needed to reach the target, **or the closest point to it**.
            *   This calculated path within the plane <strong>guarantees reaching the target intersection</strong> (or the closest reachable point).
    *   *Axis (<code>X</code>/<code>Y</code>/<code>Z</code>):* Selects the specific axis (X, Y, or Z of the <strong>Previous</strong> gizmo) to use when the <em>Type</em> is set to <code>Line</code> or <code>Plane</code>. Disabled for <code>Auto</code>.
*   <span id="opt-target">**Target:**</span>
    *   *Type (<code>Center</code>/<code>Line</code>/<code>Plane</code>):* Defines the target shape (point, line, or plane). Based on the <strong>Active</strong> gizmo.
    *   *Direction (<code>X</code>/<code>Y</code>/<code>Z</code>):* Selects the specific axis (X, Y, or Z of the <strong>Active</strong> gizmo) to define the <code>Line</code> or <code>Plane</code> target. Disabled for <code>Center</code>.
*   <span id="opt-snap-cursor-constrained">**<code>Snap Cursor</code>:**</span> This checkbox primarily affects operations within the **Constrained Transforms Section**:
    1.  **Setup Behavior:** When enabled, selecting constraint types (<code>Auto</code>/<code>Line</code>/<code>Plane</code>) or target axes/directions automatically **snaps the 3D Cursor to relevant geometry** (e.g., axis origins, target points). This helps precisely set the starting point for the [`Move To`/`Rotate To`](#op-move-to-rotate-to) operations *before* they are applied.
    2.  **Post-Operation Behavior:** By default (when <code>Snap Cursor</code> is unchecked), the [`Move to`](#op-move-to-rotate-to) and [`Rotate to`](#op-move-to-rotate-to) operations **do not** update the 3D Cursor's position/rotation after execution, preserving the original starting point. However, **if <code>Snap Cursor</code> is enabled, [`Move to`](#op-move-to-rotate-to) and [`Rotate to`](#op-move-to-rotate-to) <em>will</em> update the 3D Cursor's location and rotation** after they complete (typically placing the cursor at the final transformed location or target point reached). This makes their cursor update behavior consistent with how other transformation operations might behave (depending on the [`3D Cursor Orientation`](#opt-cursor-orientation) setting).
*   <span id="opt-limit-to-line">**<code>Limit to Line</code>:**</span> If checked (and using <code>Line</code> target), rotation stops at the ends of the line segment defined by the gizmo axis.

#### <span id="options-section">4. Options Section</span>

Modify the behavior of transformation operations like [`Transform`](#op-transform), [`Align`](#op-align), [`Move`](#op-move), and the [Constrained Transforms](#constrained-transforms-section).

*   <span id="opt-scale">**<code>Scale</code>**</span>: Include the uniform scale factor difference between the gizmos in the transformation calculations for operations in the [Transformation Section](#transformation-section). If unchecked, the scale component of the gizmos is ignored, and only location and rotation differences are applied.
*   <span id="opt-flip">**<code>Flip</code>**</span>: Rotates the Previous gizmo 180 degrees around its Y-axis before applying the transformation. Primarily used when transforming between two faces (e.g., on different objects) that should end up facing each other, rather than aligned in the same direction.
*   <span id="opt-duplicate">**<code>Duplicate</code>**</span>: Duplicates the selected elements before transforming them. Works with [`Transform`](#op-transform), [`Align`](#op-align), [`Move`](#op-move), [`Move to`](#op-move-to-rotate-to), [`Rotate to`](#op-move-to-rotate-to).
*   <span id="opt-extrude">**<code>Extrude</code>**</span>: (Edit Mode Only for Mesh/Curve/Armature) Extrudes selected mesh components, curve points, or bones during the transformation instead of just moving them. Note: If both [`Extrude`](#opt-extrude) and [`Duplicate`](#opt-duplicate) are enabled, `Extrude` overrides `Duplicate`; the elements will be extruded, not duplicated then transformed. Works with [`Transform`](#op-transform), [`Align`](#op-align), [`Move`](#op-move), [`Move to`](#op-move-to-rotate-to), [`Rotate to`](#op-move-to-rotate-to).
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
*   <span id="opt-longest-arc">**<code>Longest Arc Path</code>**</span>: When checked, forces the underlying spiral path calculation (used for [`Interpolation`](#opt-interpolation-value) and repeated [`Count`](#opt-count) operations) to use the longer arc (>180 degrees) instead of the default shortest arc between the gizmo orientations.