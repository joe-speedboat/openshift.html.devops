# openshift.html.devops
OpenShift Imagestream learning example

# DESCRIPTION:

Openshift 3.9 Imagestream learning notes

# NAMES:

GIT: https://github.com/joe-speedboat/openshift.html.devops.git

# IMPORTANT NOTES:


## get all the imagestreams
oc get is -n openshift   

## inspect nginx imagestream
oc describe is nginx -n openshift   

## setup new project
oc new-project is-demo

## setup the dev environment
oc new-app --name=html-dev  nginx:1.10~https://github.com/joe-speedboat/openshift.html.devops.git#master   
oc get all   
oc logs -f builds/html-dev-1   

oc get svc   
oc expose svc/html-dev --hostname=html-dev.app.lab.local   
oc get route   

curl http://html-dev.app.lab.local   

## show this app to the qa team
oc get is   
oc tag docker-registry.default.svc:5000/is-demo/html-dev:latest is-demo/html-qa:1.0   
oc get is   
oc new-app --name=html-qa --image-stream="is-demo/html-qa:1.0"   
oc expose svc/html-qa --hostname=html-qa.app.lab.local   
curl html-qa.app.lab.local   

## now go and make some changes to the git repo, then push it to github
## now lets build the latest dev release
oc start-build html-dev   
oc status   
oc get pods   

## check dev application for latest changes
curl http://html-dev.app.lab.local   

## check if qa application remains in desired state
curl html-qa.app.lab.local   

## now commit the new dev branch to qa branch
oc get is   
oc tag docker-registry.default.svc:5000/is-demo/html-dev html-qa:1.1   

## change the imagestream to newer release
oc edit dc/html-qa   
oc get dc   
oc get pods   

## check if qa application is reflecting latest changes from v1.1
curl html-qa.app.lab.local   

## now we rollback the qa release to v1.0
oc edit dc/html-qa   
oc get dc   
oc get pods   

## check if qa application is reflecting the rollbacked version v1.0
curl html-qa.app.lab.local   
