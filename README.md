# Sonarqube

## Step 1: Install Sonarqube using Helm. The default login is admin/admin. (https://github.com/helm/charts/tree/master/stable/sonarqube)
```
$ helm install stable/sonarqube
```

```
NAME:   virulent-gerbil
LAST DEPLOYED: Wed Jan 29 12:28:32 2020
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                       AGE
virulent-gerbil-sonarqube-config           2s
virulent-gerbil-sonarqube-copy-plugins     2s
virulent-gerbil-sonarqube-install-plugins  2s
virulent-gerbil-sonarqube-tests            2s

==> v1/Deployment
NAME                       AGE
virulent-gerbil-sonarqube  2s

==> v1/PersistentVolumeClaim
NAME                        AGE
virulent-gerbil-postgresql  2s

==> v1/Pod(related)
NAME                                         AGE
virulent-gerbil-postgresql-546954446f-p76mv  2s
virulent-gerbil-sonarqube-567f4f5999-rdf26   2s

==> v1/Secret
NAME                        AGE
virulent-gerbil-postgresql  2s

==> v1/Service
NAME                        AGE
virulent-gerbil-postgresql  2s
virulent-gerbil-sonarqube   2s

==> v1beta1/Deployment
NAME                        AGE
virulent-gerbil-postgresql  2s
```

## Step 2: Get the application URL by running these commands:
```
$ export POD_NAME=$(kubectl get pods --namespace default -l "app=sonarqube,release=virulent-gerbil" -o jsonpath="{.items[0].metadata.name}")
$ echo "Visit http://127.0.0.1:8080 to use your application"
$ kubectl port-forward $POD_NAME 8080:9000
```

## Step 3: Expose the service using a load balancer using "type: LoadBalancer"
```
$ kubectl edit service/virulent-gerbil-sonarqube
```

## Step 4: Log in the Sonarqube Console (admin/admin) Go to Administration -> MarketPlace -> Install SonarJava -> Restart. This will add quality profiles.
 
## Step 5: Test Locally
```
$ mvn sonar:sonar -Dsonar.host.url=http://acedcb649424f11eaaaf6065a4432dd8-750715036.ap-southeast-2.elb.amazonaws.com:9000
```

## Step 6: Modify Buildspec.yaml file
```
  build:
    commands:
		...
      - export SONARQUBE_URL=http://acedcb649424f11eaaaf6065a4432dd8-750715036.ap-southeast-2.elb.amazonaws.com:9000/
      - mvn clean install sonar:sonar
		...
```

## (Optional): Cleanup
```
$ helm list
NAME       	REVISION	UPDATED                 	STATUS  	CHART          	NAMESPACE
kindly-newt	1       	Mon Oct  2 15:05:44 2017	DEPLOYED	sonarqube-0.1.0	default
$ helm delete kindly-newt
```
