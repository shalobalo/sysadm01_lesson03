
# Lesson 2. Home Work. Practice
### GCP Account Setup:
1. Create a Gmail account if you don’t have one: https://accounts.google.com/signup
2. Sign up for free trial at Google cloud platform: https://cloud.google.com

### Environment Setup:
3. Open Cloud shell: https://shell.cloud.google.com/
4. Create **syseng01-** project: `if gcloud projects list --format="json"|jq -r '.[].name'|grep -q "syseng01-" ;then echo "Project exists!"; else gcloud projects create syseng01-$(echo $((10000 + $RANDOM % 99999)));fi`
5. Run `gcloud projects list` to see list of available projects. Make sure you have only one project that name fits in to `syseng01-*` format.
6. Switch to *sysaeng01-* project using command: `gcloud config set project $(gcloud projects list --format="json"|jq -r '.[] | select(.name | contains("syseng01-")) | .name')`
	> NOTE: After step 6 your prompt line should looks like: `andy@cloudshell:~ (syseng01-10246)`. Repeat previous steps if you see something other than `syseng01-` in that line.
7. Set Default zone: `gcloud config set compute/zone us-central1-a`

	> IMPORTANT! If you have GCP web page reloaded or shell console reset you have to repeat steps 6 and 7

8. Run `gcloud alpha billing accounts list` to see billing_account_id
9. To enable billing on the new project run: `gcloud alpha billing accounts projects link $(gcloud projects list --format="json"|jq -r '.[] | select(.name | contains("syseng01-")) | .name') --account-id $(gcloud beta billing accounts list --format="value(name)")`
10. Enable Compute API
	- Run `gcloud services enable compute.googleapis.com` to enable compute API. (it may take few minutes to complete)
11. Create and upload ssh keys
	- Run `ssh-keygen -b 4096 -t rsa -f "$HOME/.ssh/id_rsa1" -C "$USER" -q -N "" <<< y` to generate SSH Key pair
	
	> NOTE: Do not share you private key file `$HOME/.ssh/id_rsa`. Keep it secret like a password
	
	- Public part of the key is generated and saved along with private and has **.pub** extention: `$HOME/.ssh/id_rsa.pub`
	- Run `cat ~/.ssh/id_rsa.pub` to see public key file
	
	> Example: `ssh-rsa AAAAB3Nza...l0MWKZfQ== andy`
	
	> Public key has SINGLE LINE with format: `<algorithm> <key> <comment>`

	> **algorithm**: [SSH key format](https://en.wikipedia.org/wiki/Ssh-keygen#Key_formats_supported)

	> **key**: It's a single Base64 encoded line of characters WITHOUT SPACES. [What is Base64](https://en.wikipedia.org/wiki/Base64)
	
	> **comment**: Usually is Active User at the moment key being created `@` Hostname where the key being created

	- GCP is supporting only specific format of public key: `username:ssh-rsa AAAAB3NzaC1yc2...uLM0zbQ== username`
	- Run `sed '1s/^/'"$USER:"'/' $HOME/.ssh/id_rsa.pub > $HOME/.ssh/id_rsa.google_pub` to create version of public key file for google.
	- Make sure the key is have proper format now: `cat $HOME/.ssh/id_rsa.google_pub`
	
	> Example: `andy:ssh-rsa AAAAB3Nza...l0MWKZfQ== andy`
	
	- Run `gcloud compute project-info add-metadata --metadata-from-file ssh-keys="$HOME/.ssh/id_rsa.google_pub"` to add public key to GCP platform

	> IT'S TIME TO TAKE A SCREENSHOT: Make sure the screenshot contains google cloud shell log 

### Practice on Jumphost:
12. Create Jump Instance and get remote shell
	- Run `gcloud compute instances create lesson02-jumphost` to create new instance
 	- To get remote shell to the Instance run: `ssh $(gcloud compute instances describe lesson02-jumphost --format='get(networkInterfaces[0].accessConfigs[0].natIP)')`

	> IMPORTANT! You should see shell prompt changed to `[andy@lesson02-jumphost ~]$ ` which indicates you are connected to remote host

13. List of all files in current directory
  - Run `ls -la` to see all files in current directory
14. Create file using touch command
  - Run `touch lesson02`
  - Make sure file is created and you can see it in the `ls -la` output
15. Print all process list
  - Run `ps axu` to see all processes running
16. Start background process
  - Run `sleep 600 &` command to run background sleep
17. Find process id of the sleep
  - Run `ps aux|grep sleep` to see process parameters

	> IT'S TIME TO TAKE A SCREENSHOT:  Make sure the screenshot contains at least 10 lines of google cloud shell log

  - Run `exit` to close connection with `lesson02-jumphost`
  - Make sure you get back to cloudshell instance and your prompt line looks like this: `andy@cloudshell:~ (syseng01-10246)`

### Practice on Secured host:
18. Create secured instance and get shell access to it
	- Run `gcloud compute instances create lesson02-securehost --no-address` to create secured instance
	- Start ssh-agent on cloud shell instance: `eval $(ssh-agent -s)`
	- Add private key to agent `ssh-add ~/.ssh/id_rsa`
	- Ssh option **-A** will enable ssh-agent and your private key available on `lesson02-jumphost`
	- To connect to the instance run: `ssh -A $(gcloud compute instances describe lesson02-jumphost --format='get(networkInterfaces[0].accessConfigs[0].natIP)')`
	- Check if ssh-agent has key run `ssh-add -l`
	- Get remote shell to secured instance: `ssh lesson02-securehost`
	- Run `hostname` to make sure you are on the secured host.
	- Run `exit` to close ssh session with `lesson02-securehost`
	- Run `exit` to close ssh session with `lesson02-jumphost`

	> IT'S TIME TO TAKE A SCREENSHOT:  Make sure the screenshot contains at least 10 lines od google cloud shell log with ssh session to 'lesson02-securehost'

	- Make sure you get back to cloudshell instance and your prompt line looks like this: `andy@cloudshell:~ (syseng01-10246)`
	- Run `gcloud compute instances list`


19. Check Billing details and find running virtual machine
	- Go to Billing page: [https://console.cloud.google.com/billing](https://console.cloud.google.com/billing)
	- Check billing expenses

21. Terminate VMs
	- Run `gcloud compute instances delete lesson02-jumphost -q` to delete instance
	- Run `gcloud compute instances delete lesson02-securehost -q` to delete instance
	- Run `gcloud compute instances list` to make sure you have no running instances
