cmsui instructions
Login in the user@cmsui04.na.infn.it
If you want to set a new password execute the command passwd
If you want to set the csh enviroment execute the command tcsh
Set the cms enviroment with the command source /cvmfs/cms.cern.ch/cmsset_default.sh(.csh)
This allows you to install a cms release using cmsrel CMSSW_X_Y_Z
Then you can do cmsenv
and finally scram b -j 4 to compile

Setup the grid configuration
Load the configuration setup with the command: source /cvmfs/grid.cern.ch/etc/profile.d/setup-cvmfs-ui.sh
Go in your home make the directory .globus
Copy the usercert.pem and the userkey.pem in the .globus directory
Go in your CMSSW release and set up the cms enviroment: cmsenv
Now you're ready to open the connection with: voms-proxy-init

Setup the CRAB configuration
Go in your CMSSW release and set up the cms enviroment: cmsenv
Load the configuration setup with the command: source /cvmfs/cms.cern.ch/crab3/crab.sh
Re-do cmsenv
Open the connection with: voms-proxy-init --voms cms
