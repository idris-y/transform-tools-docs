# Transform Tools Addon - Description and Documentation

---

## Description

### Transform Tools: Advanced Gizmo Control & Precision Transformations

Transform Tools enhances object manipulation in Blender using **custom 3D gizmos** for precise transformation control. It works closely with Blender's core tools.

#### Core Features:

*   **Flexible Gizmo Definition:** Define transformation origins and targets accurately. Create gizmos automatically from selections, interactively using Blender's 3D cursor.
*   **Dual-Gizmo Transformations:** Apply a transformation between the two gizmo states, using modes for full transform (location, rotation, uniform scale).
*   **Independent Custom Orientation:** Use the Active gizmo as a custom transform orientation for standard Blender transform tools like Move, Rotate, Scale, etc.

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

*   Gizmos definition sources: [interactive clicking](#op-create).
*   Active gizmo can serve as a custom orientation for standard Blender tools via the [<code>Cursor Pivot</code>](#op-cursor-pivot) operator.
*   Utilizes Blender's 3D Cursor for interactive placement and as the origin for [Constrained Transforms](#constrained-transforms-section).

### Installation

1.  Go to `Edit > Preferences > Add-ons`.
2.  Click `Install` and select the downloaded addon `.zip` file.
3.  Find "Transform Tools" in the list and enable the checkbox.
4.  The panel will appear in the 3D View's Sidebar (N-Panel) under the "Transform Tools" tab (or your chosen short category name).

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

#### <span id="gizmo-section">1. Operations Section</span>
*   <span id="op-create" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Create</span>: Interactively define the Active gizmo by clicking its origin, X-axis, and Y-axis endpoints in the 3D view.
    *   *How it works:* This process uses the 3D Cursor system to determine where your clicks land in 3D space. The behavior depends on the [<code>Snap</code>](#opt-snap) toggle below: if active, it uses the addon's specific snapping settings; if inactive, it respects Blender's current header snapping settings.
    *   *Cancellation & Validation:*
        *   You can cancel the placement of the origin or axes using `ESC` or `Right Mouse Button`.
        *   If you cancel placing the **origin**, its position reverts based on the setting below.
        *   If you cancel placing an **axis** endpoint (X or Y), or if the manually placed axis is invalid (too short or aligned with a previous axis), that axis will be **automatically created** using a reference orientation determined by the setting below.
        *   This fallback ensures a valid gizmo is always created.
    *   *Reference State for Cancellation/Auto-Creation:* The specific state used for origin cancellation revert and the orientation used for automatic axis creation depends on the [**<code>3D Cursor Orientation</code>**](#opt-cursor-orientation) setting (see description below).
    *   *Final Cursor Update:* This operation always updates the 3D cursor's location and rotation upon successful completion, regardless of other settings.

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

*   <span id="op-undo-redo">![Undo Icon](assets/icons/Undo.png) ![Redo Icon](assets/icons/Redo.png) **Undo/Redo Gizmos**</span>: Undo or redo changes made to the gizmo states themselves.

*   <span id="op-cursor-pivot" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Cursor Pivot</span>: Switches Blender's Transform Orientation between the Active gizmo and the previously used standard orientation. This allows standard Blender operators like Move, Rotate, Scale, etc. to use the Active gizmo's precise alignment.

*   <span id="opt-fixed-size">![Fixed Visual Size Icon](assets/icons/Fixed_Visual_Size.png) **Fixed Visual Size**</span>: Keeps the visual size of the gizmos consistent on screen as you zoom in or out. Doesn't affect transformations.

*   <span id="opt-visibility">![Gizmos Visibility All Icon](assets/icons/Show_all_gizmos.png) **Gizmos Visibility**</span>: Cycles through the visibility states for the Previous and Active gizmos. Clicking the icon toggles between: showing both gizmos ![Gizmos Visibility All Icon](assets/icons/Show_all_gizmos.png), showing only the Active gizmo ![Gizmos Visibility Active Icon](assets/icons/Show_only_the_active_gizmo.png), or hiding both gizmos ![Gizmos Visibility Hidden Icon](assets/icons/Hide_all_gizmos.png).

*   <span id="op-transform" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Transform</span>: Performs a full location and rotation transformation on the selected elements, moving them from the Previous gizmo's state to the Active gizmo's state. Behavior modified by [Options Section](#options-section) settings like [`Scale`](#opt-scale) and [`Flip`](#opt-flip).
---

#### <span id="options-section">4. Options Section</span>

Modify the behavior of the [`Transform`](#op-transform) operation.

*   <span id="opt-scale">**<code>Scale</code>**</span>: Include the uniform scale factor difference between the gizmos in the transformation calculations for operations in the [Transformation Section](#transformation-section). If unchecked, the scale component of the gizmos is ignored, and only location and rotation differences are applied.
*   ***Note on Scale:*** Whether the scale component of the gizmos is included in the <code>Transform</code> operation depends on the state of the [<code>Scale</code>](#opt-scale) checkbox in the [Options Section](#options-section).

*   <span id="opt-flip">**<code>Flip</code>**</span>: Rotates the Previous gizmo 180 degrees around its Y-axis before applying the transformation. Primarily used when transforming between two faces (e.g., on different objects) that should end up facing each other, rather than aligned in the same direction.

*   <span id="opt-duplicate">**<code>Duplicate</code>**</span>: Duplicates the selected elements before transforming them. Works with [`Transform`](#op-transform), [`Align`](#op-align), [`Move`](#op-move), [`Move to`](#op-move-to-rotate-to-scale-to), [`Rotate to`](#op-move-to-rotate-to-scale-to).

*   <span id="opt-extrude">**<code>Extrude</code>**</span>: (Edit Mode Only for Mesh/Curve/Armature) Extrudes selected mesh components, curve points, or bones during the transformation instead of just moving them. Note: If both [`Extrude`](#opt-extrude) and [`Duplicate`](#opt-duplicate) are enabled, `Extrude` overrides `Duplicate`; the elements will be extruded, not duplicated then transformed. Works with [`Transform`](#op-transform), [`Align`](#op-align), [`Move`](#op-move), [`Move to`](#op-move-to-rotate-to-scale-to), [`Rotate to`](#op-move-to-rotate-to-scale-to).

*   <span id="redo-extrude-instance">**`Extrude` / `Instance` (Mode-Dependent Option)**</span>:
    *   **Function:** This option dynamically swaps between `Extrude` and `Instance` in the main option section and Redo menu based on the current mode.
    *   **In Edit Mode (shows `Extrude`):** Turns the last operation into an extrusion.
    *   **In Object Mode (shows `Instance`):** Creates linked instances instead of full copies. The checkbox is visible, but only becomes active if `Duplicate` is also checked.
    *   **Note:** If both `Extrude` and `Duplicate` are enabled, `Extrude` takes precedence.

*   <span id="opt-use-instance">**<code>Use Instance</code>**</span>: When [`Duplicate`](#opt-duplicate) is checked, creates linked instances instead of full copies.

---

#### <span id="redo-last-section">6. Redo Last Menu (F9 Panel)</span>

After performing the [`Transform`](#op-transform) operation, a special menu becomes available. This menu allows you to interactively fine-tune the result of the last action without undoing and redoing the entire process.

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
The Redo Last menu also includes familiar settings (`Scsle`, `Duplicate`, `Instance`/`Extrude`, `Flip`). Changing these values will instantly update the result of your last transformation in the viewport, allowing for quick and iterative adjustments.

---

**Session Persistence:** All settings on the main panel, including those persistently set via the Redo menu, will reset to their defaults when you restart Blender.
