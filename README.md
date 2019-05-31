# Cloud Native Jenkins setup

## Stack 
 - Minishift 
 - Jenkins 
 - Jenkins slave for dotnet core
 
## Installation
- Install Minishfit https://docs.okd.io/latest/minishift/getting-started/index.html
- Setup and install Jenkins 
```bash
# Login as system admin
oc login -usystem:admin
# Create new project
oc new-project toluna
# Deploy MongoDB template 
oc create -f https://raw.githubusercontent.com/Dimss/toltask/master/Jenkins/mongodb-ephemeral-template.json -n openshift 
# Add dotnet2.1 to dotnet tag
oc tag registry.centos.org/dotnet/dotnet-21-centos7:latest dotnet:2.1 -n openshift
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
# Once the Pipeline is finished you can access to the application 
# Exec application status URL , should response with host name 
oc get routes | grep coreapitest |awk '{print "http://"$2"/api/system"}' | xargs curl
# Exec application DB access, should response with empty array 
oc get routes | grep coreapitest |awk '{print "http://"$2"/api/Person"}' | xargs curl
```

   