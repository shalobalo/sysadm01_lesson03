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
  - Run `gcloud services enable compute.googleapis.com` to enable compute API. (it may take few minutes to complete)
11. Create and upload ssh keys
  - Run `ssh-keygen -b 4096` and keep passphrase empty
  - Use *nano* or *vi* to edit `~/.ssh/id_rsa.pub` file and change format to:
```
[username]:ssh-rsa [EXISTING_KEY_VALUE_2] [username]
```
  - Run `gcloud compute project-info add-metadata --metadata-from-file ssh-keys="$HOME/.ssh/id_rsa.pub"` to add public key to GCP platform

### Practice on Jumphost:
12. Create Jump Instance and get remote shell
  - Run `gcloud compute instances create lesson02-jumphost` to create new instance
  - To get remote shell to the Instance run:
```
ssh `gcloud compute instances list --format="json"|jq -r '.[].networkInterfaces[].accessConfigs[] | select(.name == "external-nat").natIP' 2>/dev/null`
```
or
```
gcloud compute instances list | awk '/lesson02-jumphost/{print $5}' | xargs ssh -tt
```
  - OPTIONAL: change jq parameters to search public ip by instance name
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
  - Run `gcloud compute instances create lesson02-securehost --no-address` to create secured instance
  - Start ssh-agent on cloud shell instance:
```
eval `ssh-agent -s`
```
  - Add private key to agent `ssh-add ~/.ssh/id_rsa`
  - To connect to instance remote shell run: 
```
ssh -A `gcloud compute instances list --format="json"|jq -r '.[].networkInterfaces[].accessConfigs[] | select(.name == "external-nat").natIP' 2>/dev/null`
```
  - Get remote shell to secured instance: `ssh lesson02-securehost`
  - Run `hostname` to make sure you are on the secured host.
19. Check Billing details and find running virtual machine
  - Go to Billing page: [https://console.cloud.google.com/billing](https://console.cloud.google.com/billing)
  - Check billing expenses
20. Terminate VMs
  - Run `gcloud compute instances delete lesson02-jumphost` to delete instance
  - Run `gcloud compute instances delete lesson02-securehost` to delete instance
  - Run `gcloud compute instances list` to make sure you have no running instances
