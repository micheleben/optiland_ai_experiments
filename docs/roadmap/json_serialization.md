# Json Serialization
## Aim
we would like to explore optiland codebase to understand how we could modify the code in an agentic way. The general intuition is to give an llm the ability to undestand geometries through the language so it is quite important we start from understanding the conventions used in the codebase , which is a sequential raytracer, somehow inspired by Zemax which is the industry standard.

## phase 1

Describe the terminalogy that is usually associatede with a sequential raytrace. We are aiming to populate a glossary whit terms like 'object plane', 'image plane', 'chief ray', 'paraxial ray', 'differentiable ray tracing', 'Abberration' , 'RMS spot size' , 'point spread function', 'modulation transfer function', 'field of view', 'aperture stop', 'entrance pupil', 'exit pupil', 'focal length', 'numerical aperture' , 'depth of field' , 'bokeh' , 'chromatic aberration' , 'spherical aberration' , 'coma' , 'astigmatism' , 'field curvature' , 'distortion' , 'vignetting' , 'diffraction limit' , 'airy disk' , 'ray fan plot' , 'spot diagram' , 'wavefront error' , 'Zernike polynomials' and so on.
Put these terms and their definitions in a glossary that can be easily accessed and understood by an llm. This glossary will serve as a reference for the llm when it is analyzing the codebase and trying to understand how the different components of the optical system are represented and linked to the geometry of the optical objective. Put the glossary in a markdown file with clear headings and definitions, which can be easily accessed and parsed by an llm.. This glossary will be the foundation for the llm to understand the codebase and make informed modifications to it.

status: in progress, we are currently populating the glossary with the relevant terms and their definitions. The glossary is in context/glossary.md file. We will keep updating the glossary as we find more terms and as we analyze the codebase to understand how these terms are used in the context of the optical system.

## phase 2
Now search the codebase the existing code that is associated to the terms in the glossary. For example, we can search for the term 'aperture stop' in the codebase and see how it is represented and used in the code. We can also search for 'field of view' and see how it is calculated and represented in the code. We will analyze the code to understand how these terms are linked to the geometry of the optical objective and how they are used in the ray tracing process. For this phase, we will focus on understanding the code and how it represents the different components of the optical system, rather than trying to modify the code. We will take notes on how these terms are represented in the code and how they are linked to the geometry of the optical objective. This will help us in the next phase when we will try to modify the code in an agentic way.

Describe how these topics are linked to the geometry of the constructions of an optical objective. Note that an optical objective is an istrument that transform light that comes inside the camera from an external world and project this light into a camera sensor. Then the element and aspect of an objective are usually assocated with precise geometric elements of the objective. As example 'lens AR coating' might refere to the front of a lens, with the front being the side that is exposed towards the external world and the back being the side towards the sensor.

Put your notes in a markdown file with clear headings and explanations, which can be easily accessed and parsed by an llm. Put the information in a file called context/map.md

## phase 3 
Search in the codebase the existing helper function that can be used to serialize the optical system into a json format. We are looking for functions that convert the optical system into a dictionary, which can then be easily serialized into json. We will analyze the code to understand how the different components of the optical system are represented in the dictionary and how they are linked to the geometry of the optical objective.

An example of such a function is the `to_dict` method of the `OpticalSystem` class, which converts the optical system into a dictionary representation. This method includes the aperture, fields, wavelengths, apodization, pickups, solves, and surface group of the optical system. It also includes the polarization and field definition if they are present.
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