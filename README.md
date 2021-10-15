# LaserTRAM-DB: A dashboard for the complete laser ablation icp-ms data reduction pipeline. 

LaserTRAM-DB is a dashboard for the complete processing pipeline of Laser Ablation Inductively Coupled Plasma Mass Spectrometry (LA-ICP-MS) data in geologic samples. As LA-ICP-MS data in geologic samples frequently have inclusions within them that do not represent the material of interest, user interaction is required to filter them out of the overall ablation signal. LaserTRAM-DB allows the user to filter which portion of the ablation peak is utilized in calculating concentrations, subsequently allowing for more accurate data to be obtained. It also allows for the processing of not only individual spot analysis data but a line of spots gathered in rapid succession, reducing the time required for data reduction while still ensuring data quality.

It is comprised of 3 parts: 
1. **LaserTRAM:** Choosing an interval of interest from raw cps data in individual spot analyses and normalizing it to an internal standard.



2. **LaserTRAM profiler:** Functionally the same as LaserTRAM, however has tools that allow for the rapid inspection of a line of spot analyses gathered in quick succession.

3. **LaserCalc:** Takes the output from either LaserTRAM or LaserTRAM profiler and converts the normalized data into concentrations using the equations outlined below.



# Use

Video tutorials on how to use each piece of software can be found at the following links:

- [LaserTRAM video](https://www.youtube.com/watch?v=ALVzTdMnS-k&t=338s&ab_channel=JordanLubbers)
- [LaserCalc video](https://www.youtube.com/watch?v=vWmwE5XO5l0&t=1s&ab_channel=JordanLubbers)

