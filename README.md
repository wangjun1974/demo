##1, rolling update  
- oc new-app openshift/php:5.6~https://github.com/cdan/ab-deploy.git -l abdeploy=true --name=myappv1  
- oc edit dc myappv1 (intervalSeconds: 2)  add readiness probe under imagePullPolicy

                readinessProbe:    
                  failureThreshold: 3  
                  httpGet:  
                    path: /  
                    port: 8080  
                    scheme: HTTP  
                  periodSeconds: 5  
                  successThreshold: 1  
                  timeoutSeconds: 1  
- oc scale --replicas=6 dc myappv1
- oc expose service/myappv1 --hostname=rolling.apps.example.com  
- while true; do curl http://rolling.apps.example.com/; sleep 1; done;  
- modify https://github.com/cdan/ab-deploy.git  
- oc start-build myappv1  
- no http failure during update.   

##2, ab testing
- oc login -u redhat  
- oc new-project abdeploy  
- oc new-app openshift/php:5.6~https://github.com/cdan/ab-deploy.git -l abdeploy=true --name=myappv1  
- oc create -f abservice.json
- oc expose service/ab-router --hostname=abdeploy.apps.example.com
- for i in {1..100}; do curl http://abdeploy.apps.example.com/; echo ''; done
- modify https://github.com/cdan/ab-deploy.git
- oc new-app openshift/php:5.6~https://github.com/cdan/ab-deploy.git -l abdeploy=true --name=myappv2
- for i in {1..100}; do curl http://abdeploy.apps.example.com/; echo ''; done 
- oc scale --replicas=5 dc myappv1
- for i in {1..100}; do curl http://abdeploy.apps.example.com/; echo ''; done

##3, blue green deployment
- oc new-app openshift/php:5.6~https://github.com/cdan/ab-deploy.git -l abdeploy=true --name=myappv1
- oc expose service/myappv1 --hostname=bg.apps.example.com
- modify https://github.com/cdan/ab-deploy.git
- oc new-app openshift/php:5.6~https://github.com/cdan/ab-deploy.git -l abdeploy=true --name=myappv2
- while true; do curl http://bg.apps.example.com/; echo; sleep 1; done;
- oc edit route myappv1 ; change myappv1->myappv2
- request direct to v2 without error.

