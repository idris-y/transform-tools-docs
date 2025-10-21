# Transform Tools Addon - Lite Version

---

## Description

### Transform Tools: Enhanced Gizmo Control & Precision Transformations

Transform Tools enhances object manipulation in Blender using **custom 3D gizmos** for precise transformation control. It works closely with Blender's core tools.

#### Core Features:

*   **Flexible Gizmo Definition:** Define transformation origins and targets accurately. Create gizmos automatically from selections, interactively using Blender's 3D cursor, or update them based on existing elements.
*   **Dual-Gizmo Transformations:** Apply a transformation between the two gizmo states, using modes for full transform (location, rotation, scale), specific axis alignment, or location only.
*   **Independent Custom Orientation:** Use the Active gizmo as a custom transform orientation for standard Blender transform tools like Move, Rotate, Scale, etc.
*   **Advanced Options:** Includes interpolation, duplication, edit-mode extrusion, axis flipping, and count repetition.
*   **Gizmo Management:** Provides tools to swap, copy/paste, undo/redo gizmo states, and control visibility.

#### Workflow:

The addon operates by **defining the 'Previous' (start) and 'Active' (end) gizmo states, then applying transformations** based on these states. This system facilitates precise pose-to-pose adjustments. It integrates with Blender's 3D Cursor for placement.

---

## User Documentation

### Overview

Transform Tools provides enhanced transformation capabilities in Blender using custom 3D gizmos and dedicated operators. It allows for precise control over moving, rotating, and scaling objects and mesh components. The addon includes methods for:

*   Transforming elements between two explicitly defined transform states using [dual gizmos](#transformation-section).
*   Using a single gizmo as a [custom orientation](#op-cursor-pivot) for standard Blender transform tools.

**Core Workflow:** The fundamental process involves:

1.  **Defining** the 'Previous' gizmo (representing the start state).
2.  **Defining** the 'Active' gizmo (representing the end state).
3.  Selecting the elements to modify.
4.  **Applying** a transformation operation that uses the defined gizmos (like [<code>Transform</code>](#op-transform), [<code>Align</code>](#op-align) and [<code>Move</code>](#op-move)).

**Key Concepts:**

*   Gizmos definition sources: [selections](#op-get-from-selected), [interactive clicking](#op-create), [3D cursor state](#op-get-from-selected).
*   Active gizmo can serve as a custom orientation for standard Blender tools via the [<code>Cursor Pivot</code>](#op-cursor-pivot) operator.

### Installation

1.  Go to `Edit > Preferences > Add-ons`.
2.  Click `Install` and select the downloaded addon `.zip` file.
3.  Find "Transform Tools" in the list and enable the checkbox.
4.  The panel will appear in the 3D View's Sidebar (N-Panel) under the "TTools L" tab, aka "Transform Tools Lite".

#### IMPORTANT NOTE: Upgrading or Installing Multiple Versions

*   Please be aware that running multiple versions of the Transform Tools addon (e.g., having both "Core" and "Lite" enabled) at the same time is not recommended and may cause conflicts.

*   The recommended procedure for a clean upgrade is to first **disable and REMOVE** the existing version from Blender's Add-on Preferences. **Restarting Blender** before installing the new version is also advised to ensure all modules are fully unloaded.

*   Automatic Conflict Detection

    To assist with this process, the addon includes a built-in conflict detection system.

    If you enable a new version while an older one is still active, an **"Addon Conflict Detected"** dialog box will appear, listing the conflicting addons that were found.

    *   You will be given an interactive choice:
        *   Clicking the **"OK"** button will **automatically disable** the conflicting version(s) for you, allowing the new one to register correctly.
        *   Clicking **"Cancel"** will abort the process, leaving the old version active and the new one disabled, allowing you to manage the conflict manually in your preferences.
        
---

### <span id="sec-cursor-snapping">Working with the 3D Cursor and Snapping</span>

The **3D Cursor** plays a vital role in several Transform Tools operations:

*   **Interactive Gizmo Creation ([`Create`](#op-create)):** Click locations are interpreted via the 3D Cursor system to determine positions in 3D space.
*   **Gizmo Definition ([`Get from selected`](#op-get-from-selected)):** When nothing is selected, the Active gizmo is defined using the 3D Cursor's current transform.

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

*   <span id="opt-auto-update">![Auto-Update Gizmos Icon](../assets/icons/Auto-Update_Gizmos.png) **Auto-Update Gizmos**</span>: If enabled, whenever the **Active Gizmo** is defined or updated (e.g., via [`Create`](#op-create), [`Get from selected`](#op-get-from-selected), [`Paste`](#op-paste)), the **Previous Gizmo** automatically takes the state of the **former Active Gizmo**. This ensures the two gizmos usually represent the 'before' and 'after' states of the last operation.
    *   *Note:* This setting is overridden and has no effect when using [`Get from selected`](#op-get-from-selected) with exactly 2 objects, as both gizmos are defined directly from the selected objects in that specific case.

*   <span id="opt-cursor-orientation">![3D Cursor Orientation Icon](../assets/icons/3D_Cursor_Orientation.png) **3D Cursor Orientation**</span>: This option primarily controls the reference **orientation and scale** used by the interactive [`Create`](#op-create) operation when handling origin cancellation or performing automatic axis creation/alignment. The new gizmo's **origin location** will always be based on the 3D Cursor's current location if origin placement is cancelled.
    1.  *Primary Effect (Reference for <code>Create</code>'s Orientation & Scale during Auto-Creation/Cancellation):*
        *   **When Enabled:** If an axis needs to be auto-created or cancelled, the [`Create`](#op-create) operation uses the current **3D Cursor's orientation** and derives a **scale factor from the screen space zoom level** as the reference.
        *   **When Disabled:** If an axis needs to be auto-created or cancelled, the [`Create`](#op-create) operation uses the **orientation and scale** from the **former Active Gizmo** (the one active before [`Create`](#op-create) began) as the reference.
    2.  *Secondary Effect (Updating the 3D Cursor by other operations):*
        *   **When Enabled:** Allows certain other addon operations (like [`Get from selected`](#op-get-from-selected)) to update the 3D cursor's location and rotation.
        *   **When Disabled:** These other addon operations will not automatically update the 3D cursor.
        *   *(Note: The interactive [`Create`](#op-create) operation *always* updates the 3D cursor's location and rotation upon successful completion, regardless of this setting).*

*   <span id="opt-snap">![Snap Icon](../assets/icons/Snap.png) **Snap**</span>: When enabled, this forces the interactive [`Create`](#op-create) operation to use a specific, pre-defined set of snapping settings, **overriding** whatever snapping settings are currently active in Blender's 3D View header. When disabled, [`Create`](#op-create) respects Blender's current header snapping settings.
    *   *Addon's Internal Snap Behavior (When this toggle is ON):*
        *   Turns snapping **ON**.
        *   Sets snapping targets to **Vertex, Edge, Face, and Edge Midpoint**.
        *   Aligns the placed point's **rotation** to the snapped surface normal.
        *   Allows snapping to the **object being edited** (self-snapping).
        *   Ensures snapping works in both **Edit Mode and Object Mode**.
    *   *Refer to the ["Working with the 3D Cursor and Snapping"](#sec-cursor-snapping) section above for general information on Blender's snapping system.*

*   <span id="op-swap" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Swap</span>: Swaps the transform components (location, rotation, scale) of the Previous and Active gizmos. Affected components depend on the [Swap/Paste Mode Dropdown](#opt-swap-paste-mode).

*   <span id="op-axes-cycling">![Axes Cycling Icon](../assets/icons/Axes_Cycling.png) **Axes Cycling**</span>: Rotates the meaning of the Active gizmo's axes (X becomes Z, Y becomes X, Z becomes Y).

*   <span id="op-rotate-gizmo">![Rotate Icon](../assets/icons/Rotate.png) **Rotate**</span>: Rotates the Active gizmo 90 degrees around its local X-axis.

*   <span id="op-global-align">![Global Align Icon](../assets/icons/Global_Align.png) **Global Align**</span>: Resets the rotation of the Active Gizmo, aligning its local axes with the world's global X, Y, and Z axes. The gizmo's origin (location) and scale are not affected.

*   <span id="op-copy" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Copy</span>: Copies the current transform components of the Active gizmo.

*   <span id="op-paste" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Paste</span>: Pastes the copied transform components onto the Active gizmo. Affected components depend on the [Swap/Paste Mode Dropdown](#opt-swap-paste-mode).

*   <span id="opt-swap-paste-mode" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Swap/Paste Mode Dropdown</span> (<code>Gizmo</code>/<code>Location</code>/<code>Rotation</code>/<code>Scale</code>): Controls which transform components (full state, location only, rotation only, or scale only) are affected by the [`Swap`](#op-swap) or [`Paste`](#op-paste) operations.

*   <span id="op-undo-redo">![Undo Icon](../assets/icons/Undo.png) ![Redo Icon](../assets/icons/Redo.png) **Undo/Redo Gizmos**</span>: Undo or redo changes made to the gizmo states themselves.

*   <span id="op-cursor-pivot" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Cursor Pivot</span>: Switches Blender's Transform Orientation between the Active gizmo and the previously used standard orientation. This allows standard Blender operators like Move, Rotate, Scale, etc. to use the Active gizmo's precise alignment.

*   <span id="opt-fixed-size">![Fixed Visual Size Icon](../assets/icons/Fixed_Visual_Size.png) **Fixed Visual Size**</span>: Keeps the visual size of the gizmos consistent on screen as you zoom in or out. Doesn't affect transformations.

*   <span id="opt-visibility">![Gizmos Visibility All Icon](../assets/icons/Show_all_gizmos.png) **Gizmos Visibility**</span>: Cycles through the visibility states for the Previous and Active gizmos. Clicking the icon toggles between: showing both gizmos ![Gizmos Visibility All Icon](../assets/icons/Show_all_gizmos.png), showing only the Active gizmo ![Gizmos Visibility Active Icon](../assets/icons/Show_only_the_active_gizmo.png), or hiding both gizmos ![Gizmos Visibility Hidden Icon](../assets/icons/Hide_all_gizmos.png).

*   <span id="op-apply-to-selection" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Apply to Selection</span>: Applies the Active gizmo's location & rotation while preserve is average scale to a uniform scale, directly to the selected object, setting its world matrix. This is similar to `Object > Apply > All Transforms` but uses the gizmo's state instead of the world origin.

---

#### <span id="transformation-section">2. Transformation Section</span>

*   <span id="op-transform" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Transform</span>: Performs a full location and rotation transformation on the selected elements, moving them from the Previous gizmo's state to the Active gizmo's state. Behavior modified by [Options Section](#options-section) settings like [`Scale`](#opt-scale) and [`Flip`](#opt-flip).
*   <span id="op-align-xyz" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Align X / Y / Z</span>: A set of three buttons. Each transforms the selection from the specified axis of the Previous gizmo to the corresponding axis of the Active gizmo.
*   <span id="op-move" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Move</span>: Moves the selection from the location of the Previous gizmo to the location of the Active gizmo, ignoring rotation. Behavior modified by [Options Section](#options-section) settings like [`Scale`](#opt-scale) and [`Flip`](#opt-flip).*   <span id="op-rotate" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Rotate</span>: Rotates the selection at the location of the Previous gizmo to match the rotation of the Active gizmo.
*   <span id="op-scale" style="background-color: rgba(0, 0, 0, 0.667); color: white; padding: 2px 5px; border-radius: 3px; font-weight: bold;">Scale</span>: Scales the selection to match the proportional change between the gizmos, using the Previous gizmo's center as the pivot point.
*   *Requirement:* These buttons are disabled if Active gizmo and Previous gizmo haven't been defined.

---

#### <span id="options-section">4. Options Section</span>

Modify the behavior of transformation operations like [`Transform`](#op-transform), [`Align`](#op-align) and [`Move`](#op-move).

*   <span id="opt-scale">**<code>Scale</code>**</span>: Include the uniform scale factor difference between the gizmos in the transformation calculations for operations in the [Transformation Section](#transformation-section). If unchecked, the scale component of the gizmos is ignored, and only location and rotation differences are applied.
*   ***Note on Scale:*** Whether the scale component of the gizmos is included in the <code>Transform</code>, <code>Align</code>, and <code>Move</code> operations depends on the state of the [<code>Scale</code>](#opt-scale) checkbox in the [Options Section](#options-section), while the "Scale" operation alway active the scale option -it can be deactivate manually in the redo menu but there is no practical reason to do that-.

*   <span id="opt-flip">**<code>Flip</code>**</span>: Rotates the Previous gizmo 180 degrees around its Y-axis before applying the transformation. Primarily used when transforming between two faces (e.g., on different objects) that should end up facing each other, rather than aligned in the same direction.

*   <span id="opt-duplicate">**<code>Duplicate</code>**</span>: Duplicates the selected elements before transforming them.

*   <span id="opt-instance">**<code>Instance</code>**</span>: *(Object Mode Only)* When [`Duplicate`](#opt-duplicate) is checked, creates linked instances instead of full copies.

*   <span id="opt-extrude">**<code>Extrude</code>**</span>: *(Edit Mode Only for Mesh/Curve/Armature)* Extrudes selected mesh components, curve points, or bones during the transformation instead of just moving them. *Note*: If both [`Extrude`](#opt-extrude) and [`Duplicate`](#opt-duplicate) are enabled, `Extrude` overrides `Duplicate`; the elements will be extruded, not duplicated then transformed.

*   ***Note:*** The `Instance` and `Extrude` options are contextual and replace each other in the UI. The option displayed depends on whether you are in **Object Mode** or **Edit Mode**.

---

#### <span id="redo-last-section">6. Redo Last Menu (F9 Panel)</span>

After performing any transformation operation ([`Transform`](#op-transform), [`Move to`](#op-move-to-rotate-to-scale-to), etc.), a special menu becomes available. This menu allows you to interactively fine-tune the result of the last action without undoing and redoing the entire process.

**How to Access:**
*   Press `F9` immediately after an operation.
*   Or, click on the operator panel that appears in the bottom-left corner of the 3D Viewport.

This menu contains all the standard options from the main panel, plus several **exclusive settings** that offer even greater control.

### Exclusive Redo Last Settings

These advanced options are **only available in the Redo Last menu**:

*   <span id="redo-operation-type">**`Operation Type`**</span>: A dropdown menu that allows you to change the fundamental type of the transformation you just performed, without needing to undo and re-run the operator from the main panel. This is a powerful tool for quickly iterating on a transformation.
    *   **Use Cases:**
        *   **Switching Modes:** If you executed a full [`Transform`](#op-transform) but realize you only wanted to apply the location difference, you can select `Move` from this menu to instantly see the updated result.
        *   **Testing Alignments:** After performing an `Align X` operation, you can use this dropdown to instantly preview the result of `Align Y` or `Align Z` to find the correct orientation without re-running the operator.
    *   **Available Operations:**
        *   `Transform` (Full transform)
        *   `Move` (Location only)
        *   `Rotate` (Rotation only)
        *   `Scale` (Scale only)
        *   `Align X`, `Align Y`, `Align Z` (Axis-specific alignment)

*   <span id="redo-scale-axes">**`Individual Scale Axes (X, Y, Z)`**</span>:
    *   **Function:** These checkboxes allow you to enable or disable scaling on individual axes *after* the operation has been performed, providing precise non-uniform scaling control.
    *   **Availability:** These options become available when the `Scale` checkbox is active in the Redo Last menu. They are **enabled by default** when using the dedicated `Scale` or `Scale to` operators.
    *   **Critical Behavior Difference:** The axes they affect depend on the current mode:
        *   **In Object Mode:** Scaling is applied along the **object's own local X, Y, and Z axes**. This is standard non-uniform scaling, as an object's transform cannot be sheared directly.
        *   **In Edit Mode:** Scaling is applied along the **gizmo's custom axes**. This allows you to directly stretch and squash the selected geometry along any arbitrary orientation. Because this manipulates vertices directly, it can produce complex deformations and targeted shape changes that are impossible to achieve in Object Mode, which would require a combination of scaling and shearing.

*   <span id="opt-reverse">**<code>Reverse</code>**</span>: Inverts the direction of the transformation. When checked, the selected elements will transform from the Active gizmo's state back to the Previous gizmo's state, effectively swapping the start and end points for the operation.

*   <span id="redo-complex-dup">**`Complex Dup`**</span>: This option uses a more robust but slower duplication method. This ensures that complex relationships, such as modifiers that target other duplicated objects, are updated correctly. It is recommended to leave this behavior active for the most reliable results. However, if you are duplicating a high number of simple objects and experience a noticeable slowdown, you can deactivate this compatibility mode in the operator's Redo Last (F9) menu for a significant performance increase.
