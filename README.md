# Cloud Native Jenkins setup

## Stack 
 - Minishift 
 - Jenkins 
 - Jenkins slave for dotnet core
 - dotNet 2.1 core app
 - MongoDB 
## Demo application
- REST API on top of dotNet core 2.1 
- MongoDB for persistency 
## Installation
- Install Minishfit https://docs.okd.io/latest/minishift/getting-started/index.html
- Setup and install Jenkins

Login as system admin 
```bash
oc login -usystem:admin
```
Create new project
```bash
oc new-project toluna
```
Deploy MongoDB template
```bash 
oc create -f https://raw.githubusercontent.com/Dimss/toltask/master/Jenkins/mongodb-ephemeral-template.json -n openshift
```
 Add dotnet2.1 to dotnet tag
```bash
oc tag registry.centos.org/dotnet/dotnet-21-centos7:latest dotnet:2.1 -n openshift
```
Deploy Jenkins
```bash
oc create -f https://raw.githubusercontent.com/Dimss/toltask/master/Jenkins/jenkins.yaml
```
Once Jenkins pod is up and running access to Jenkins UI
Open Jenkins URL in browser and make sure you are able to login 
user and pass: `admin/password`
To get Jenkins URL run
```bash
oc get routes | grep jenkins |awk '{print "http://"$2}'
```
Set following values in in-process script approval (https://jenkins-toluna.192.168.64.10.nip.io/scriptApproval/)
```bash
method hudson.plugins.git.GitSCM getUserRemoteConfigs
method hudson.plugins.git.UserRemoteConfig getUrl
staticMethod groovy.json.JsonOutput toJson java.lang.Object
```
Deploy Jenkins Pipeline Build config
```bash
oc create -f https://raw.githubusercontent.com/Dimss/toltask/master/coreapitest/ocp/ci/bc.yaml
``` 
Start build and refresh Jenkins UI to see the Pipeline progress execution
```bash
oc start-build coreapitest
```
Once the Pipeline is finished you can access to the application 
Exec application status URL , should response with host name
```bash
oc get routes | grep coreapitest |awk '{print "http://"$2"/api/system"}' | xargs curl
``` 
Exec application DB access, should response with empty array
```bash
oc get routes | grep coreapitest |awk '{print "http://"$2"/api/Person"}' | xargs curl
``` 

   