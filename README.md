# pyCIFer

<img width="500" alt="image" src="https://github.com/user-attachments/assets/c364a8d1-036d-4963-b77a-fb436884eb91" />

![Static Badge](https://img.shields.io/badge/DOI_(all_versions)-10.5281%2Fzenodo.18148747-blue) ![Static Badge](https://img.shields.io/badge/suggestions-welcome-8A2BE2) 

pyCIFer is a Python-based program dedicated to adding valuable data entries to CIF files automatically and reporting CIFs in a needed tabular format for both article's main text or supplementary material (ESI). Both editing and reporting the CIF files is conducted using template files, which can be customised up to the user's needs. This program is distributed using an executable, however an initial ```.py``` code can be sent to those interested (for that matter please contact the author).
pyCIFer primarily uses [PyCIFRW](https://github.com/jamesrhester/pycifrw) package capabilities[^1], (c) Australian Nuclear Science and Technology Organisation (ANSTO).

### Installment and file redirection
Upon installation, make sure to direct the files in ```config``` folder ((*i.e.* the template ```.docx```, ```.inp``` and ```.dvp``` files) to ```C:\exe``` (since it is **crucial** for appropriate work of pyCIFer). At last, one has to create a PATH variable for working in command line.

### Argument list
pyCIFer is usually opened via a command line by typing either ```pycifer``` (if PATH variable is present) or ```pycifer.exe```. After entering the filename (without any extension!) the following arguments are available:
- ```[A]``` -- insert additional info to CIF file
- ```[C]``` -- do a prerequisite validation (checkCIF style)
- ```[R]``` -- report structure using a template
- ```[M]``` -- report multiple structures in one table using a template
- ```[S]``` -- select new CIF file
- ```[Q]``` -- quit the program

Asides the last one, below is a short explanation of working with each of the five commands.

### CIF report (one file)
The style of CIF report in pyCIFer is somewhat similar to what is implemented in CIFTab[^2]; however, the creation of pyCIFer template files (the examples of which are located in ```config``` folder) to one's personal taste is much more simple. The template files are filled using the standard jinja2 protocol and the related docxtpl package. The arguments for filling the template file are the common ones used in CIF files (see the [CIFCore](https://github.com/COMCIFS/cif_core) (CORE_DIC) CIF dictionary[^3]). For example, to extract the crystal system of the structure in question, one must type ```{{ _space_group_crystal_system }}``` to a corresponding place in ```.docx``` template. Aside from the common CIFCore values, pyCIFer provides a number of custom ones:
- ```{{r chemformula}}``` -  chemical formula in richtext mode, *i.e.* using subscript for numbers;
- ```{{r spacegroup }}``` -  spacegroup in richtext mode, *i.e.* using italics when needed;
- ```{{ resolution }}``` - crystal structure evaluation resolution (calculated from ```.cif``` file directly);
- ```{{ tabno }}``` and ```{{ comno }}``` - table and compound (roman numerals!) numbers, respectively.

Additionally, some new custom values have been added in version v2026/1-1, dedicated to hydrogen bonding parameters (**make sure the hydrogen bonds are added to a CIF file beforehand**):
- ```{{ hbond.donor }}``` and ```{{ hbond.acceptor }}``` - atom corresponding to a hydrogen bond donor an acceptor;
- ```{{ hbond.dh_length }}```, ```{{ hbond.ha_length }}```, ```{{ hbond.da_length }}``` and ```{{ hbond.dha_angle }}``` - main geometrical parameters of hydrogen bonds;
- ```{{ hbond.symmcode }}``` - SHELX-style symmcode;
- ```{{r hbond.symmop }}``` - text-like analogue for symmcode in richtext mode, *i.e.* using italics for axes (*e.g.*, prints -1+*x*, *y*, 1+*z* for ```1_456``` symmcode).

### CIF report (several files)
The report style in templates is basically the same as in one-structure case, apart for some details. Due to file implementation in pyCIFer, the values are called using an additional ```{%tc for dbc in dbcs %}``` loop (or a ```{%tr ... }``` depending on what case of report is needed, see the ```mdef``` and ```macta``` example templates), and the dictionary values, are called from ```dbc``` (*e.g.* ```{{ dbc._space_group_crystal_system }}```).

### CIF Editing
All the additional data to be filled to a CIF file is added *via* a configuration file (with ```.inp``` extension). If custom data (*i.e.* not constant for all crystal structures, as crystal size, its shape and colour, *etc.*) is present, one can pass it by using ```!INPUT``` keyword in configuration file. Please note that the minimum and maximum theta values are taken from ```.p4p``` file (```!SAINGL``` keyword).

### CIF prerequisite validation
From version v2026/3 and onward, *pyCIFer* can handle prerequisite checkCIF/PLATON-style checking against a data validation prefilter file (with ```.dvp``` extension). By default, it is recommended to pre-check your CIF files using standard IUCr DVP checks (listed on their [website](https://journals.iucr.org/services/cif/checking/prefilter.html), use the ```precheck.dvp``` file). The ```.dvp``` files need to be, along with configuration files and templates, in the config folder.
> [!NOTE]
> Some entries, listed at the IUCr DVP, are deprecated and replaced by some new ones. See the [CIFCore](https://github.com/COMCIFS/cif_core) (CORE_DIC) CIF dictionary for alternative entries.

The ```.dvp``` files can be edited to one's taste and needs via certain general check types, called by the next keywords:
- ```PRES``` -- the entry must be present in the CIF file
- ```CHAR``` -- must be of type 'char' ('str' in Python)
- ```NUMB``` -- must be of type 'numb' ('float' or 'int' in Python)
- ```NOEM``` -- must be non-empty
- ```HYDR``` -- must be present if structure contains any H atoms
- ```FRAC``` -- must not contain fractions as decimals; checked by presence of any dot symbols  
- ```GRUP-X``` -- all entries, listed in a group No. X, must be present and given, if at least one non-'?' value is present. Maximum 9 different groups can be made (GRUP-1 through GRUP-9)
- ```LEAS-X``` -- at least one of the entries, listed in group, must be present and given. Numbering same as ```GRUP```
- ```OLDE``` -- old (deprecated) entry type

Other keywords are to be added later. Comments can be also put at the DVP files with a hashtag symbol. 
Example of usage in DVP file:
```
SYMM_001    _space_group_crystal_system    PRES CHAR
        |   |                          |  |
        -----                          ----
        2 tabs                         2 tabs
```
The line is consisted of a check code (e.g., standard PLATON one), followed by a checked entry and check type keywords. The spaces between have to be 2 tabs (\t). The ```SYMM_001``` will check crystal system to be present in the CIF file and to be a char in its value, otherwise it will raise an alert.

> [!CAUTION]
> The DVP is conducted at the very **minimal** level in order to check the absence of any important entries in the CIF
> file, which is equal to level A-, B-, or C-alert(s). A DVP-based check **does not** substitute a full checkCIF validation.

### References
[^1]: A validating CIF parser: *PyCIFRW*. J. Appl. Cryst., 2006, **39**, 621–625. DOI: [10.1107/S0021889806015627](https://doi.org/10.1107/S0021889806015627).
[^2]: A short history of SHELX. Acta Cryst. A, 2007, **64 (1)**, 112-122. DOI: [10.1107/S0108767307043930](https://doi.org/10.1107/S0108767307043930).
[^3]: Specification of the Crystallographic Information File format, version 2.0. J. Appl. Crystallogr., 2016, **49**, 277-284. DOI: [10.1107/S1600576715021871](https://doi.org/10.1107/S1600576715021871).


<div align="right">
  (C) Danila R. Chernyavskiy, 2025-2026
