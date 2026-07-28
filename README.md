# gen2-SCEXAO
gen2 integration scripts for SCExAO

This is a repository that integrates all the basic commands that govern the SCExAO intrument and its parts with gen2 to have a smoother, more efficient calibration procedure on sky.

Included in the repo are 3 folders, each containing a variety of files that initiate, declare, and run commands that communicate directly with SCExAO. 

The first folder is the launcher and contains 3 YAML files. 
The first is labelled 'SCEXAO Setup', and it contains the listed arguments and call syntax for all commands that should be run on setup
The second is labelled 'SCEXAO IR', and it contains the listed arguments and call syntax for all commands that calibrate the Infra-Red bench of SCExAO
The third is labelled 'SCEXAO Vis', and it contains the listed arguments and call syntax for all commands that calibrate the Visible bench of SCExAO 

The second folder is labelled 'para' and contains all the PARA files for each command.
Each PARA file has one or more arguments, outlines the supported inputs for those arguments, gives the deafult input, and decribes the argument's data type 

The third folder is labelled 'task' and contains a .py file that reads the PARA files and executes the requested SCExAO operation

These three folders communicate starting first with the YAML file that launches the commands for a given meduim (setup/IR/Vis), then the .py file runs in the 'task' foler that reads the PARA file of the desired command to be run, and performs the requested SCExAO operation, communicating directly with the hardware
