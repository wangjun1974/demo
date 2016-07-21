oc login -u redhat  

oc new-project abdeploy  

oc new-app openshift/php:5.6~https://github.com/cdan/ab-deploy.git -l abdeploy=true --name=myappv1  

oc create -f abservice.json

oc expose service/ab-router --hostname=abdeploy.apps.example.com

for i in {1..100}; do curl http://abdeploy.apps.example.com/; echo ''; done

modify https://github.com/cdan/ab-deploy.git

oc new-app openshift/php:5.6~https://github.com/cdan/ab-deploy.git -l abdeploy=true --name=myappv2

for i in {1..100}; do curl http://abdeploy.apps.example.com/; echo ''; done

oc scale --replicas=5 dc myappv1

for i in {1..100}; do curl http://abdeploy.apps.example.com/; echo ''; done

