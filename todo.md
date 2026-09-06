# To-do plans for future versions of *pyCIFer*
* Implement CIF file comparison
* When doing project refinement from ```cmd``` ShelXL application, the ```res``` file doesn't copy itself to the CIF, even with the ```ACTA``` instruction on. Make another check in the custom ```.dvp``` to compare ```.res``` files
* Same with the ```fcf``` files. If ShelXL pastes an FCF file to the directory, find it, ask the user, and paste it whole under the ```_iucr_refine_fcf_details```.
* Add more internal checks to ```.dvp``` file, maybe use the official CIF Core dic file
* (!) Do check on weighing scheme convergence (although it is an alert G in checkCIF, it is still an important issue)
* Addition of automatic parsing through cmd
* Moving to CLI?
