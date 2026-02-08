
# a glossary to help undestanding a sequential raytracer

## 📘 Sequential Raytracing Glossary

### **System Geometry & Planes**

| Term | Definition |
|------|------------|
| **Object plane** | The plane in the external world containing the scene being imaged. Light originates or reflects from here. |
| **Image plane** | The plane where the sensor sits; the lens maps object points to image points here. |
| **Optical axis** | The central axis of symmetry running through the lens system. |
| **Aperture stop** | The physical opening (often an iris) that limits how much light passes through the system. |
| **Entrance pupil** | The image of the aperture stop as seen from object space—determines the cone of rays accepted from each object point. |
| **Exit pupil** | The image of the aperture stop as seen from image space—where rays appear to converge after leaving the system. |
| **Field of view (FOV)** | The angular extent of the scene that the lens can image. |

### **Key Rays**

| Term | Definition |
|------|------------|
| **Chief ray (principal ray)** | [*](https://wp.optics.arizona.edu/jsasian/wp-content/uploads/sites/33/2016/03/Opti517-Optical-Quality-2014.pdf "OPTI 517 Image Quality Richard Juergens Senior Engineering Fellow") Ray aberration curves trace fans of rays in two orthogonal directions—they map the image positions of rays relative to the chief ray. The chief ray passes through the center of the aperture stop from an off-axis object point. |
| **Marginal ray** | A ray from an on-axis object point that passes through the edge of the aperture stop. Defines the system's light-gathering ability. |
| **Paraxial ray** | An idealized ray close to the optical axis where small-angle approximations (sin θ ≈ θ) hold. Used for first-order calculations. |
| **Skew ray** | A ray that neither lies in nor intersects the optical axis. |

### **Sequential vs Non-Sequential Raytracing**

| Term | Definition |
|------|------------|
| **Sequential raytracing** | [*](https://hikarilearningblog.com/en/opticstudio_sequential_1/ "Geometrical optics and sequential _OpticStudio sequential mode (1) | 光ラーニング") The meaning of "sequential ray tracing" is to predict the way light particles travel in a predetermined order by calculation. The important thing is the "predetermined order." Surfaces are numbered and rays traverse them in fixed sequence. |
| **Non-sequential raytracing** | [*](https://www.3doptix.com/academy-post/ray-optics-simulation/ "Ray Optics Simulation | 3DOptix") In sequential ray-tracing, optical elements are intersected one at a time in a predefined order where non-sequential ray-tracing allows for random and multiple encounters between rays and surfaces. Used for stray light, ghost analysis. |
| **Surface** | In sequential mode, bodies are split into surfaces added to an ordered list. Each surface has properties (curvature, material, coating). |

### **Aberrations (What Goes Wrong)**

| Term | Definition |
|------|------------|
| **Aberration** | [*](https://www.zemax.com/blogs/news/correcting-six-common-types-of-lens-aberrations?_pos=1&_sid=62f3754c1&_ss=r "Correcting six common types of lens aberrations – Zemax") In optical design, aberrations occur when light from one point of an object doesn't converge into or diverge from a single point after transmission through the system. The non-linear terms in Snell's Law cause deviations from perfect imagery. |
| **Seidel aberrations** | [*](https://blog.ozeninc.com/resources/lens-design-in-zemax-aberration-theory "Lens Design in Zemax - Aberration Theory") In the mid-19th century Ludwig von Seidel formalized a set of five third-order aberrations that are common among all rotationally symmetric optical systems. These are: spherical, coma, astigmatism, field curvature, distortion. |
| **Spherical aberration** | [*](https://www.zemax.com/blogs/news/correcting-six-common-types-of-lens-aberrations?_pos=1&_sid=62f3754c1&_ss=r "Correcting six common types of lens aberrations – Zemax") This occurs when there is increased refraction of light rays striking a lens near the edge versus closer to the center. |
| **Coma** | [*](https://blog.ozeninc.com/resources/lens-design-in-zemax-aberration-theory "Lens Design in Zemax - Aberration Theory") Off-axis point sources produce comet-shaped blurs due to asymmetric magnification across the pupil. |
| **Astigmatism** | [*](https://www.laserfocusworld.com/sponsored/zemax/article/16571887/correcting-six-common-types-of-lens-aberrations "Correcting six common types of lens aberrations | Laser Focus World") Rays that propagate in two perpendicular planes have different foci. Where the horizontal and vertical axes cross, the axes will be in sharp focus at two different distances. |
| **Field curvature (Petzval)** | [*](https://blog.ozeninc.com/resources/lens-design-in-zemax-aberration-theory "Lens Design in Zemax - Aberration Theory") This is a failure to focus the entire image on a single plane. Instead, the focal plane is a paraboloid surface resembling a bowl, called the Petzval Surface of Curvature. |
| **Distortion** | [*](https://www.zemax.com/blogs/news/correcting-six-common-types-of-lens-aberrations?_pos=1&_sid=62f3754c1&_ss=r "Correcting six common types of lens aberrations – Zemax") Barrel, pincushion, or mustache distortion—where image magnification varies with distance from the optical axis. |
| **Chromatic aberration** | Wavelength-dependent focus shift. Longitudinal chromatic aberration (LCA) is along the axis; transverse (TCA) is perpendicular. |
| **Defocus** | [*](https://www.zemax.com/blogs/news/correcting-six-common-types-of-lens-aberrations?_pos=1&_sid=62f3754c1&_ss=r "Correcting six common types of lens aberrations – Zemax") The optical system is out of focus, reducing the sharpness of images produced by the system. |

### **Image Quality Metrics**

| Term | Definition |
|------|------------|
| **Spot diagram** | [*](https://www.microscopyu.com/microscopy-basics/modulation-transfer-function "Modulation Transfer Function | Nikon’s MicroscopyU") After passing through the exit pupil and being distributed over the image plane, the ray intersections are used to plot a spot diagram of the light points at the image plane. |
| **RMS spot size** | Root-mean-square radius of the spot diagram—a single number summarizing blur. |
| **Point Spread Function (PSF)** | [*](https://www.opticsforhire.com/blog/understanding-strehl-ratio-amp-wavefront-error-for-optical-systems/ "Understanding Strehl Ratio in Optical Systems | OFH - Optics for Hire") The PSF is the image of an ideal point source formed by an optical system. It describes how light spreads at the image plane due to diffraction and aberrations. |
| **Modulation Transfer Function (MTF)** | [*](https://en.wikipedia.org/wiki/Optical_transfer_function "Optical transfer function - Wikipedia") The optical transfer function is defined as the Fourier transform of the point spread function. The modulation transfer function (MTF) is the magnitude of this—it tells you contrast as a function of spatial frequency. |
| **Strehl ratio** | [*](https://www.opticsforhire.com/blog/understanding-strehl-ratio-amp-wavefront-error-for-optical-systems/ "Understanding Strehl Ratio in Optical Systems | OFH - Optics for Hire") The Strehl ratio compares a real optical system to an ideal, aberration-free system. It is defined as the peak intensity of the real system's PSF divided by the peak intensity of the ideal PSF. A value of 1.0 indicates a perfect system. |
| **Wavefront error (WFE)** | [*](https://www.opticsforhire.com/blog/understanding-strehl-ratio-amp-wavefront-error-for-optical-systems/ "Understanding Strehl Ratio in Optical Systems | OFH - Optics for Hire") Wavefront error primarily manifests as aberrations, introducing distortions in the way light propagates through an optical system. |
| **OPD (Optical Path Difference)** | The difference in optical path length between actual rays and the ideal reference sphere. Related to wavefront error. |
| **Diffraction limit** | [*](https://www.edmundoptics.com/knowledge-center/application-notes/optics/comparison-of-optical-aberrations/ "Comparison of Optical Aberrations | Edmund Optics") The goal is to create a system that is diffraction-limited, which is the best possible performance. The aberrations of diffraction-limited systems are contained within the Airy disk spot size. |
| **Encircled/ensquared energy** | Fraction of total energy within a given radius or square at the image plane. |

### **Surface Types & Descriptions**

| Term | Definition |
|------|------------|
| **Standard (spherical) surface** | A surface with constant radius of curvature. |
| **Conic surface** | [*](https://www.thepulsar.be/article/-devoptical-part-22--aspheric-lenses "#DevOptical Part 22: Aspheric Lenses") There are two ways to specify aspheric surfaces: through a conic section or through a power series. Conic constant k: 0=sphere, -1=parabola, <-1=hyperbola, 0<k<-1=ellipse. |
| **Even asphere** | [*](https://www.thepulsar.be/article/-devoptical-part-8--raytracing-101/ "#DevOptical Part 8: Raytracing 101") The last equation is known as an even asphere in optical design softwares because it only has even terms in r powers. Adds polynomial terms (α₄r⁴ + α₆r⁶ + ...) to conic base. |
| **Zernike surface** | [*](https://ansyshelp.ansys.com/public/Views/Secured/Zemax/v251/en/OpticStudio_User_Guide/OpticStudio_Help/topics/Zernike_Standard_Sag.html "Zernike Standard Sag") The Zernike Standard Sag surface is defined by the same polynomial as the Even Aspheric surface plus additional aspheric terms defined by Zernike Standard coefficients. Uses orthogonal polynomials to describe complex shapes. |
| **Freeform surface** | Non-rotationally-symmetric surface (XY polynomials, Q-type, etc.) for advanced designs. |

### **Differentiable Ray Tracing (Your Target Domain!)**

| Term | Definition |
|------|------------|
| **Differentiable ray tracing** | [*](https://arxiv.org/abs/2302.12031 "[2302.12031] Gradient descent-based freeform optics design using algorithmic differentiable non-sequential ray tracing") Algorithmic differentiable ray tracing is a new paradigm that allows one to solve the forward problem of how light propagates through an optical system while obtaining gradients of the simulation results with respect to parameters specifying the optical system. |
| **Autograd/backpropagation** | [*](https://rc.signalprocessingsociety.org/education/webinars/spsweb24003 "Differentiable Ray Tracing for End-to-End Optical Designs and Modeling | IEEE Signal Processing Society Resource Center") The application of differentiability streamlines the solution of inverse problems, akin to the training of neural networks, enabling direct optimization through gradient descent. |
| **End-to-end optimization** | [*](https://rc.signalprocessingsociety.org/education/webinars/spsweb24003 "Differentiable Ray Tracing for End-to-End Optical Designs and Modeling | IEEE Signal Processing Society Resource Center") Differentiability facilitates the simultaneous optimization of front-end optical system modeling (hardware) and back-end image processing algorithms (software). |

---

## 🔗 Geometric Conventions: "Front" vs "Back" and Surface Orientation

In a camera objective, light flows: **World → Front elements → Aperture stop → Back elements → Sensor**

| Convention | Meaning |
|------------|---------|
| **Front surface** | The surface facing the external world (object side). First to receive light. |
| **Back surface** | The surface facing the sensor (image side). |
| **Positive thickness** | Distance measured along the optical axis in the direction of light propagation (object→image). |
| **Radius sign** | Positive radius = center of curvature is to the *right* (image side) of the surface. |
| **AR coating** | Applied to *front* surfaces to reduce reflections from incoming light. |
| **Surface number** | In sequential mode, Surface 0 = object, then surfaces numbered 1, 2, 3... in order of ray encounter, final surface = image. |

---

This should give you a solid foundation for understanding Optiland's conventions. Since [*](https://optiland.readthedocs.io/en/latest/ "Welcome to Optiland’s documentation! — Optiland 0.5.8 documentation") Optiland is a Python-based, open-source framework for optical design, analysis, and optimization that can trace rays through multi-surface optical assemblies including aspherics and freeforms, and analyze paraxial properties, wavefront errors, PSFs/MTFs, these terms will map directly to its API.
