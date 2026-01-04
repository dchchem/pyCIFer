# pyCIFer

<img width="500" alt="image" src="https://github.com/user-attachments/assets/c364a8d1-036d-4963-b77a-fb436884eb91" />

### Installment and file redirection
pyCIFer is distributed using an ```.exe``` wizard installer, however an initial ```.py``` code can be sent to those interested (for that matter please contact the author).
Upon installation, a note would pop up to direct the files extracted to ```C:\exe``` folder (since it is **crucial** for appropriate work of pyCIFer). All the files, except for ```pyCIFer.exe```, should be put to ```config``` folder (*i.e.* the template ```.docx``` and ```.inp``` files). At last, one has to create a PATH variable for working in command line.

### Argument list
pyCIFer is usually opened *via* a command line by typing either ```pycifer``` (if PATH variable is present) or ```pycifer.exe```. After entering the filename (without any extension!) the following arguments are available:
- A -- insert additional info to CIF file
- R -- report structure using a template
- M -- report multple structures in one table using a template
- Q -- quit the program

Asides the last one, below is a short explanation of working with each of the three commands.

### CIF report (one file)
The style of CIF report in pyCIFer is somewhat similar to what is implemented in CIFTab (*Sheldrick, 2008*); however, the creation of pyCIFer template files (the examples of which are located in ```config``` folder) to one's personal taste is much more simple. The template files are filled using the standard jinja2 protocol and the related docxtpl package. The arguments for filling the template file are the common ones used in CIF files (see the CIFCore (CORE_DIC) CIF dictionary). For example, to extract the crystal system of the structure in question, one must type ```{{ _space_group_crystal_system }}``` to a corresponding place in ```.docx``` template. Aside from the common CIFCore values, pyCIFer provides a number of custom ones:
- ```{{r chemformula}}``` -  chemical formula in richtext mode, *i.e.* using subscript for numbers;
- ```{{ spacegroup }}``` -  spacegroup in richtext mode, *i.e.* using italics when needed;
- ```{{ resolution }}``` - crystal structure evaluation resolution (calculated from ```.cif``` file directly);
- ```{{ tabno }}``` and ```{{ comno }}``` - table and compound (roman numerals!) numbers, respectively.

### CIF report (several files)
The report style in templates is basically the same as in one-structure case, apart for some details. Due to file implementation in pyCIFer, the values are called using an additional ```{%tc for dbc in dbcs %}``` loop (or a ```{%tr ... }``` depending on what case of report is needed, see the ```mdef``` and ```macta``` example templates), and the dictionary values, are called from ```dbc``` (*e.g.* ```{{ dbc._space_group_crystal_system }}```).

### CIF Editing
All the additional data to be filled to a CIF file is added *via* a configuration file (with ```.inp``` extension). If custom data (*i.e.* not constant for all crystal structures, as crystal size, its shape and colour, *etc.*) is present, one can pass it by using ```!INPUT``` keyword in configuration file. Please note that the minimum and maximum theta values are taken from ```.p4p``` file (```!SAINGL``` keyword).

<div align="right">
  (C) Danila R. Chernyavskiy, 2025-2026