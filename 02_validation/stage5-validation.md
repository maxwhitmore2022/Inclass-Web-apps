# Stage 5 Validation Matrix

Record actual results with the date and unit system used.

| Case | Expected result | Actual result | Status |
|---|---|---|---|
| 20 ft simply supported W12x26, 1 kip/ft | R_L = 10 kip; R_R = 10 kip; M_max = 50 kip-ft; v_max = 0.609 in | R_L = 10 kip; R_R = 10 kip; M_max = 50 kip-ft; v_max = 0.609 in | PASS |
| 20 ft simply supported, centered 12 kip point load | M_max = 60 kip-ft; deflection depends on selected member |  |  |
| 20 ft simply supported, 12 kip point load at 15 ft | Off-center reaction and moment match independent hand calculations |  |  |
| 20 ft simply supported, partial uniform load | Reactions, shear, and moment match boundary hand calculations |  |  |
| 20 ft cantilever, 12 kip end point load | R_A = 12 kip; M_A = 240 kip-ft; M_max = 240 kip-ft |  |  |
| 20 ft cantilever, 1 kip/ft full uniform load | R_A = 20 kip; M_A = 200 kip-ft; M_max = 200 kip-ft |  |  |
| 20 ft cantilever, partial uniform load | Reactions and moment match independent hand calculations |  |  |
| Passing bending case | Bending status is PASS |  |  |
| Failing bending case | Bending status is FAIL |  |  |
| Passing deflection case | Deflection status is PASS |  |  |
| Failing deflection case | Deflection status is FAIL |  |  |
| US to metric conversion | Physical beam and results remain unchanged | Span 20 ft became 6.096 m; deflection became 15.456 mm; reactions and moments converted consistently | PASS |
| Metric to US conversion | Physical beam and results remain unchanged | Reset verified restoring 20 ft and US units | PASS |
| Negative or out-of-range span/load | Inline error appears and results become unavailable | Negative span showed inline invalid state and unavailable summary values | PASS |
| Reversed uniform-load interval | Inline error appears and results become unavailable |  |  |
| Invalid load position | Inline error appears and results become unavailable |  |  |
| Steel/lumber member switching | Only category-specific presets and material fields appear |  |  |
| Keyboard navigation | Inputs, reset, export, print, and results are operable and readable |  |  |
| Mobile layout | No horizontal page overflow and all controls remain usable |  |  |
| Print/export | Print layout and JSON export contain the current result summary |  |  |
| Light theme | Full page uses the light palette with readable text and controls | Light palette verified in browser | PASS |
| Dark theme | Full page uses the dark palette with readable text and controls | Dark palette, labels, panels, and controls verified in browser | PASS |
| Theme persistence | Reload restores the most recently selected theme | Dark selection restored as dark after reload | PASS |
| System-theme fallback | First visit follows the operating-system preference |  |  |
| Theme keyboard use | Toggle has visible focus and an accessible name |  |  |
