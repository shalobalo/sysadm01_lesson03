# Lesson 2. Home Work. Practice
### GCP Account Setup:
1. Create a Gmail account if you don’t have one: https://accounts.google.com/signup
2. Sign up for free trial at Google cloud platform: https://cloud.google.com

### Environment Setup:
3. Open Cloud shell: https://shell.cloud.google.com/
4. Create *syseng01-* project: `if gcloud projects list --format="json"|jq -r '.[].name'|grep -q "syseng01-" ;then echo "Project exists!"; else gcloud projects create syseng01-$(echo $((10000 + $RANDOM % 99999)));fi`
5. Run `gcloud projects list` find new project and copy project ID (It might be different than name)
6. Switch to *sysaeng01-* project: `gcloud config set project $(gcloud projects list --format="json"|jq -r '.[] | select(.name | contains("syseng01-")) | .name')`
7. Set Default zone: `gcloud config set compute/zone us-central1-a`
8. Run `gcloud alpha billing accounts list` to see billing_account_id
9. To enable billing on the new project run: 
```
gcloud alpha billing accounts projects link $(gcloud projects list --format="json"|jq -r '.[] | select(.name | contains("syseng01-")) | .name') --account-id $(gcloud beta billing accounts list --format="value(name)")
```
10. Enable Compute API
	 a. Run `gcloud services enable compute.googleapis.com` to enable compute API. (it may take few minutes to complete)
11. Create and upload ssh keys
	a. Run `ssh-keygen -b 4096` and keep passphrase empty
	b. Use *nano* or *vi* to edit `~/.ssh/id_rsa.pub` file and change format to:
`[username]:ssh-rsa [EXISTING_KEY_VALUE_2] [username]`
	c. Run `gcloud compute project-info add-metadata --metadata-from-file ssh-keys="$HOME/.ssh/id_rsa.pub"` to add public key to GCP platform

### Practice on Jumphost:
12. Create Jump Instance and get remote shell
a.	 Run `gcloud compute instances create lesson02-jumphost` to create new instance
b. To get remote shell to the Instance run:
```
gcloud compute instances list --format=json|jq -r '.[] | select(.name == "lesson02-jumphost").networkInterfaces[].accessConfigs[].natIP' |xargs ssh -tt
```
  > NOTE: Please add `-l <username>` at the end of previous command to use username other than in cloud shell
  > Example: `gcloud compute instances list --format=json|jq -r '.[] | select(.name == "lesson02-jumphost").networkInterfaces[].accessConfigs[].natIP' |xargs ssh -tt -l andy_01`

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

### Practice on Secured host:
18. Create secured instance and get shell access to it
  a. Run `gcloud compute instances create lesson02-securehost --no-address` to create secured instance
  b. Start ssh-agent on cloud shell instance: `eval $(ssh-agent -s)`
  c. Add private key to agent `ssh-add ~/.ssh/id_rsa`
  d. To connect to instance remote shell run: 
`gcloud compute instances list --format=json|jq -r '.[] | select(.name == "lesson02-jumphost").networkInterfaces[].accessConfigs[].natIP' |xargs ssh -tt -A`

	e.  Get remote shell to secured instance: `ssh lesson02-securehost`
	f. Run `hostname` to make sure you are on the secured host.
19. Check Billing details and find running virtual machine
	a.  Go to Billing page: [https://console.cloud.google.com/billing](https://console.cloud.google.com/billing)
    b. Check billing expenses
21. Terminate VMs
  a. Run `gcloud compute instances delete lesson02-jumphost` to delete instance
  b. Run `gcloud compute instances delete lesson02-securehost` to delete instance
  c. Run `gcloud compute instances list` to make sure you have no running instances
