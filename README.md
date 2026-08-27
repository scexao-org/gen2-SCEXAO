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

The third folder is labelled 'task' and contains a SCXdd.py file that reads the PARA files and executes the requested SCExAO operation

The fourth folder is labelled 'sk' and contains all skeleton scripts that the buttons outlined in the launcher call and execute
The skeleton files are organized in three folders in the same format as the launchers, with SETUP, INFRA_RED, and VISIBLE directories
The STARTUP_SCEXAO.sk, SHUTDOWN_SCEXAO.sk, and SET_MODE.sk scripts in particular are unique in that they are not a commmand themselves that are defined with their own constructor in SCXdd.py, or have their own PARA file and g2cam definition in SCEXAO.py.
Instead, just as the others, the launcher button calls the skeleton file, and the skeleton file contains various command calls which are implemented with their own constructor, para file, and g2cam definition. 
To implement checks before each one of these command calls, so that every command in the entire script wouldn't run innecessarily every time the button was pressed, the available status aliases for every command had to be stored in a separate variable locally defined in the skeleton script and stripped of their leading and following spaces, which would otherwise interfere in the checks.
Some of these settings/buttons, such as SHUTDOWN_SCEXAO.sk and STARTUP_SCEXAO.sk (calibration), contain OBS commands that prompt the user to check something before continuing with the other command calls.
This makes some of these buttons nto entirely automatic, but it was necessary to implement for all procedures to be properly executed. Apart from these few examples, the rest of the scripts are compeltely automated and require no further actions by the user to execute.

Because the OBS command for CHECK_STATUS is very rudementary and doesn't allow for checking status aliases that contain spaces, I was forced to make a new CEHCK_STATUS function for SCEXAO that allowed for such alias return values since most status sent by SCEXAO contains spaces. 
The SCEXAO CHECK_STATUS is defined in the python file SCXdd.py, which can be found in the task directory. The function definition is long, and can certainly be simplified and made more efficient in the future, but is nice because it allows for direct modifications to the function in case the user wants to change something, without modifying OBS functions that every other SUBARU instrument uses and introduce the risk of breaking something.
Also included in SCXdd.py is a CHECK_STATUS_CHANGED function that allows for checking when a status alias has changed instead of directly comparing it to a value.
This is purely for future use and has not as of yet been implemented into any skeleton scripts. 

The python script that is used for running g2cam is located in the scexao keyword repository, under the daemons directory, and is named SCEXAO.py (full path: /home/scexao/src/scxkw/scxkw/daemons/SCEXAO.py)
Every button command is defined here with the actual call to the command living in either scexao-ctrl, scexaoPCAM, or scexaoRTC (or sc2, sc5, and sc6). 
For commands living in scexao-ctrl, the subprocess.run function is used, and only the name of the command and the desired parameters are needed to run. 
For commands living in other computers, however, I made a local function called run_remote_command that uses ssh to access commands living in scexaoPCAM and scexaoRTC. 
These commands need the full path to run, and some even require entering the directory with a cd bash command before running. 
Thus, things can get slightly complicated, especially when some command paths require quotation marks around certain sections, which don't register by simply including them in the string that you pass into run_remote_command
Additionally, I encountered some issues with the return values of some commands, as sometimes a 1 would be returned when a 0 should have been, making g2cam think an error occured and crashing the program when the command in reality ran smoothly
To work around these issues, I imported shlex, which allows for joining parts of a command path string that require quotes, effectively producing the same effect as if one were to type quotation marks into the command call directly in a terminal
Additionally, I included another argument for commands that return abnormal values, even when there is no error. Thus, as long as some type of text indicating that the command was sucessful is returned, and that value is passed as an argument to the run_remote_command call, the program will not crash, even if a non-zero integer value is returned
Use this format for any future implementation of commands through ssh:
# For executable with arguments: use separate list elements (including brakets)
["/path/program", "argument1", "argument2"] 
# For shell operators such as &&, cd, pipes, or redirects: use one string (without brakets)
"cd /some/directory && program argument1"

These four folders communicate starting first with the YAML file that launches the commands for a given meduim (setup/IR/Vis), then the SCXdd.py file runs in the 'task' foler that reads the PARA file of the desired command to be run, and performs the requested SCExAO operation, communicating directly with the hardware
