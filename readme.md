I wanted to automate the creation of the Windows and Linux VMs for our labs. Working with duck.ai it showed me how to export an ARM template that I could run over and over.

____
#Step Zero
If you do not have the Azure CLI installed install it. For macOS I ran the command:

[i]brew update && brew install azure-cli[/i]

____
#Step One
Create Environment Variable (EV) for the VM admin password.

Although we are working in a lab environment I want to always be thinking securely so rather than hard code the VM password into the script, I chose to make an environment variable. If you don't know what that is please ask duck.ai or google. Basically it is a way for us to save the password outside of the script and then we can pass it to the script when the time comes.

Create the EV (on macOS) we modify the .bash_profile file (located in ~/.bash_profile
). It is located in your In terminal navigate to to your user directory type:

nano .bash_profile

Use the arrow key to go to the bottom and add:

export AZURE_VM_ADMIN_PASSWORD="yourSuperSecretPassword"

Hit CTRL + X to close and press Y to save. Then type:

source .bash_profile

____
#Step Two
Generate an ARM Template

Log into the Azure GUI and configure a Win10 VM like normal. Create it.

After it is created go to your resource group and on the side click on "Automation" and click "Export Template"

You will get a zip file with the template.json file inside of it. This JSON file should contain all of the parameters you specified in the GUI.

It is important that you do this using YOUR login as your resource group and other settings will be different than mine. It is also why I do not post my finished script here. I think it would be more confusing if you tried to copy it and modify rather than create yourself.

____
#Step Three
Specify the EV, path to template, run and troubleshoot

We use an Azure CLI command to run the script. Your's will be different but this is what I used:

az deployment group create \
  --resource-group student-rg-yourcrazysha256hashhere \
  --template-file yourwin10templatenamehere.json \
  --parameters adminPassword=$AZURE_VM_DEFAULT_PASSWORD

The first line is your resource group.
The second line is your template-file name. You can specify the full path to the file as well, otherwise you need to move to the directory the file is in when you execute the command.
The third line is the EV we set in Step One.

Now, go to terminal and run the command annnnddd.... you are probably going to get an error of some kind. Take the error and put it into duck.ai. For me, I had an issue with some kind of "id" parameter (removed it), then an issue with some kind of "guest" parameter (removed it) and a few other things. After 4-5 check-ins with duck.ai the script ran successfully and when I went to the Azure GUI I saw the VM waiting there.

____
#Step Four
Test it out

The real test of course is to try logging into it with the password you set as the EV in Step One. Spoiler alert: mine worked fine.

If you need to go back and see what you put you can type:

printenv

That will list out all of your EVs.











 As we are in security I also wanted the script to be secure so that I didn’t need to hardcode login credentials into the script. I am on a MacBook Air. This is what I came up with:

1. Install Azure CLI:

brew update && brew install azure-cli

2. I created environment variables for the Azure login and saved them to the bash_profile file

export AZURE_ADMIN_USERNAME="yourAdminUsername"
export AZURE_ADMIN_PASSWORD="yourSecurePassword"


