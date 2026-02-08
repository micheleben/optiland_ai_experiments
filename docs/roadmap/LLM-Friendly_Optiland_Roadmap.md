# LLM-Friendly Optiland Roadmap

## Aim

We would like to explore the optiland codebase to understand how we could modify the code in an agentic way. The general intuition is to give an LLM the ability to understand geometries through the language, so it is quite important we start from understanding the conventions used in the codebase, which is a sequential raytracer, somehow inspired by Zemax which is the industry standard.

## Phase 1 — Glossary

Describe the terminology that is usually associated with a sequential raytracer. We are aiming to populate a glossary with terms like 'object plane', 'image plane', 'chief ray', 'paraxial ray', 'differentiable ray tracing', 'aberration', 'RMS spot size', 'point spread function', 'modulation transfer function', 'field of view', 'aperture stop', 'entrance pupil', 'exit pupil', 'focal length', 'numerical aperture', 'depth of field', 'bokeh', 'chromatic aberration', 'spherical aberration', 'coma', 'astigmatism', 'field curvature', 'distortion', 'vignetting', 'diffraction limit', 'Airy disk', 'ray fan plot', 'spot diagram', 'wavefront error', 'Zernike polynomials', and so on.

Put these terms and their definitions in a glossary that can be easily accessed and understood by an LLM. This glossary will serve as a reference for the LLM when it is analyzing the codebase and trying to understand how the different components of the optical system are represented and linked to the geometry of the optical objective. Put the glossary in a markdown file with clear headings and definitions, which can be easily accessed and parsed by an LLM. This glossary will be the foundation for the LLM to understand the codebase and make informed modifications to it.

The glossary should also cover optiland-specific concepts that are not found in textbooks but are essential for understanding the serialized representation — terms like pickups, solves, apodization types, field definitions, interaction models, variables, operands, and the merit function.

**Status**: in progress. The glossary is in `context/glossary.md`. We are populating it with relevant terms and their definitions and will keep updating it as we analyze the codebase.

## Phase 2 — Codebase Mapping

Search the codebase for existing code associated with the glossary terms. For example, we can search for 'aperture stop' and see how it is represented and used in the code. We can also search for 'field of view' and see how it is calculated and represented. We will analyze the code to understand how these terms are linked to the geometry of the optical objective and how they are used in the ray tracing process. For this phase, we will focus on understanding the code and how it represents the different components of the optical system, rather than trying to modify the code. We will take notes on how these terms are represented in the code and how they are linked to the geometry of the optical objective. This will help us in the next phase when we will try to modify the code in an agentic way.

Describe how these topics are linked to the geometry of the construction of an optical objective. Note that an optical objective is an instrument that transforms light that comes inside the camera from the external world and projects this light onto a camera sensor. The elements and aspects of an objective are usually associated with precise geometric elements of the objective. As an example, 'lens AR coating' might refer to the front of a lens, with the front being the side that is exposed towards the external world and the back being the side towards the sensor.

Put your notes in a markdown file with clear headings and explanations, which can be easily accessed and parsed by an LLM. Put the information in a file called `context/map.md`.

**Status**: first pass complete. The map contains glossary-to-code mappings for all major categories, a serialization architecture tree, and TODOs for deeper investigation.

## Phase 3 — Serialization Discovery

Search the codebase for existing helper functions that can be used to serialize the optical system into a JSON format. We are looking for functions that convert the optical system into a dictionary, which can then be easily serialized into JSON. We will analyze the code to understand how the different components of the optical system are represented in the dictionary and how they are linked to the geometry of the optical objective.

An example of such a function is the `to_dict` method of the `Optic` class, which converts the optical system into a dictionary representation. This method includes the aperture, fields, wavelengths, apodization, pickups, solves, and surface group of the optical system. It also includes the polarization and field definition if they are present.

```python
    def to_dict(self) -> dict:
        """Convert the optical system to a dictionary.

        Returns:
            The dictionary representation of the optical system.

        """
        data = {
            "version": 1.0,
            "aperture": self.aperture.to_dict() if self.aperture else None,
            "fields": self.fields.to_dict(),
            "wavelengths": self.wavelengths.to_dict(),
            "apodization": self.apodization.to_dict() if self.apodization else None,
            "pickups": self.pickups.to_dict(),
            "solves": self.solves.to_dict(),
            "surface_group": self.surface_group.to_dict(),
        }

        data["wavelengths"]["polarization"] = self.polarization
        data["fields"]["field_definition"] = (
            self.field_definition.to_dict() if self.field_definition else None
        )
        data["fields"]["object_space_telecentric"] = self.obj_space_telecentric
        return data
```

Key findings from the codebase exploration:
- 50+ classes implement `to_dict()` / `from_dict()` methods
- 10+ base classes use a `_registry` factory pattern with `__init_subclass__()` for polymorphic deserialization
- The `"type"` key in serialized dicts selects the concrete subclass (exception: `BasePropagationModel` uses `"class"`)
- Maximum nesting depth is 6 levels (Optic → SurfaceGroup → Surface → InteractionModel → Coating → Material → PropagationModel)
- File I/O is handled by `optiland/fileio/optiland_handler.py` using standard JSON with 4-space indentation

## Phase 4 — JSON Schema

Produce a JSON Schema that can be used to validate the JSON representation of the optical system. This schema will define the structure of the JSON representation and the types of the different fields. We will analyze the code to understand how the different components of the optical system are represented in the JSON format and how they are linked to the geometry of the optical objective. We will then use this information to create a JSON Schema that can be used to validate the JSON representation of the optical system.

JSON Schema is a formal specification (itself written in JSON) that describes the structure, types, and constraints of a JSON document. Think of it as a "contract" for what valid JSON looks like.

The schema could be derived from the existing methods we identified in Phase 3. You would essentially be documenting the implicit contract that already exists in the code, but making it explicit and machine-readable. It bridges the gap between "we have serialization" and "an LLM can reliably use that serialization."

### Why it matters for LLM-agentic use

Right now, `to_dict` produces JSON like this (conceptually):

```json
{
  "aperture": { "type": "EPD", "value": 10.0 },
  "wavelengths": { "primary": 0.55, "wavelengths": [0.48, 0.55, 0.65] },
  "surface_group": { "surfaces": [ "..." ] }
}
```

An LLM receiving this has to guess what fields exist, what types they are, what values are valid. It might try to set `"aperture": {"type": "FOV", "value": -3}` and have no idea that's invalid until runtime.

A JSON Schema would formalize all of that:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "aperture": {
      "type": "object",
      "properties": {
        "type": {
          "type": "string",
          "enum": ["EPD", "imageFNO", "objectNA", "float_by_stop_size"],
          "description": "Aperture definition method"
        },
        "value": {
          "type": "number",
          "minimum": 0,
          "description": "Aperture size in mm (for EPD) or ratio (for FNO)"
        }
      },
      "required": ["type", "value"]
    }
  }
}
```

### What this buys us concretely

**LLM tool-use**: Modern LLM APIs (OpenAI, Anthropic, etc.) accept JSON Schema directly in tool/function definitions. If you provide the schema, the model is constrained to produce valid output — it literally can't generate malformed optical systems.

**Validation**: You can validate any JSON against the schema programmatically (`jsonschema` library in Python). If an LLM proposes a modification, you validate it before deserializing — catching errors like missing fields, wrong types, or out-of-range values early.

**Self-documenting**: The schema carries description fields, enum constraints, minimum/maximum bounds. It *is* the documentation of the format. An LLM can read the schema and understand what every field means without needing the glossary separately.

**Round-trip safety**: With a schema, the flow becomes:

`to_dict` → JSON → LLM reads it → LLM proposes modified JSON → validate against schema → `from_dict` → new `Optic`

Without the schema, that middle validation step doesn't exist, and we're trusting the LLM to produce structurally correct output blindly.

### Schema versioning strategy

The schema must evolve alongside the optiland codebase. Since the `to_dict()` output already includes a `"version": 1.0` field, we should formalize this into a versioning strategy.

**Principles:**

1. **Semantic versioning for the schema**: Use `MAJOR.MINOR` (e.g., `1.0`, `1.1`, `2.0`).
   - **MAJOR** bump: breaking changes — removing fields, renaming keys, changing the structure of existing objects in ways that invalidate previously-valid JSON.
   - **MINOR** bump: additive changes — new optional fields, new geometry/material types registered in the factory, new enum values added to existing enums.

2. **Schema version tracks independently of optiland package version**: The optiland package is at `v0.5.9`, but the serialization format version is `1.0`. These can diverge — a new optiland release might not change the schema at all, or a single release might bump the schema version.

3. **The `"version"` field in serialized JSON is the contract**: Every JSON output from `Optic.to_dict()` must include a `"version"` key. The schema validator should check this field first and select the appropriate schema version.

4. **Backwards compatibility within a MAJOR version**: A `1.1` schema should accept all valid `1.0` JSON. New fields added in `1.1` should be optional with sensible defaults. This means an LLM trained on the `1.0` schema can still produce valid JSON even if the system has been upgraded to `1.1`.

5. **Schema file naming convention**: Store versioned schemas as:
   ```
   schemas/optiland_schema_v1.0.json
   schemas/optiland_schema_v1.1.json
   schemas/optiland_schema_v2.0.json
   ```
   With a symlink or copy `schemas/optiland_schema_latest.json` pointing to the current version.

6. **Registry-aware schema generation**: Since optiland uses `_registry` dicts populated by `__init_subclass__()`, the set of valid `"type"` values for geometries, materials, coatings, etc. can change when new subclasses are added. The schema generation process should introspect these registries rather than hardcoding enum values, so the schema stays in sync with the code automatically.

7. **Migration notes**: When a MAJOR version bump occurs, provide a migration guide documenting what changed and how to convert old JSON to the new format. This is important both for human users and for LLMs that may encounter old-format JSON in their context.

**Implementation sketch:**

```python
# schemas/generate_schema.py
from optiland.geometries.base import BaseGeometry
from optiland.materials.base import BaseMaterial
# ... etc.

def generate_schema(version="1.0"):
    """Generate JSON Schema from optiland's current class registries."""
    geometry_types = list(BaseGeometry._registry.keys())
    material_types = list(BaseMaterial._registry.keys())
    # ... build schema with live enum values from registries
    return schema
```

This ensures the schema is always a faithful reflection of what the code actually accepts.
