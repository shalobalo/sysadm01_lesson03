
# Lesson 2. Home Work. Practice
### GCP Account Setup:
1. Create a Gmail account if you don’t have one: https://accounts.google.com/signup
2. Sign up for free trial at Google cloud platform: https://cloud.google.com

### Environment Setup:
3. Open Cloud shell: https://shell.cloud.google.com/
4. Create *syseng01-* project: 
```
if gcloud projects list --format="json"|jq -r '.[].name'|grep -q "syseng01-" ;then echo "Project exists!"; else gcloud projects create syseng01-$(echo $((10000 + $RANDOM % 99999)));fi
```
5. Run `gcloud projects list` to see list of available projects. Make sure you have only one project that name fits in to `syseng01-*` format.
6. Switch to *sysaeng01-* project using command: 
```
gcloud config set project $(gcloud projects list --format="json"|jq -r '.[] | select(.name | contains("syseng01-")) | .name')
```
	> NOTE: After step 7 your prompt line should looks like: `andy@cloudshell:~ (syseng01-10246)`. Repeat previous steps if you see something other than `syseng01-` in that line.
	
7. Set Default zone: `gcloud config set compute/zone us-central1-a`

	> IMPORTANT! If you have GCP web page reloaded or shell console reset you have to repeat steps 6 and 7

8. Run `gcloud alpha billing accounts list` to see billing_account_id
9. To enable billing on the new project run: 
```
gcloud alpha billing accounts projects link $(gcloud projects list --format="json"|jq -r '.[] | select(.name | contains("syseng01-")) | .name') --account-id $(gcloud beta billing accounts list --format="value(name)")
```
10. Enable Compute API
	- Run `gcloud services enable compute.googleapis.com` to enable compute API. (it may take few minutes to complete)
11. Create and upload ssh keys
	- Run `ssh-keygen -b 4096 -t rsa -f "$HOME/.ssh/id_rsa1" -C "$USER" -q -N "" <<< y` to generate SSH Key pair.
	> NOTE: Do not share you private key file `~/.ssh/id_rsa`. Keep it secret like a password.
	- Public part of the key is generated and saved along with private and has **.pub** extention: `~/.ssh/id_rsa.pub`
	- Run `cat ~/.ssh/id_rsa.pub` to see public key file
	> Example:
	```
	ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCu7kGuW5I5vbWTAuGI6V9uFlZ164yECf6XUXCoNE8kSvIKSMwhut6l+2UZFf6pAftz7iSPrZUXjTWr27ksraW+2bqn5u8PMHAyYFiPxr0WbOn/c1oEyhRuS+090VPfIGq5dz19CRxn2A+zXCZhfwDWT71PzuWqwI6VfaRBIlLietJSa9Pfd80jZziBPQKd4U6Oz5zHyX/9QHiGsgalrZXvazE3LD9Cc+jhXlf65RsQZ/mg7GKS4NAM+zz8ymgF8epmNhwgw0U68yH4O4bhih3kHBd7eRgRqbAWQbh2hXvkhrdANPcuQDH3rz1j2e+uoJ6w48RKpq4hB5vqw+Pcn/bDpc5m4ESU5j1fsYhzFQv+9aLznhUg3lLjPV1Fq7DK24JclJk6YGoPUGDcihW+19De5PkpVncOY8w/mkchzweEiw/sQpQF8nNvG9FpbG1n+9OxhipiiW2wCOHaHLREoBctx01A7Cq/0fTFFrqDn871qBXoEjS/qP7NlXtvLBcn3VJEQw1kBYn0WRsrIb2HwLKDbzMFvH3OZoM/U66s5h6YnNZoYjs1IDcaf0B/PdZsqVtDtME3ZkqMcX72/rDqH+O8xWe/K0EBOJ/DzMRfALRePnkvLUlp8J9IU+v6D3/ExJaCMKOTLj7hf+CASclRc0VOB2P9lL6vmQyE1rl0MWKZfQ== andy
	```
	> Public key has SINGLE LINE with format `<algorithm> <key> <comment>`

	> **algorithm**: [SSH key format](https://en.wikipedia.org/wiki/Ssh-keygen#Key_formats_supported)

	> **key**: It's a single Base64 encoded line of characters WITHOUT SPACES. [What is Base64](https://en.wikipedia.org/wiki/Base64)
	
	> **comment**: Usually is Active User at the moment key being created `@` Hostname where the key being created

	- GCP is supporting only specific format of public key: ** username:ssh-rsa AAAAB3NzaC1yc2...uLM0zbQ== username**
	- Run `sed -i '1s/^/'"$USER:"'/' ~/.ssh/id_rsa1.pub` to convert public key to the format.
	- Make sure the key is have proper format now: `cat $HOME/.ssh/id_rsa.pub`
	> Example: 
	`andy:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDIpTEWgpgRUGV2MsY5njK5BdqZMDrLw1+bcfZuhoHitm5FA1gEN1CoIHmTRfIQGv2nVSaUL+9ZvYyuclVgWhkJ+uaOYjtHscz3ITwHoBuaxY5Yz3YhS4kBt8Ky2UwO8+yUTFo7jTVFCia/VR6v9C3YDmGDwMxhfKAZ4DDO6eCGiHdoOcH7TKT3YCvHX3C3aOC4hZuq7vsdLRZNhNw+0twULqiKUf7MVUcBd8MSkiln4oTcP3q4xaTIiKMBYr+KJ6A4KIx+DVYwbYMSlbCGX1y08XRwAJ1ZmxqsMFX1IGs/ueD3de1PyabjRhr+us1GXHNbQLQlk2Vn+iFjR7btL0K8kwbiuM/IVbeY2odam9XHbhLqY/4J2HXhujwT0lJ3LUGCjTR8XeP3XA7hndIr9T9v+rOZMMDKllCtAK3goSq5EEJZgeN+YbtAhSpyPgMlHFUiYWonUAJWzQJkjfsgYh2y6t9eSRZ1fvqhopsHj/pBF8UnW8S0xSC0lWkVWV83YKe7E7T9oHfqTBhMEas++bcXaePqmGd7+fD5S4CojZ+gV9Hqt4RzQUPTZWC8SDyGXjL9AP+XS7QR320zYRMVx4iIiSndKY5O7XM/9ObqlV0RkT4yFwMjUmEE9NUqHNJn2WhBEe0F23KfRkOFEmcVICKfgLrwEBtpwci8EOyuLM0zbQ== andy`
	- Run `gcloud compute project-info add-metadata --metadata-from-file ssh-keys="$HOME/.ssh/id_rsa.pub"` to add public key to GCP platform
	> ATTENTION!
	> IT'S TIME TO TAKE A SCREENSHOT:  Make sure the screenshot contains google cloud shell log 

### Practice on Jumphost:
12. Create Jump Instance and get remote shell
	- Run `gcloud compute instances create lesson02-jumphost` to create new instance
 	- To get remote shell to the Instance run:
```
ssh $(cat ~/.ssh/id_rsa.pub|awk -F':' '{print $1}')@$(gcloud compute instances describe lesson02-jumphost --format='get(networkInterfaces[0].accessConfigs[0].natIP)')
```
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

	> ATTENTION!
	> IT'S TIME TO TAKE A SCREENSHOT:  Make sure the screenshot contains at least 10 lines of google cloud shell log 
  - Run `exit` to close connection with `lesson02-jumphost`
  - Make sure you get back to cloudshell instance and your prompt line looks like this: `andy@cloudshell:~ (syseng01-10246)`

### Practice on Secured host:
18. Create secured instance and get shell access to it
	- Run `gcloud compute instances create lesson02-securehost --no-address` to create secured instance
	- Start ssh-agent on cloud shell instance: `eval $(ssh-agent -s)`
	- Add private key to agent `ssh-add ~/.ssh/id_rsa`
	- Ssh option **-A** will enable ssh-agent and your private key available on `lesson02-jumphost`
	- To connect to the instance run: 
```
ssh -A $(cat ~/.ssh/id_rsa.pub|awk -F':' '{print $1}')@$(gcloud compute instances describe lesson02-jumphost --format='get(networkInterfaces[0].accessConfigs[0].natIP)')
```
	- Check if ssh-agent has key run `ssh-add -l`
	- Get remote shell to secured instance: `ssh lesson02-securehost`
	- Run `hostname` to make sure you are on the secured host.
	- Run `exit` to close ssh session with `lesson02-securehost`
	- Run `exit` to close ssh session with `lesson02-jumphost`
	> ATTENTION!
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
