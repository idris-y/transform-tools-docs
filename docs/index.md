# Transform Tools Addon - Description and Documentation

---

## Blendermarket Description

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

*   Transforming elements between two explicitly defined transform states using dual gizmos.
*   Using a single gizmo as a custom orientation for standard Blender transform tools.
*   Executing constrained transformations, where elements move or rotate from the 3D cursor position towards a computed intersection point with target geometry (a point, line, or plane) derived from the gizmos.

**Core Workflow:** The fundamental process involves:

1.  **Defining** the 'Previous' gizmo (representing the start state).
2.  **Defining** the 'Active' gizmo (representing the end state).
3.  Selecting the elements to modify.
4.  **Applying** a transformation operation that uses the defined gizmos.

**Key Concepts:**

*   Gizmos definition sources: selections, interactive clicking, 3D cursor state.
*   Active gizmo can serve as a custom orientation for standard Blender tools via the `Toggle Orientation` operator.
*   Utilizes Blender's 3D Cursor for interactive placement and as the origin for Constrained Transforms.

### Installation

1.  Go to `Edit > Preferences > Add-ons`.
2.  Click `Install` and select the downloaded addon `.zip` file.
3.  Find "Transform Tools" in the list and enable the checkbox.
4.  The panel will appear in the 3D View's Sidebar (N-Panel) under the "Transform Tools" tab.

---

### Panel Sections

#### 1. Gizmo Section

This section focuses on defining, manipulating, and managing the core gizmos.

*   **`Create`:** Interactively define the Active gizmo by clicking its origin, X-axis, and Y-axis endpoints in the 3D view.
    *   *How it works:* This process uses the 3D Cursor system to determine where your clicks land in 3D space, respecting Blender's snapping settings and cursor depth.
    *   *Cancellation & Validation:*
        *   You can cancel the placement of the origin or axes using `ESC` or `Right Mouse Button`.
        *   If you cancel placing the **origin**, its position reverts based on the setting below.
        *   If you cancel placing an **axis** endpoint (X or Y), or if the manually placed axis is invalid (too short or aligned with a previous axis), that axis will be **automatically created** using a reference orientation determined by the setting below.
        *   This fallback ensures a valid gizmo is always created.
    *   *Reference State for Cancellation/Auto-Creation:* The specific state used for origin cancellation revert and the orientation used for automatic axis creation depends on the **`(Icon) 3D Cursor Orientation`** setting (see description below).
    *   *Final Cursor Update:* This operation always updates the 3D cursor's location and rotation upon successful completion, regardless of other settings.

*   **<code>Get from selected</code>:** Creates/updates gizmo states based on the current selection. The behavior differs depending on the mode and selection count:
    *   **Object Mode (1 object selected OR >2 objects selected):**
        *   Defines the **Active gizmo** using the transform of the single active object (if 1 selected) or the average transform (location, rotation, uniform scale factor) of all selected objects (if >2 selected).
        *   The **Previous gizmo** is *only* updated if the `(Icon) Auto-Update Gizmos` option is enabled.
    *   **Object Mode (Exactly 2 objects selected):**
        *   Defines **both gizmos directly**, regardless of the `Auto-Update Gizmos` setting:
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
        *   Defines the **Active gizmo** using the 3d cursor's current transform state.

*   **(Icon) `Auto-Update Gizmos`:** (Parent icon) If enabled, whenever the **Active Gizmo** is defined or updated (e.g., via `Create`, `Get from selected`, `Paste`), the **Previous Gizmo** automatically takes the state of the **former Active Gizmo**. This ensures the two gizmos usually represent the 'before' and 'after' states of the last operation.
    *   *Note:* This setting is overridden and has no effect when using `Get from selected` with exactly 2 objects, as both gizmos are defined directly from the selected objects in that specific case.

*   **<code>(Icon) `Snap`</code>:** (Snap icon) Use the addon's snapping settings instead of current Blender's snap settings during interactive gizmo creation (<code>Create</code>).

*   **(Icon) `3D Cursor Orientation`:** (Cursor icon) This option controls the reference source used by the interactive `Create` operation when handling origin cancellation or performing automatic axis creation/alignment.
    1.  *Primary Effect (Reference for <code>Create</code>):*
        *   **When Enabled:** The `Create` operation uses the current **3D Cursor's state** (location and rotation) as the reference.
        *   **When Disabled:** The `Create` operation uses the state of the **former Active Gizmo** (the one active before <code>Create</code> began) as the reference.
    2.  *Secondary Effect (Updating the 3D Cursor):*
        *   **When Enabled:** Allows certain other addon operations (like `Get from selected`) to update the 3D cursor's location and rotation.
        *   **When Disabled:** These addon operations will not automatically update the 3d cursor.
        *   *(Note: The interactive `Create` operation *always* updates the 3d cursor upon completion, regardless of this setting).*

*   **<code>Swap</code>:** Swaps the transform components (location, rotation, scale) of the Previous and Active gizmos.

*   **(Icon) Axes Cycling:** (Gimbal icon) Rotates the meaning of the Active gizmo's axes (X becomes Z, Y becomes X, Z becomes Y).

*   **(Icon) Rotate:** (Driver icon) Rotates the Active gizmo 90 degrees around its local X-axis.

*   **<code>Copy</code>:** Copies the current transform components of the Active gizmo.

*   **<code>Paste</code>:** Pastes the copied transform components onto the Active gizmo.

*   **Swap/Paste Mode Dropdown** (<code>Gizmo</code>/<code>Location</code>/<code>Rotation</code>/<code>Scale</code>): Controls which transform components (full state, location only, rotation only, or scale only) are affected by the <code>Swap</code> or <code>Paste</code> operations.

*   **(Icons) Undo/Redo Gizmos:** (Loop Back/Forward icons) Undo or redo changes made to the gizmo states themselves.

*   **<code>Toggle Orientation</code>:** Switches Blender's Transform Orientation between the Active gizmo and the previously used standard orientation. This allows standard Blender operators like Move, Rotate, Scale, etc. to use the Active gizmo's precise alignment.

*   **(Icon) `Fixed Visual Size`:** (Fixed Size icon) Keeps the visual size of the gizmos consistent on screen as you zoom in or out. Doesn't affect transformations.

*   **(Icon) `Gizmos Visibility`:** (Eye icons) Cycles through showing both gizmos, only the Active gizmo, or hiding both.

#### 2. Transformation Section

These operations use both the Previous and Active gizmos to transform selections between the two defined states.

*   **<code>Transform</code>:** Performs a full location and rotation transformation on the selected elements, moving them from the Previous gizmo's state to the Active gizmo's state.
*   **<code>Align</code>:** Transforms the selection using only the location and the selected axis (<code>X</code>/<code>Y</code>/<code>Z</code>) of the gizmos. Select the axis to align with using the dropdown.
*   **<code>Move</code>:** Moves the selection from the location of the Previous gizmo to the location of the Active gizmo, ignoring rotation.
*   ***Note on Scale:*** Whether the scale component of the gizmos is included in the <code>Transform</code>, <code>Align</code>, and <code>Move</code> operations depends on the state of the <code>Scale</code> checkbox in the **Options Section**.
*   *Requirement:* These buttons are disabled if both a Previous and Active gizmo haven't been defined.

#### 3. Constrained Transforms Section

Perform transformations where selections move or rotate along defined directions towards a target, originating from the 3D Cursor.

*   *How it works:* These operations calculate the transformation starting from the 3D Cursor's current location. The direction of movement/rotation and the target geometry (point, line, plane) are determined by the settings chosen in this section, which reference the Previous and Active gizmo states.
*   **<code>Move to</code> / <code>Rotate to</code>:** Buttons to enable/disable the respective constrained transform mode.
    *   <code>Move to</code>: Moves the selection starting from the 3D Cursor location along the <code>Constrain direction</code> until it hits or reaches the closest possible point to the target.
    *   <code>Rotate to</code>: Rotates the selection starting from the 3D Cursor location around the specified <code>Constrain direction</code> axis (relative to the Previous gizmo) until it hits or reaches the closest possible point to the target.
*   **<code>Apply</code>:** Executes the enabled <code>Move to</code> or <code>Rotate to</code> operation.
*   **Constrain direction:** Defines the direction (for <code>Move to</code>) or the axis of rotation (for <code>Rotate to</code>) originating from the 3D Cursor.
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
*   **Target:**
    *   *Type (<code>Center</code>/<code>Line</code>/<code>Plane</code>):* Defines the target shape (point, line, or plane). Based on the <strong>Active</strong> gizmo.
    *   *Direction (<code>X</code>/<code>Y</code>/<code>Z</code>):* Selects the specific axis (X, Y, or Z of the <strong>Active</strong> gizmo) to define the <code>Line</code> or <code>Plane</code> target. Disabled for <code>Center</code>.
*   **<code>Snap Cursor</code>:** This checkbox primarily affects operations within the **Constrained Transforms Section**:
    1.  **Setup Behavior:** When enabled, selecting constraint types (<code>Auto</code>/<code>Line</code>/<code>Plane</code>) or target axes/directions automatically **snaps the 3D Cursor to relevant geometry** (e.g., axis origins, target points). This helps precisely set the starting point for the <code>Move To</code>/<code>Rotate To</code> operations *before* they are applied.
    2.  **Post-Operation Behavior:** By default (when <code>Snap Cursor</code> is unchecked), the <code>Move to</code> and <code>Rotate to</code> operations **do not** update the 3D Cursor's position/rotation after execution, preserving the original starting point. However, **if <code>Snap Cursor</code> is enabled, <code>Move to</code> and <code>Rotate to</code> <em>will</em> update the 3D Cursor's location and rotation** after they complete (typically placing the cursor at the final transformed location or target point reached). This makes their cursor update behavior consistent with how other transformation operations might behave (depending on the <code>(Icon) 3D Cursor Orientation</code> setting).
*   **<code>Limit to Line</code>:** If checked (and using <code>Line</code> target), rotation stops at the ends of the line segment defined by the gizmo axis.

#### 4. Options Section

Modify the behavior of transformation operations (<code>Transform</code>, <code>Align</code>, <code>Move</code>, <code>Move to</code>, <code>Rotate to</code>).

*   **<code>Scale</code>:** Include the **uniform scale factor** difference between the gizmos in the transformation calculations for operations in the **Transformation Section** (<code>Transform</code>, <code>Align</code>, <code>Move</code>). If unchecked, the scale component of the gizmos is ignored, and only location and rotation differences are applied.
*   **<code>Flip</code>:** Rotates the Previous gizmo 180 degrees around its Y-axis before applying the transformation. Primarily used when transforming between two faces (e.g., on different objects) that should end up facing each other, rather than aligned in the same direction.
*   **<code>Duplicate</code>:** Duplicates the selected elements before transforming them.
*   **<code>Extrude</code>:** (Edit Mode Only for Mesh/Curve/Armature) Extrudes selected mesh components, curve points, or bones during the transformation instead of just moving them. Note: If both <code>Extrude</code> and <code>Duplicate</code> are enabled, <code>Extrude</code> overrides <code>Duplicate</code>; the elements will be extruded, not duplicated then transformed.
*   **<code>Interpolation Value</code>:** Blends between the start (0.0) and end (1.0) transform states. The addon calculates the smooth spiral path between the two gizmo states required to get from the Previous to the Active state (using the shortest arc by default). This slider interpolates the transformation along that calculated path. Values outside the 0.0-1.0 range extrapolate along the path (negative values move in the opposite direction). *Values outside the 0 to 1 slider range must be entered manually.*
*   **(Icon) <code>Lock Interpolation</code>:** (Lock/Unlock icon) If checked, the final transformation respects the <code>Interpolation Value</code> even when <code>Count</code> > 1. If unchecked, <code>Count</code> repeats the <em>full</em> (1.0) transformation multiple times.
*   **<code>Count</code>:** Repeats the calculated transformation multiple times.
*   **<code>Use Instance</code>:** When <code>Duplicate</code> is checked, creates linked instances instead of full copies.
*   **<code>Longest Arc Path</code>:** When checked, forces the underlying spiral path calculation (used for <code>Interpolation</code> and repeated <code>Count</code> operations) to use the longer arc (>180 degrees) instead of the default shortest arc between the gizmo orientations.
