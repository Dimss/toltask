# Cloud Native Jenkins setup

## Stack 
 - Minishift 
 - Jenkins 
 - Jenkins slave for dotnet core
 
## Installation
- Install Minishfit https://docs.okd.io/latest/minishift/getting-started/index.html
- Setup and install Jenkins 
```bash
# Create new project
oc new-project toluna
# Deploy mongo template 
oc process -f https://raw.githubusercontent.com/Dimss/toltask/master/Jenkins/mongodb-ephemeral-template.json  | oc create -f -
# Add dotnet2.1 to dotnet tag
oc tag registry.centos.org/dotnet/dotnet-21-centos7:latest dotnet:2.1
# Deploy Jenkins
oc create -f https://raw.githubusercontent.com/Dimss/toltask/master/Jenkins/jenkins.yaml
# Once Jenkins pod is up and running access to Jenkins UI
# Get Jenkins URL, for example: http://jenkins-toluna.192.168.64.10.nip.io
# Open Jenkins URL in browser 
# and make sure you are able to login 
# user and pass: admin/password
oc get routes | grep jenkins |awk '{print "http://"$2}'
# Deploy Jenkins Pipeline Build config 
oc create -f https://raw.githubusercontent.com/Dimss/toltask/master/coreapitest/ocp/ci/bc.yaml
# Start build and refresh Jenkins UI to see the Pipeline progress execution
oc start-build coreapitest 
```

   