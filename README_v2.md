# Yb Molecular Complex Toolkit

A browser-based calculator for the crystal-field splitting and f–f oscillator strengths of Yb(III) molecular complexes. It follows the spin–orbit crystal-field (SOCF) point-charge approach described in the Supporting Information of Chem. Sci. (DOI: 10.1039/D4SC02944E).

Everything runs locally in the browser. No data is uploaded anywhere.

Live page: https://yb-molc.github.io/Yb-molecular-complex/yb_toolkit_v2.html

## Usage

1. Open `yb_toolkit_v2.html` in a browser (or use the live page).
2. In the Crystal-field splitting (SOCF) tab, load a CIF file, or click "Load example (thiolfan)".
3. Upload an absorption spectrum (two-column CSV) or type the target energies E(9,10), E(11,12), E(13,14).
4. Optionally upload an emission spectrum or type the ground splitting E(3,4)−E(1,2).
5. Choose the fitting stages and oscillator-strength options, then click "Run calculation".
6. Use the Oscillator strength tab to get the measured f of the main absorption line from a spectrum, and enter it in panel 5 of the SOCF tab.

## Inputs

- Yb(III) donor geometry: from a CIF (donor atoms within a cutoff, including symmetry-generated atoms) or the built-in example.
- Target energies from absorption: automatic peak picking or manual entry.
- Ground splitting from emission (optional).
- Spin–orbit constant ξ (default 2870 cm⁻¹).
- For oscillator strengths: measured f for E(9,10), refractive index, temperature, and 4f–5d radial integrals.

## Method

### Electronic structure (SI section 1)

- One-hole 4f¹³ model in the 14-state |m_l, m_s⟩ basis.
- H = ξ l·s + H_CF, diagonalized in one step (SI eq. S4).
- The crystal field comes from point charges on the donor atoms: B_kq = A_kq ⟨r^k⟩, k = 1–6 (SI eq. S2–S3).
- Initial charges are −χ/4 (Pauling electronegativity), rescaled so the total is −3 e.
- Radial moments are parameterized as ⟨r^k⟩ = a·ρ^k.
- Output: 7 Kramers doublets (4 ground, 3 excited), their splittings, and the transition energies from the lowest doublet (1,2).

### Fitting (adapted from SI section 4)

- Stage 0: fit a and ρ with the initial charges (9-start Nelder–Mead, least squares in meV).
- Stage 1: crude rigid rotation scan, 72 orientations (Rz(φ), then Rx(θ); SI eq. S20–S21).
- Stage 2: random charge sampling (default 200 samples, seeded).
  - SI rule: charges with −χ/4 ≤ −0.625 e move toward −1 e, the others toward 0 e. A uniform [−1, 0] e range is also available.
  - Each sample is rescaled to a total of −3 e and a, ρ are re-fitted. Samples whose fit hits a parameter bound are skipped.
  - The best charge set is kept and fully re-fitted.
- Stage 3: fine rotation scan, 288 orientations, scoring energies and f.
- The rotation stages are diagnostic only. Rotating all charges together leaves the energies and the orientation-averaged oscillator strengths unchanged, so the error spread is at rounding level (about 10⁻¹² %).

### Oscillator strengths (SI sections 2–3)

- M1: magnetic dipole operator −μ_B(l + 2s) between 4f eigenstates (SI eq. S5).
- E1: odd-k (k = 1, 3, 5) ligand-field mixing of the 4f states with 10 5d states, first-order perturbation (SI eq. S16). The dipole matrix element uses ⟨4f|r|5d⟩ (SI eq. S18).
- 5d levels: degenerate at the gap by default. An optional k = 2, 4 crystal field uses the 5d ⟨r²⟩ and ⟨r⁴⟩ inputs. 5d spin–orbit coupling is neglected, as in the SI.
- Gap matching: the 5d/4f gap is scanned on a log grid and refined by bisection until f(1,2 → 9,10) matches the measured value (default 10⁻⁶). The lower bound is the highest 4f level + 0.05 eV; the upper bound is 9 eV.
- Output: all 12 transitions (4 ground → 3 excited doublets) with E1, M1, E1 + M1 and a Boltzmann-weighted (k_BT) column in the format of SI Table S11; the f-versus-gap curve; and the 5d percentage of each state (SI eq. S19).

### Oscillator strength tab

- Fits the absorption line in a window around the peak with a Lorentzian and a Gaussian (with optional baseline).
- Converts the integrated absorption to an oscillator strength f₁₂ using the concentration, path length and refractive index.

## Outputs

- 3D view of the donor geometry.
- Energy-level diagram and calculated lines overlaid on the absorption spectrum.
- Residual and percent-error tables (SI eq. S22).
- Initial and final charges, and a summary of each fitting stage.
- Oscillator-strength table, f-versus-gap plot and 5d percentages.
- Warnings for an under-determined fit, parameters at bounds, large residuals, more than 5 % 5d character, and an unreachable target f.
- JSON and CSV export.

## Caveats

- The 4f–5d radial integrals are placeholder inputs, not computed values. They trade off against the fitted gap, so the absolute gap and the 5d percentages depend on what you enter.
- With charge sampling there are more free parameters than targets, so the individual charges are not well determined. Runs with different seeds show how much they move.
- Oscillator strengths are orientation-averaged (solution or powder). E1 and M1 are added without interference, and the local-field correction is the single n_r factor used in the SI.
- The point-charge model includes neither covalency nor vibrations.
- The built-in example uses a simplified element assignment, so its numbers do not reproduce the paper exactly.

## Differences from the SI

- ⟨r^k⟩ = a·ρ^k is fitted. The SI does not describe fitting ⟨r^k⟩.
- The fit minimizes squared residuals in meV. The SI percent error (eq. S22) is reported but not minimized.
- Charges are rescaled to −3 e after sampling, consistent with the totals in SI Tables S7–S10.
- Charges are selected on energies only; the gap is then matched to the measured f. The SI uses energies and oscillator strengths in stage 2.
- The gap lower bound is the highest 4f level + 0.05 eV instead of 0.72 eV, to avoid vanishing energy denominators.

## Not implemented

- Hessian sensitivity analysis of the rotation scan (SI eq. S23).
- Atomic similarity factor γ and m_j decomposition (SI section 6, Table S12).
- CASSCF/LoProp charges.
- Grouping of more than 6 donors into effective charges.
- Linewidth fluctuation models (SI section 8).

## Files

- `yb_toolkit_v2.html`: the whole toolkit in a single self-contained file (both tabs, no external dependencies).

## Citation

If you use this toolkit, please cite the original article (Chem. Sci., DOI: 10.1039/D4SC02944E).
