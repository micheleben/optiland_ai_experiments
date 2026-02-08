# Map from Glossary to Codebase

This document maps glossary terms to their concrete implementations in the optiland codebase. This is a first pass — each section sketches the mapping and notes where deeper investigation is needed.

> **Convention**: Paths are relative to the `optiland/` package root. The notation `Class.method()` refers to the class in the file listed.

---

## System Geometry & Planes

| Glossary Term | Code Location | Notes |
|---------------|---------------|-------|
| **Object plane** | `surfaces/object_surface.py` → `ObjectSurface` | Always index 0 in `SurfaceGroup`. Thickness = object distance. |
| **Image plane** | `surfaces/image_surface.py` → `ImageSurface` | Always last index in `SurfaceGroup`. |
| **Optical axis** | Implicit — the z-axis of the coordinate system | `coordinate_system.py` → `CoordinateSystem`. Tilts (rx, ry) rotate relative to this axis. |
| **Aperture stop** | `surfaces/standard_surface.py` → `Surface.is_stop` | A boolean on one surface. `SurfaceGroup.stop_index` returns its position. The system aperture is in `aperture.py` → `Aperture` with types: `EPD`, `imageFNO`, `objectNA`, `float_by_stop_size`. |
| **Entrance pupil** | `paraxial.py` → `Paraxial.EPD()`, `Paraxial.EPL()` | Computed, not stored. EPD = diameter, EPL = location along axis. |
| **Exit pupil** | `paraxial.py` → `Paraxial.XPD()`, `Paraxial.XPL()` | Computed, not stored. XPD = diameter, XPL = location along axis. |
| **Field of view** | `fields/field.py` → `Field`, `fields/field_group.py` → `FieldGroup` | Field points define the FOV. Interpretation depends on `field_types/` → `AngleField`, `ObjectHeightField`, etc. |

**TODO for full mapping**: Trace how `Optic.set_aperture()` propagates to `SurfaceGroup` and how the stop surface is selected during ray tracing.

---

## Key Rays

| Glossary Term | Code Location | Notes |
|---------------|---------------|-------|
| **Chief ray** | `rays/ray_generator.py` → `RayGenerator` | Generated at pupil center (Hx=0, Hy=0) for off-axis fields. Used as reference in OPD calculations. |
| **Marginal ray** | `rays/ray_generator.py` → `RayGenerator` | Generated at pupil edge for on-axis field. Defines light-gathering. |
| **Paraxial ray** | `rays/paraxial_rays.py` → `ParaxialRays` | Represented by height `y` and slope `u`. Traced by `raytrace/paraxial_ray_tracer.py`. |
| **Real ray (skew ray)** | `rays/real_rays.py` → `RealRays` | Full 3D: position (x,y,z), direction cosines (L,M,N), plus OPD, intensity, phase. Traced by `raytrace/real_ray_tracer.py`. |

**TODO for full mapping**: Document how `RayGenerator.generate()` maps field/pupil coordinates to initial ray positions and directions. Document ray aiming strategies in `rays/ray_aiming/`.

---

## Sequential Raytracing & Surfaces

| Glossary Term | Code Location | Notes |
|---------------|---------------|-------|
| **Surface** | `surfaces/standard_surface.py` → `Surface` | Core class. Composes: geometry, material_post, interaction_model, optional coating, optional physical_aperture. Records ray data after tracing (x, y, z, u, v, w, opd, intensity, aoi). |
| **Surface group** | `surfaces/surface_group.py` → `SurfaceGroup` | Ordered list. Index 0 = object, last = image. `trace()` method iterates through surfaces. `SurfaceFactory` creates surfaces from dict specs. |
| **Surface numbering** | Implicit in `SurfaceGroup._surfaces` list | 0-indexed. Stop surface tracked by `stop_index` property. |
| **Thickness** | `coordinate_system.py` → `CoordinateSystem.z` | The z-position of a surface's coordinate system relative to the previous surface defines the "thickness" (axial gap). |

**Key serialization note**: `SurfaceGroup.to_dict()` returns a list of `Surface.to_dict()` dicts. Each surface dict contains nested dicts for geometry, material, interaction model, etc. This is the deepest and most complex part of the JSON structure (up to 6 levels of nesting).

---

## Aberrations

| Glossary Term | Code Location | Notes |
|---------------|---------------|-------|
| **Seidel aberrations** | `aberrations.py` → `Aberrations.third_order()` | Returns 13 arrays: TSC, SC, CC, TCC, TAC, AC, TPC, PC, DC, TAchC, LchC, TchC, S. |
| **Spherical** | TSC (transverse), SC (longitudinal) | Per-surface contributions summed for total. |
| **Coma** | CC (sagittal), TCC (tangential = 3*CC) | |
| **Astigmatism** | TAC (transverse), AC (longitudinal) | |
| **Field curvature** | TPC (transverse Petzval), PC (longitudinal) | |
| **Distortion** | DC | |
| **Chromatic** | TAchC (transverse axial), LchC (longitudinal axial), TchC (lateral) | |

**Also available as optimization operands**: Each aberration has a corresponding operand type in `optimization/operand/aberration.py`, plus `_sum` variants that sum over all surfaces.

---

## Image Quality Metrics

| Glossary Term | Code Location | Notes |
|---------------|---------------|-------|
| **Spot diagram** | `analysis/spot_diagram.py` → `SpotDiagram` | Traces grid of rays, plots intersection points at image plane. |
| **RMS spot size** | `analysis/spot_diagram.py` and `optimization/operand/ray.py` → `rms_spot_size` operand | Computed from spot diagram data. |
| **PSF** | `psf/` → `FFT_PSF`, `HuygensFresnel_PSF`, `GeometricPSF`, `SampledPSF` | Multiple computation methods. FFT is fastest; Huygens-Fresnel most accurate for large aberrations. |
| **MTF** | `mtf/` → `FFT_MTF`, `GeometricMTF`, `HuygensFresnel_MTF`, `SampledMTF` | Derived from PSF via Fourier transform. |
| **Strehl ratio** | Derived from PSF peak intensity vs. diffraction-limited peak | Not a standalone class — computed from PSF results. |
| **Wavefront error** | `wavefront/` → `OPD`, `ZernikeOPD` | OPD computed during ray tracing. `ZernikeOPD` fits to Zernike polynomials. |
| **OPD** | `wavefront/opd.py` → `OPD`, also stored per-ray in `RealRays.opd` | Optical path difference from reference sphere. |
| **Encircled energy** | `analysis/encircled_energy.py` → `EncircledEnergy` | Cumulative energy fraction vs. radius. |

**TODO for full mapping**: Document the analysis pipeline — how `Optic` → `RayTracer` → `Analysis` connects, and how analysis results feed back into optimization operands.

---

## Surface Types → Geometry Classes

| Glossary Term | Code Location | Serialization `type` key |
|---------------|---------------|--------------------------|
| **Standard (spherical)** | `geometries/standard.py` → `StandardGeometry` | `"StandardGeometry"` |
| **Plane** | `geometries/plane.py` → `Plane` | `"Plane"` |
| **Even asphere** | `geometries/even_asphere.py` → `EvenAsphere` | `"EvenAsphere"` |
| **Odd asphere** | `geometries/odd_asphere.py` → `OddAsphere` | `"OddAsphere"` |
| **Conic** | Handled by `StandardGeometry` with conic constant | `"StandardGeometry"` with `conic != 0` |
| **Zernike** | `geometries/zernike.py` → `ZernikeGeometry` | `"Zernike"` |
| **Chebyshev** | `geometries/chebyshev.py` → `ChebyshevPolynomialGeometry` | `"Chebyshev"` |
| **Polynomial (XY)** | `geometries/polynomial.py` → `PolynomialGeometry` | `"Polynomial"` |
| **Biconic** | `geometries/biconic.py` → `BiconicGeometry` | `"Biconic"` |
| **Toroidal** | `geometries/toroidal.py` → `ToroidalGeometry` | `"Toroidal"` |
| **NURBS** | `geometries/nurbs/nurbs_geometry.py` → `NurbsGeometry` | `"NurbsGeometry"` |
| **Forbes Q** | `geometries/forbes/geometry.py` → `ForbesQ2dGeometry`, `ForbesQbfsGeometry`, `ForbesQNormalSlopeGeometry` | `"ForbesQ2d"`, etc. |
| **Grid sag** | `geometries/grid_sag.py` → `GridSagGeometry` | `"GridSag"` |
| **Diffraction grating** | `geometries/plane_grating.py`, `standard_grating.py` | `"PlaneGrating"`, `"StandardGrating"` |

**Key pattern**: All geometries inherit from `BaseGeometry` in `geometries/base.py` and use a `_registry` dict for polymorphic `from_dict()`. The `"type"` key in the serialized dict selects the subclass.

---

## Optiland-Specific Concepts → Code

| Glossary Term | Code Location | Serialization Notes |
|---------------|---------------|---------------------|
| **Pickup** | `pickup.py` → `Pickup`, `PickupManager` | Serialized as list of `{source_surface_idx, attr_type, target_surface_idx, scale, offset}`. |
| **Solve** | `solves/` → `BaseSolve`, `ChiefRayHeightSolve`, `MarginalRayHeightSolve`, `QuickFocusSolve` | Uses registry pattern. Serialized with `"type"` key. |
| **Apodization** | `apodization/` → 7 types inheriting `BaseApodization` | Uses registry pattern. `"type"` key selects subclass. |
| **Variable** | `optimization/variable/` → 18+ variable behavior classes | Not serialized on the Optic itself — belongs to `OptimizationProblem`. |
| **Operand** | `optimization/operand/` → `ParaxialOperand`, `AberrationOperand`, `RayOperand`, `LensOperand` | Merit function terms. Not part of Optic serialization. |
| **Material** | `materials/` → `IdealMaterial`, `Material`, `AbbeMaterial`, `AbbeMaterialE`, `MaterialFile` | Uses registry pattern. `"type"` key selects subclass. Real glasses use catalog lookup. |
| **Coating** | `coatings.py` → `SimpleCoating`, `FresnelCoating` | Uses registry pattern. Optional on each surface. |
| **Physical aperture** | `physical_apertures/` → `RadialAperture`, `EllipticalAperture`, `RectangularAperture`, `PolygonAperture` | Uses registry pattern. Optional on each surface. Boolean combinations supported. |
| **Interaction model** | `interactions/` → `RefractiveReflectiveModel`, `PhaseInteractionModel`, `ThinLensInteractionModel` | Uses registry pattern. Every surface has one. |
| **Backend** | `backend/` | Not serialized — runtime configuration. |
| **Multi-configuration** | `multiconfig/` | Separate from main Optic serialization. Needs investigation. |

---

## Serialization Architecture Summary

The `Optic.to_dict()` method produces a deeply nested dictionary structure. Here's the composition tree:

```
Optic
├── aperture: Aperture {type, value}
├── field_definition: BaseFieldDefinition {field_type}
├── fields: FieldGroup
│   └── fields: [Field {x, y, vx, vy}, ...]
├── wavelengths: WavelengthGroup
│   └── wavelengths: [Wavelength {value, unit, is_primary, weight}, ...]
├── polarization: "ignore" | PolarizationState
├── apodization: BaseApodization {type, ...params}
├── pickups: [Pickup {source_surface_idx, attr_type, target_surface_idx, scale, offset}, ...]
├── solves: [BaseSolve {type, ...params}, ...]
├── obj_space_telecentric: bool
└── surface_group: [Surface, ...]
    └── Surface
        ├── geometry: BaseGeometry {type, cs: CoordinateSystem, ...params}
        ├── material_post: BaseMaterial {type, ...params}
        │   └── propagation_model: BasePropagationModel {class, ...}
        ├── interaction_model: BaseInteractionModel {type, is_reflective}
        │   ├── coating: BaseCoating {type, ...} | null
        │   └── bsdf: BaseBSDF {type, ...} | null
        ├── aperture: BaseAperture {type, ...} | null
        ├── is_stop: bool
        ├── surface_type: str | null
        └── comment: str
```

**Key design pattern**: 10+ base classes use a `_registry` dict populated by `__init_subclass__()`. The `"type"` key in serialized dicts selects the concrete subclass during `from_dict()`. Exception: `BasePropagationModel` uses `"class"` instead of `"type"`.

---

## TODOs for Complete Mapping

1. **Trace the ray generation pipeline**: `Optic.trace_rays()` → `RayGenerator` → `RealRayTracer` → per-surface `Surface.trace()`. Document how pupil/field coordinates become ray positions.
2. **Map optimization flow**: `OptimizationProblem` → operands + variables → optimizer. This is NOT serialized with `Optic.to_dict()` — it's a separate concern. Should an LLM be able to define optimization problems via JSON?
3. **Document multi-configuration**: How does `multiconfig/` extend the base serialization?
4. **Investigate tolerancing**: `tolerancing/` — how are tolerance specs represented?
5. **Map analysis → operand connection**: How do analysis classes (SpotDiagram, PSF, etc.) relate to optimization operands?
6. **Catalog glass lookup**: How does `Material` resolve glass names from catalogs? What happens during `from_dict()` for real glass?
