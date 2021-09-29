
# Lesson 2. Home Work. Practice
### GCP Account Setup:
1. Create a Gmail account if you don’t have one: https://accounts.google.com/signup
2. Sign up for free trial at Google cloud platform: https://cloud.google.com

### Environment Setup:
3. Open Cloud shell: https://shell.cloud.google.com/
4. Create *syseng01-* project: `if gcloud projects list --format="json"|jq -r '.[].name'|grep -q "syseng01-" ;then echo "Project exists!"; else gcloud projects create syseng01-$(echo $((10000 + $RANDOM % 99999)));fi`
5. Run `gcloud projects list` find new project. Make sure you have only one project that name fits in to `syseng01-*` format.
6. Switch to *sysaeng01-* project: `gcloud config set project $(gcloud projects list --format="json"|jq -r '.[] | select(.name | contains("syseng01-")) | .name')`
7. Set Default zone: `gcloud config set compute/zone us-central1-a`
8. Run `gcloud alpha billing accounts list` to see billing_account_id
9. To enable billing on the new project run: 
```
gcloud alpha billing accounts projects link $(gcloud projects list --format="json"|jq -r '.[] | select(.name | contains("syseng01-")) | .name') --account-id $(gcloud beta billing accounts list --format="value(name)")
```
10. Enable Compute API
	- Run `gcloud services enable compute.googleapis.com` to enable compute API. (it may take few minutes to complete)
11. Create and upload ssh keys
	- Run `ssh-keygen -b 4096` keep path to key file default example:`/home/andy/.ssh/id_rsa`, keep passphrase empty (press enter two times)
	> NOTE: The public key has a SINGLE LINE with format `Type Base64key User@Host`
	- `Type` is usually `ssh-rsa` but could be `ssh-dsa`
	- `Base64key` is a line with ASCI characters WITHOUT SPACES
	- `User` Active User at the moment key created
	- `Host` Hostname where the key created

	- Run command `echo $USER` to see system username
	> Example: 
	```
	% echo $USER
	andy
	```
	- Use *nano* or *vi* to edit `~/.ssh/id_rsa.pub` file and change format to: `username:ssh-rsa [EXISTING_KEY_VALUE_] username`
	> Note: you have to replace `user@host` at the end of the line with the username from previous step. 
	> Remember key doens't contain any spaces and usually ends with one or two equal signs
	> Example: 
	`
	andy:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDIpTEWgpgRUGV2MsY5njK5BdqZMDrLw1+bcfZuhoHitm5FA1gEN1CoIHmTRfIQGv2nVSaUL+9ZvYyuclVgWhkJ+uaOYjtHscz3ITwHoBuaxY5Yz3YhS4kBt8Ky2UwO8+yUTFo7jTVFCia/VR6v9C3YDmGDwMxhfKAZ4DDO6eCGiHdoOcH7TKT3YCvHX3C3aOC4hZuq7vsdLRZNhNw+0twULqiKUf7MVUcBd8MSkiln4oTcP3q4xaTIiKMBYr+KJ6A4KIx+DVYwbYMSlbCGX1y08XRwAJ1ZmxqsMFX1IGs/ueD3de1PyabjRhr+us1GXHNbQLQlk2Vn+iFjR7btL0K8kwbiuM/IVbeY2odam9XHbhLqY/4J2HXhujwT0lJ3LUGCjTR8XeP3XA7hndIr9T9v+rOZMMDKllCtAK3goSq5EEJZgeN+YbtAhSpyPgMlHFUiYWonUAJWzQJkjfsgYh2y6t9eSRZ1fvqhopsHj/pBF8UnW8S0xSC0lWkVWV83YKe7E7T9oHfqTBhMEas++bcXaePqmGd7+fD5S4CojZ+gV9Hqt4RzQUPTZWC8SDyGXjL9AP+XS7QR320zYRMVx4iIiSndKY5O7XM/9ObqlV0RkT4yFwMjUmEE9NUqHNJn2WhBEe0F23KfRkOFEmcVICKfgLrwEBtpwci8EOyuLM0zbQ== andy
	`
	- Make sure the key is have proper format now: `cat $HOME/.ssh/id_rsa.pub`
	- Run `gcloud compute project-info add-metadata --metadata-from-file ssh-keys="$HOME/.ssh/id_rsa.pub"` to add public key to GCP platform
	> ATTENTION!
	> IT'S TIME TO TAKE A SCREENSHOT:  Make sure the screenshot contains google cloud shell log 

### Practice on Jumphost:
12. Create Jump Instance and get remote shell
	- Run `gcloud compute instances create lesson02-jumphost` to create new instance
 	- To get remote shell to the Instance run:
`ssh $(cat ~/.ssh/id_rsa.pub|awk -F':' '{print $1}')@$(gcloud compute instances describe lesson02-jumphost --format='get(networkInterfaces[0].accessConfigs[0].natIP)')`

13. List of all files in current directory
  - Run `ls -la` to see all files in current directory
14. Create file using touch command
  - Run `touch lesson02`
15. Print all process list
  - Run `ps axu` to see all processes running
16. Start background process
  - Run `sleep 600 &` command to run background sleep
17. Find process id of the sleep
  - Run `ps aux|grep sleep` to see process parameters

	> ATTENTION!
	> IT'S TIME TO TAKE A SCREENSHOT:  Make sure the screenshot contains google cloud shell log 
  - Run `exit` to close connection with `lesson02-jumphost`
  - Make sure you get back to cloudshell instance and your prompt line looks like this: `andy@cloudshell:~ (syseng01-10246)`

### Practice on Secured host:
18. Create secured instance and get shell access to it
	- Run `gcloud compute instances create lesson02-securehost --no-address` to create secured instance
	- Start ssh-agent on cloud shell instance: `eval $(ssh-agent -s)`
	- Add private key to agent `ssh-add ~/.ssh/id_rsa`
	- To connect to instance remote shell run: 
`ssh -A $(cat ~/.ssh/id_rsa.pub|awk -F':' '{print $1}')@$(gcloud compute instances describe lesson02-jumphost --format='get(networkInterfaces[0].accessConfigs[0].natIP)')`

	- Get remote shell to secured instance: `ssh lesson02-securehost`
	- Run `hostname` to make sure you are on the secured host.

	> ATTENTION!
	> IT'S TIME TO TAKE A SCREENSHOT:  Make sure the screenshot contains google cloud shell log with ssh session to 'lesson02-securehost'

19. Check Billing details and find running virtual machine
	- Go to Billing page: [https://console.cloud.google.com/billing](https://console.cloud.google.com/billing)
	- Check billing expenses

21. Terminate VMs
	- Run `gcloud compute instances delete lesson02-jumphost` to delete instance
	- Run `gcloud compute instances delete lesson02-securehost` to delete instance
	- Run `gcloud compute instances list` to make sure you have no running instances
