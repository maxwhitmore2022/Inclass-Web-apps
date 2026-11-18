# Beam Designer

## Purpose
Shear, moment, and deflection diagrams for a beam, with a check of
whether the beam is strong and stiff enough.

## Inputs
- **Unit system**
  - Units: US customary or metric display units
  - Range: US customary or metric
  - Default: US customary
  - Control: segmented toggle
  - Behavior: switching units converts existing entered values and updates all labels without changing the calculated beam
- **Support type**
  - Units: none
  - Range: simply supported or cantilever
  - Default: simply supported
  - Control: two-option radio group
- **Span**
  - Units: ft in US customary mode; m in metric mode
  - Range: greater than 0; 0.1–1000 ft or 0.03–305 m
  - Default: 20 ft (6.096 m)
  - Control: labeled numeric text field
- **Point loads**
  - Unit magnitude: kip or kN
  - Magnitude range: 0–1000 kip or 0–4448 kN
  - Magnitude default: 1 kip (4.448 kN)
  - Position units: ft or m
  - Position range: 0 to the beam span
  - Position default: 10 ft (3.048 m) for a new load
  - Control: repeatable list row with numeric magnitude field and numeric position field
  - Loads may be added or removed without limit. A row with magnitude 0 is ignored. Loads act vertically downward.
- **Point-load position origin**
  - Units: none
  - Range: left end or right end
  - Default: left end
  - Control: segmented toggle displayed for each point-load row
- **Uniform loads**
  - Unit magnitude: kip/ft or kN/m
  - Magnitude range: 0–100 kip/ft or 0–1459 kN/m
  - Magnitude default: 1 kip/ft (14.593 kN/m)
  - Start/end units: ft or m
  - Start/end range: 0 to the beam span, with end greater than start
  - Start/end default: the full span
  - Control: repeatable list row with numeric magnitude, numeric start, and numeric end fields
  - Loads may be added or removed without limit. A row with magnitude 0 is ignored. Loads act vertically downward.
- **Uniform-load position origin**
  - Units: none
  - Range: left end or right end
  - Default: left end
  - Control: segmented toggle displayed for each uniform-load row
- **Member category**
  - Units: none
  - Range: steel W-shape or lumber
  - Default: steel W-shape
  - Control: dropdown
- **Member preset**
  - Units: none; properties shown include section area, elastic section modulus, modulus of elasticity, and allowable bending stress
  - Range: a broad preset list of common steel W-shapes and lumber sizes
  - Default: W12x26
  - Control: dropdown filtered by the selected member category
- **Steel grade**
  - Units: none
  - Range: the applicable preset grades in the steel preset library
  - Default: A992
  - Control: dropdown; shown only when member category is steel
- **Lumber species and grade**
  - Units: none
  - Range: the applicable preset species and grades in the lumber preset library
  - Default: Douglas Fir-Larch No.1
  - Control: dropdown; shown only when member category is lumber

## Calculations
### Coordinate and sign conventions
- Let `x` be measured from the left end of the beam, from `x = 0` to
  `x = L`.
- `L` is the beam span in ft or m.
- Downward point loads `P_i` and distributed loads `w_j` are positive
  in magnitude in the load-input values. Downward loading produces
  positive sagging moment in the positive diagrams.
- Upward vertical reactions are positive. A positive internal shear
  follows the usual beam convention: it is the resultant vertical force
  acting upward on the left face of a cut. Positive internal moment is
  sagging moment.
- Section properties must use the strong axis about which bending
  occurs. The member preset must provide elastic section modulus `S_x`
  and strong-axis moment of inertia `I_x`; it must also provide modulus
  of elasticity `E` and allowable bending stress `F_b`.
- A section area `A` is optional for these calculations and is not needed
  for bending or deflection.

### Unit conventions
- US customary:
  - `L`: ft
  - `P_i`: kip
  - `w_j`: kip/ft
  - `E`: ksi
  - `S_x`: in³
  - `I_x`: in⁴
  - `F_b`: ksi
  - Internal moment `M(x)`: kip-ft
  - Internal shear `V(x)`: kip
  - Bending stress `f_b`: ksi
  - Deflection `v(x)`: in
- Metric:
  - `L`: m
  - `P_i`: kN
  - `w_j`: kN/m
  - `E`: MPa
  - `S_x`: mm³
  - `I_x`: mm⁴
  - `F_b`: MPa
  - Internal moment `M(x)`: kN-m
  - Internal shear `V(x)`: kN
  - Bending stress `f_b`: MPa
  - Deflection `v(x)`: mm
- Convert all values to one consistent system before calculations.
  In US units, convert moments to kip-in when combining with `S_x` or
  `I_x`; convert load lengths to in before deflection integration.
  In metric units, convert span, load positions, and moment-arm lengths
  to mm for deflection calculations.

### Support reactions
- For a simply supported beam, let `R_L` be the left reaction and
  `R_R` be the right reaction. Solve:
  - `ΣF_y = 0: R_L + R_R - ΣP_i - Σw_j(a_j, b_j) = 0`
  - `ΣM_L = 0: R_R L - ΣP_i x_i - Σw_j [b_j² - a_j²] / 2 = 0`
- The total uniform-load resultant is
  `W_j = w_j(b_j - a_j)` and acts at
  `(a_j + b_j)/2`.
- `R_R = [ΣP_i x_i + ΣW_j(a_j + b_j)/2] / L`
- `R_L = ΣP_i + ΣW_j - R_R`
- For a cantilever with the fixed support at `x = 0`, let `R_A` be the
  vertical reaction and `M_A` be the fixed-end reaction moment. Solve:
  - `R_A = ΣP_i + ΣW_j`
  - `M_A = ΣP_i x_i + ΣW_j(a_j + b_j)/2`
- Treat `M_A` as the hogging reaction moment. The sign shown in diagrams
  must be consistent with the positive-sagging convention.

### Shear diagram
- Calculate the shear at every coordinate as the sum of vertical
  forces on the segment to the left of `x`:
  - A left reaction adds `R_L` or `R_A`.
  - A point load `P_i` at `x_i` subtracts `P_i` for `x >= x_i`.
  - A uniform load `w_j` subtracts `w_j(x - a_j)` where
    `a_j <= x <= b_j`, and subtracts `w_j(b_j - a_j)` where `x >= b_j`.
- Produce `V(x)` at a sufficiently fine set of points and at every
  support or load boundary. Use piecewise-linear sections between
  point values.
- Report `V_max = max(|V(x)|)` and its location `x_V`.

### Moment diagram
- Calculate internal bending moment by cut equilibrium:
  - For each support reaction, add its moment about the cut according
    to positive-sagging convention.
  - Subtract the moment of each downward point load to the left of
    the cut.
  - Subtract the moment of each downward uniform-load segment to the
    left of the cut.
- Add every point-load and uniform-load boundary to the moment
  evaluation set.
- The equivalent explicit form for one simply supported beam is
  `M(x) = R_L x - Σ[P_i(x - x_i)H(x - x_i)]
  - Σ[w_j(x - a_j)²/2 H(x - a_j)
  - w_j(x - b_j)²/2 H(x - b_j)]`,
  where `H(t)` is the Heaviside step function.
- For a cantilever with its fixed support at `x = 0`,
  `M(x) = -M_A + R_A x - Σ[P_i(x - x_i)H(x - x_i)]
  - Σ[w_j(x - a_j)²/2 H(x - a_j)
  - w_j(x - b_j)²/2 H(x - b_j)]`
  using `M_A` as the hogging fixed-end moment magnitude.
- Produce `M(x)` at support and load boundaries and at a sufficiently
  fine set of points.
- Report `M_max = max(|M(x)|)` and its location `x_M`. Report positive
  and negative maximum values separately only if both occur.

### Deflection
- Assume Euler-Bernoulli beam behavior and use the strong-axis moment
  of inertia `I_x` from the selected member.
- Apply `E I_x v''(x) = -M(x)` for positive sagging moment, where
  `v(x)` is upward vertical displacement.
- Integrate `M(x)` twice with the following boundary conditions:
  - Simply supported: `v(0) = 0` and `v(L) = 0`.
  - Cantilever: `v(0) = 0` and `v'(0) = 0`.
- Alternatively, use equivalent superposition of the same simply
  supported or cantilever load cases and boundary conditions.
- Use at least the support/load boundaries and enough interior points
  to find the maximum downward deflection.
- Report `v_max = max(|v(x)|)`, its location `x_v`, and the signed
  vertical displacement at every displayed diagram coordinate.
- A downward deflection is negative in the deflection diagram when the
  upward displacement sign convention is retained.
- The equation is valid only for small-deflection elastic behavior.

### Bending stress
- For US units, use `M(x)` in kip-in and `S_x` in in³:
  `f_b(x) = M(x)/S_x`, in ksi.
- For metric units, use `M(x)` in N-mm and `S_x` in mm³:
  `f_b(x) = M(x)/S_x`, in MPa.
- The displayed maximum absolute bending stress is
  `f_b,max = max(|f_b(x)|) = |M_max|/S_x`.
- Let `F_b` be the member preset's allowable bending stress. Compare
  the stronger of tension and compression from the same strong-axis
  section using the elastic section modulus:
  `bending utilization = f_b,max/F_b`.
- Bending passes when `f_b,max <= F_b`.

### Deflection check
- Compute the allowable deflection:
  - US: `v_allow = L(in)/360`
  - Metric: `v_allow = L(mm)/360`
- Here `L` is the total span and not the distance to the maximum
  deflection location.
- Deflection passes when `|v_max| <= v_allow`.
- Report deflection utilization as `|v_max|/v_allow`.

### Missing result quantities
- Before implementation, the selected member preset must provide
  strong-axis `I_x`, strong-axis `S_x`, `E`, and `F_b`.
- Calculations must also retain every point at which loads or support
  conditions change for shear and moment discontinuity values.

## Outputs
- **Beam and load visualization**
  - Draw one beam centered in a labeled view with span `L` shown in
    ft or m.
  - Draw a simply supported beam with support symbols at `x = 0` and
    `x = L`, or a cantilever beam with a fixed-support symbol at
    `x = 0`.
  - Draw each nonzero point load `P_i` as a downward arrow at its
    entered position `x_i`, with its magnitude and unit beside or on
    the arrow.
  - Draw each nonzero uniform load `w_j` as downward arrows or a
    hatched load band over `[a_j, b_j]`, with its magnitude and unit
    shown once for the loaded interval.
  - Scale the beam, supports, loads, arrows, and labels proportionally
    enough to distinguish all entries. If an overlap occurs, stagger
    labels rather than changing the calculated geometry.
- **Reaction values**
  - For simply supported beams, display `R_L` and `R_R` with units:
    kip or kN.
  - For cantilever beams, display `R_A` with units and fixed-end
    reaction moment `M_A` with units: kip-ft or kN-m.
  - Label the reaction values in the beam view and repeat them in a
    compact Results summary.
- **Shear diagram**
  - Display `V(x)` in a separate, horizontally aligned diagram beneath
    or beside the beam.
  - Use ft or m on the horizontal axis and kip or kN on the vertical
    axis.
  - Plot positive and negative regions with the sign convention from
    Calculations.
  - Mark zero crossings and all support, point-load, and uniform-load
    boundaries.
  - Display `V_max = max(|V(x)|)` and `x_V` in the shear-diagram header
    or Results summary.
- **Moment diagram**
  - Display `M(x)` in a separate diagram with ft or m on the horizontal
    axis and kip-ft or kN-m on the vertical axis.
  - Plot positive sagging moment and negative hogging moment with
    clearly labeled signs or colors.
  - Mark the location and value of every local maximum or minimum
    needed to identify `M_max`.
  - Display `M_max = max(|M(x)|)` and `x_M`; include both positive and
    negative peak magnitudes when they occur.
- **Deflection diagram**
  - Display `v(x)` in a separate diagram with ft or m on the horizontal
    axis and in or mm on the vertical axis.
  - Use an exaggerated but explicitly labeled deflection scale; do not
    imply that the structural deflection is the same scale as the beam
    length.
  - Draw the undeformed beam position as a dashed reference line and
    the deflected position as a solid line.
  - Mark `v_max = max(|v(x)|)`, its location `x_v`, and `v_allow = L/360`
    in the deflection section.
  - Display signed displacement values according to the upward-positive
    convention from Calculations.
- **Maximum bending stress**
  - Display `f_b,max` in ksi or MPa.
  - Display the strong-axis section modulus `S_x` and allowable bending
    stress `F_b` used in the comparison.
  - Display bending utilization as `f_b,max/F_b`, including a
    percentage.
- **Bending check**
  - Display a clear PASS or FAIL status for
    `f_b,max <= F_b`.
  - PASS text should use a positive confirmation style and FAIL text
    should use a clear failure style without relying on color alone.
  - Show the governing limit, governing value, and utilization ratio
    beside the status.
- **Deflection check**
  - Display a clear PASS or FAIL status for `|v_max| <= v_allow`.
  - Show `|v_max|`, `v_allow`, and deflection utilization
    `|v_max|/v_allow` with matching units and a percentage.
- **Overall result**
  - Display an overall PASS only when both bending and deflection pass.
  - Display overall FAIL when either check fails.
- **Summary format**
  - Keep all numerical outputs consistent with the active unit system.
  - Round displayed lengths and positions to at least 3 significant
    digits, forces to 3 significant digits, moments to 3 significant
    digits, stress to 3 significant digits, and deflection to 3 decimal
    places or an equivalent appropriate precision.
  - Preserve the full-precision internal values for all comparisons;
    never base pass/fail decisions on rounded display values.
  - Recalculate and update every result immediately after a valid input
    change. Do not display stale or blocked output values.
- **Invalid-input state**
  - Show inline errors at the offending inputs and mark each affected
    diagram or result unavailable.
  - Do not draw misleading force, moment, or deflection results while
    the required inputs are incomplete or invalid.

## Look and feel
### Overall presentation
- Use royal blue and white as the visual theme. Place a BYU Cougar logo
  in the top left corner.
- Use a clean, restrained, professional engineering layout with
  consistent typography, spacing, borders, and colors.
- Place inputs in a compact control panel and place the beam view and
  diagrams in a larger results area. Stack the panels vertically on
  narrow screens.
- Do not cause horizontal page overflow on common desktop, tablet, or
  mobile widths.
- Keep labels, axes, units, support symbols, load arrows, and PASS/FAIL
  statuses legible at all supported widths.
- Include an original cartoon panda face as a small, decorative fixed
  element that remains separate from controls and meaningful page content.
- Clicking an area outside a control moves the panda face smoothly to the
  click location, keeping it inside the viewport.
- Pressing the letter `P` outside a text or selection control toggles a
  stylized hair shape above the panda face.
- Use a smaller mascot size on narrow screens and disable movement
  transitions when the user has enabled reduced-motion preferences.
- Provide a Light/Dark theme toggle in the page header.
- Default to the user's saved theme preference. On a first visit, follow
  the operating-system color scheme and fall back to light mode if no
  preference is available.
- Save the selected theme locally so it persists after reload.
- Apply the theme to the page background, panels, inputs, borders, text,
  diagrams, status cards, controls, and native scrollbars.
- Preserve royal-blue and BYU branding in both themes.
- Use text and an icon for the theme control and update its accessible label.
- Make the toggle keyboard-operable and respect reduced-motion preferences
  for any theme transition.
- Print and export reports should use a clear, light, high-contrast format.

### Initial state
- Open with the default simply supported 20 ft beam loaded by the
  default 1 kip/ft uniform load and W12x26 A992 member.
- Clearly label this beam as an example.
- Provide a reset action that restores every input and the unit system
  to the documented defaults.

### Status and accessibility
- Use text and icons in addition to color for PASS, FAIL, warnings, and
  invalid-input states.
- Use labeled form fields, visible focus states, keyboard-operable
  controls, and text alternatives for all meaningful diagram values.
- Preserve a clear reading and interaction order in responsive layouts.
- Respect the system preference for reduced motion if transitions or
  animated recalculation are used.

## Validation
### Existing hand-calculation example
- The default validation case is a 20 ft simply supported W12x26 beam
  subjected to a uniformly distributed load of 1 kip/ft:
  - Left reaction: `R_L = 10.0 kip`
  - Right reaction: `R_R = 10.0 kip`
  - Maximum moment: `M_max = 50.0 kip-ft`
  - Maximum deflection: `v_max = 0.609 in downward`
  - Maximum bending stress: calculate from `M_max` and the W12x26
    strong-axis section modulus

### Required test coverage
- Verify the example simply supported uniform-load case with independent
  hand calculations.
- Test a simply supported beam with one centered point load.
- Test a simply supported beam with a point load away from midspan.
- Test partial-length uniform loads on a simply supported beam.
- Test a cantilever beam with an end point load.
- Test a cantilever beam with a uniform load over the full span.
- Test a cantilever beam with a partial-length uniform load.
- Test at least one passing and one failing bending case.
- Test at least one passing and one failing deflection case.
- Test all documented US/metric unit conversions in both directions.
- Test invalid span, negative load magnitude, load position outside the
  span, reversed uniform-load intervals, and missing or unsupported
  member selections.

### Numerical acceptance
- Compare reaction and shear values within 0.5% of independent
  hand-calculated or reference values.
- Compare moment values within 0.5% of independent hand-calculated or
  reference values.
- Compare maximum deflection within 1% or 0.001 in, whichever is
  larger, for US validation cases.
- Convert metric validation tolerances consistently from the US reference
  values.
- Calculate bending stress and deflection utilization from full-precision
  values, then compare pass/fail decisions to the reference results.
- Do not accept a result if a rounded displayed value changes the
  pass/fail status.

## Out of scope
- Continuous beams and indeterminate beam systems.
- Buckling in all forms, including flexural, lateral-torsional, local,
  flange, web, shear, and torsional buckling.
- Axial loads and axial stress; torsion and torsional stress.
- Lateral loads, inclined beams, frames, trusses, plates, and shells.
- Temperature effects, support settlement, partial or flexible end
  fixity, dynamic loads, impact, vibration, and fatigue.
- Shear strength and shear stress checks.
- Shear deformation and Timoshenko-beam effects.
- Custom or user-entered member properties; the app is limited to
  presets from its steel and lumber libraries.
- Multi-member assemblies, load combinations, bracing, connection,
  and foundation design.
- Professional or code certification. The app is an analysis and
  educational tool, not a substitute for a structural engineer.

## Build stages

### Stage 1: Static mockup and page shell
Create the responsive royal-blue-and-white layout without real
calculations:

- Header with the app title and a placeholder for the BYU Cougar logo.
- Input panel with unit system, support, span, load, and member controls.
- Beam sketch and placeholders for shear, moment, and deflection.
- Summary cards for reactions, bending, deflection, and overall status.
- Clearly labeled example values that are not presented as calculations.

Test checklist:

- Open `index.html` directly and verify the complete mockup.
- Check desktop and narrow-screen layouts for overflow and usability.
- Verify the royal-blue/white theme and top-left logo placement.
- Confirm all major sections from the spec are visible.
- Confirm no values are presented as calculated results.

### Stage 2: Interactive inputs and validation
Add state and controls for the documented inputs:

- US/metric toggle with value conversion.
- Support and span controls.
- Repeatable point-load and uniform-load rows.
- Member category and preset dropdowns.
- Reset behavior and inline validation.

Test checklist:

- Switch support types and update the visible support-specific controls.
- Add, edit, and remove multiple point and uniform loads.
- Test positions at both ends and invalid reversed or out-of-range loads.
- Toggle units and confirm values convert without changing beam geometry.
- Change members and confirm steel/lumber presets update.
- Reset and verify all values return to documented defaults.
- Verify invalid input produces inline errors and unavailable results.

### Stage 3: Statics and beam/shear/moment diagrams
Implement reaction, shear, and moment calculations:

- Convert inputs to one consistent internal unit system.
- Solve reactions for simply supported and cantilever beams.
- Generate shear and moment boundary points.
- Draw the beam, supports, loads, reactions, and diagrams.

Test checklist:

- Validate the 20 ft simply supported W12x26 with 1 kip/ft case:
  reactions are 10 kip each and maximum moment is 50 kip-ft.
- Check centered and off-center point loads by hand.
- Check full-span and partial-span uniform loads.
- Check cantilever end-point-load and full-span uniform-load cases.
- Confirm sign conventions, zero crossings, and boundary labels.
- Verify the unit toggle leaves physical results unchanged.

### Stage 4: Deflection and member checks
Add deflection and bending-strength calculations:

- Integrate `EI v'' = -M` with correct support boundary conditions.
- Find maximum downward deflection and location.
- Apply strong-axis `I_x` and `S_x` member properties.
- Calculate bending stress, utilization, `L/360`, and overall status.

Test checklist:

- Match the validation case's 0.609 in maximum deflection.
- Check simply supported and cantilever deflection shapes independently.
- Confirm maximum deflection location and value are stable.
- Verify one passing and one failing bending case.
- Verify one passing and one failing deflection case.
- Confirm pass/fail decisions use full precision, not displayed precision.
- Confirm metric results are equivalent within the specified tolerances.

### Stage 5: Polish, accessibility, and release verification
Finish responsive behavior and the full validation matrix:

- Improve labels, legends, status icons, and result formatting.
- Add keyboard support, focus states, semantic labels, and text alternatives.
- Use text plus icons for PASS, FAIL, and invalid states.
- Record test outputs in `02_validation/` and screenshots or exports in
  `03_output/`.
- Keep all app implementation in top-level `index.html`.

Test checklist:

- Run the Validation test matrix and record actual versus expected values.
- Check desktop/mobile layouts and keyboard navigation.
- Verify preset-only steel/lumber selection.
- Confirm out-of-scope features are not exposed.
- Test the final page directly and through GitHub Pages.
- Confirm no console errors, stale outputs, or misleading invalid states.
- Test Light and Dark themes at desktop and mobile widths.
- Reload after selecting a theme and confirm it persists.
- Test system-theme fallback, keyboard operation, print, export, and contrast
  in both themes.