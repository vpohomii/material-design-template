<h1>Week2 Assestment CI/CD by Pohomii Vasyl</h1>
<h2>Create Jenkins VM with internet access</h2>
1.  Create VM on AWS (Ubuntu 20.04 LTS with limited access. Create access key).  

```  
 * sudo apt update && sudo apt upgrade -y. 
 * sudo apt install git. 
 * git config --global user.name "Vasiliy Pohomii"
 * git config --global user.email "vpohomii@playtika.com"
 * git config --global core.editor vim
 * sudo apt install openjdk-8-jdk-headless  software-properties-common
 * sudo update-alternatives --config java
 * adding $JAVA_HOME path to /etc/environment
 * source /etc/environment
```
2. Installing Jenkins. 
```
 * wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -  
 * sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
 * sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list' 
 * sudo apt install -y jenkins. 
```
3. open /etc/default/jenkins and changin default port 8080 to 8081.  
``` 
 * sudo systemctl enable jenkins #starting and enabling jenkins  
 * sudo systemctl start jenkins  
```
4. go on page http://3.121.127.172:8081/ 
```
* sudo cat /var/lib/jenkins/secrets/initialAdminPassword.   
  #prepare Initial admin password. 
```  
5. create user admin.
6. Check Install suggested plugin.
7. Go to Manage Jenkins -> Configure system
8. set execcutors number = 0 => Save.
9. User =>configure change time zone to Europe/Kiev.
10. Manage Jenkins = > Manage Plugins = > Aviable 
11. Search and install Role-based authorization strategy (GitHub was installed by default).
12. Manage Jenkins => Manage Users => Create User. (Create user jenkins-vasylpohomii).
13. <img src="./RBStrategy.png" alt="Strategy" />

<h2>Create Agent VM</h2>
1. Create and Spinup New VM on AWS (choose existing keypair, choose created earlier security group). 
```  
* sudo apt update && sudo apt upgrade -y  
* sudo apt install git  
* sudo apt install -y openjdk-8-jre-headless  software-properties-common
```
2. Creating new key with "ssh-keygen -m PEM" on Jenkins server VM, jenkins user.
3. Creatig group and user jenkins on Agent VM. 
```
* groupadd jenkins  
* useradd -d /home/jenkins -m -r -s /bin/bash -g jenkins jenkins 
* su -l jenkins # swich to user jenkins  
* java -version # check Java aviablity  
``` 
4. Copy content of public key to agent VM usr jennkins
5. Connecting from Jenkins server to Agent VM via ssh with created key provided
6. Manage Jenkins => Manage Node and Clouds => New Node => Provide name "agentVM1", "Remote root directory", No of executors", Choose usage - "Use this node as much as possible", Provide AgentVM ip addess, choose connect method via ssh, Adding Credentials (SSH Username with privat key), saving and chosing jenkins(jenkins), Choose "Manually trusted key verification Strategy" and "Keep this agent online as much as possible" in Aviability field. Click "Save" button.
<img src="./AgentVM.png" alt="AgentVM" />
<img src="./AgentVM1.png" alt="AgentVM1" />

<h2>Configure tools – NodeJS</h2>
1. Go Manage Jenkins => Global Tool Configuration
2. Add NodeJS installation and tools (uglify-js, clean-css).
   NodeJS version 14.18.1 (LTS version)
   tools name provided separated with wthitespace
<img src="./GlobalTools_NodeJS.png" alt="GTNode" />

<h2>Create “Multibranch Pipeline” pipeline job </h2>
1. Creating folder VasylPohomiy.
2. cd VasylPohomii
3. Fork repo [https://github.com/joashp/material-design-template](materal-design-template)
```
 *  git clone https://github.com/vpohomii/material-design-template.git
 *  git checkout -b week2_vpohomii
```
   #Create and write file 
4. vim Jenkinsfile #with declarative pipeline

<groovy src="./Jenkins" alt="Jenkins file" />

<img src="./job1.png" alt="Job_Config_p1" />
<img src="./job2.png" alt="Job_Config_p2" />
<img src="./job3.png" alt="Job_Config_p3" />

5. Changing URL of origin in git
   ```
   git remote set-url origin git@github.com:vpohomii/material-design-template.git
   ```
6. Push Changes to our new branch
   ```
   git add .
   git commit -m "Create Jenkinsfile"
    >  2 files changed, 114 insertions(+)
    >  create mode 100644 Jenkinsfile
    >  create mode 100644 Week2_CI-CD_tools/Jenkinsfile
   git push origin week2_vpohomii
    > numerating objects: 5, done.
    > Counting objects: 100% (5/5), done.
    > Compressing objects: 100% (3/3), done.
    > Writing objects: 100% (4/4), 989 bytes | 989.00 KiB/s, done.
    > Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
    > To github.com:vpohomii/material-design-template.git
    >    4c6dfff..8740315  week2_vpohomii -> week2_vpohomii  
   ```
7. Go to Jenkins UI http://3.121.127.172:8081/job/Week2_CICD_Assestment_VasiliyPohomii/job/week2_vpohomii/ and press "Build Now"
<img src="./Build.png" alt="Build" />
<img src="./Buildst.png" alt="Build" />
  
<h2>Setup the GitHub webhook to trigger the jobs </h2>
1. Go ManageJenkins => Configure System
   Find GitHub section
   Type the server name "server"
   Add Credential with type "Secret text"
   go to GitHub UI Settings => Developer Settings => Perconal access token
   Click "Generate new tocken" => provide name for the token, choose Expiration time and define scopes, the click "Create token".
   Ater that copy token text into fiel secret in Jenkins Credential window 
   click "Add" button and pick "Manage hooks" checkbox.

<img src="./Hooks1.png" alt="Hooks1" />
<img src="./Hooks2.png" alt="Hooks2" />
<img src="./Hooks3.png" alt="Hooks3" />

The go in top right corner click on username and choose Configure, then find "API tocken" field and press "Add new tocken" => Generate. Copy new token and go to UI of GitHub repository Settings. Click on WebHooks provide youre server webhook address (http://3.121.127.172:8081/github-webhook/) type of payload (json) and Secret (GitHub Personal token). Choose Events that will trigger hooks (Push and Pull Request). Click add webhook.  
In Recent Deliverys we see that web hooks works propertly.
<img src="./h4.png" alt="Hooks3" />
<img src="./h5.png" alt="Hooks3" />
<img src="./h6.png" alt="Hooks3" />
<img src="./h7.png" alt="Hooks3" />
<img src="./h10.png" alt="Hooks3" />

<h2> * Spin up VM with artifactory and publish result to artifactory </h2>
1. SpinUp Amazon EC-2 on debian. Install artifactory from .deb package https://www.jfrog.com/confluence/display/JFROG/Installing+Distribution#InstallingDistribution-ManualDebianInstallation
http://3.126.153.191:8082/
<img src="./jfrog.png" alt="Jfrog1" />
3. Setup credentials.
4. Create local generic repo with name nom-artifactory.
5. <img src="./jfrog2.png" alt="Jfrog2" />
6. Install Artifactory plugin.
7. Setup "Instance ID" and JFrog Instance URL and credentials
<img src="./jfrogs.png" alt="Jfrog3" />
<img src="./jfrogc.png" alt="Jfrog4" />
8. Start "Buld Job". Job Successfull. Artifact was uploaded.
```
Archiving artifacts
Recording fingerprints
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (uploading artifacts to artifactory storage)
[Pipeline] tool
[Pipeline] envVarsForTool
[Pipeline] withEnv
[Pipeline] {
[Pipeline] rtUpload
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
[consumer_0] Deploying artifact: http://3.126.153.191:8082/artifactory/npm-artifactory/result.tar

GitHub has been notified of this commit’s build result

Finished: SUCCESS
```



<img src="./job.png" alt="Jfrog5" />
<img src="./jlog1.png" alt="Jfrog6" />
<img src="./jlog2.png" alt="Jfrog7" />
<img src="./jlog3.png" alt="Jfrog8" />
<img src="./jlog4.png" alt="Jfrog9" />
<img src="./jlog5.png" alt="Jfrog10" />
<img src="./artif.png" alt="artifactory" />
