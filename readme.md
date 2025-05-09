![image](https://github.com/user-attachments/assets/9ffa3665-97e8-4f1c-a241-c7e1a3ce57f1)</br>
I wanted to automate the creation of the Windows and Linux VMs for our labs. Working with duck.ai it showed me how to export an ARM template that I could run over and over. It is also important to remember that in a real environment we will be handling hundreds if not thouasands (if not hundreds-of-thousands) endpoints. There is no scalable way to create things manually and we must script to scale.

____
# Step Zero
If you do not have the Azure CLI installed install it. For macOS I ran the command:

_brew update && brew install azure-cli_

____
# Step One
Create Environment Variable (EV) for the VM admin password.

Although we are working in a lab environment I want to always be thinking securely so rather than hard code the VM password into the script, I chose to make an environment variable. If you don't know what that is please ask duck.ai or google. Basically it is a way for us to save the password outside of the script and then we can pass it to the script when the time comes.

To create the EV (on macOS) we modify the .bash_profile file (located in ~/.bash_profile
):

_nano .bash_profile_

Use the arrow key to go to the bottom and add (include the quotes):

_export AZURE_VM_ADMIN_PASSWORD="yourSuperSecretPassword"_

(You can technically call it whatver you want)

Hit CTRL + X to close and press Y to save. Then type:

_source .bash_profile_

____
# Step Two
Generate an ARM Template

Log into the Azure GUI and configure a Win10 VM like normal. Create it.

After it is created go to your resource group and on the side click on "Automation" and click "Export Template"
![image](https://github.com/user-attachments/assets/dad4f24c-2e79-4ac3-8ad3-786a9782f2dd)</br>
(My GUI is in Japanese, but I think you get the idea)


You will get a zip file with the template.json file inside of it. This JSON file should contain all of the parameters you specified in the GUI.

It is important that you do this using YOUR login as your resource group and other settings will be different than mine. It is also why I do not post my finished script here. I think it would be more confusing if you tried to copy it and modify rather than create yourself.

____
# Step Three
Specify the EV, path to template, run and troubleshoot

We use an Azure CLI command to run the script. Your's will be different but this is what I used:

_az deployment group create \
  --resource-group student-rg-yourcrazysha256here \
  --template-file yourwin10templatenamehere.json \
  --parameters adminPassword=$AZURE_VM_DEFAULT_PASSWORD_

The first line is your resource group.
The second line is your template-file name. You can specify the full path to the file as well, otherwise you need to move to the directory the file is in when you execute the command.
The third line is the EV we set in Step One.

Now, go to terminal and run the command annnnddd.... you are probably going to get an error of some kind. 
<img width="1673" alt="image" src="https://github.com/user-attachments/assets/a474aaf7-4688-4d6f-9ddb-09ca8458f769" />

Take the error and put it into duck.ai. For me, I had an issue with some kind of "id" parameter (removed it), then an issue with some kind of "guest" parameter (removed it) and a few other things. After 4-5 check-ins with duck.ai the script ran successfully and when I went to the Azure GUI I saw the VM waiting there.

You also may need to verify the file with a website like https://jsonlint.com/ to make sure your commas are in the right spots.

____
# Step Four
Test it out

The real test of course is to try logging into it with the password you set as the EV in Step One. Spoiler alert: mine worked fine.

If you need to go back and see what you put you can type:

_printenv_

That will list out all of your EVs.

____
# Further

You could also save the username as an EV so it also is not hard coded into the script. Additionally this allows you to use a very very difficult password and pass it along to the VM. However, you should still rotate out the password every month or so.

Now try creating one for the Linux VM!

____
# Bonus!

Want to delete using CLI as well? Run these:</br>

_az vm delete --resource-group student-rg-yourcrazysha256here --name yourVMnamehere --yes_ </br>
_az network nsg delete --resource-group student-rg-yourcrazysha256here --name yourVMnamehere-nsg_ </br>

If you have created other resources like a public IP, NIC, etc. you will need to create commands for that as well, but just ask duck.ai.



