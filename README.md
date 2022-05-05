# Task 1
```
gcloud config set compute/zone us-east1-b
```
```
git clone https://source.developers.google.com/p/$DEVSHELL_PROJECT_ID/r/sample-app
```
```
gcloud container clusters get-credentials jenkins-cd
```
```
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=$(gcloud config get-value account)
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &
printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
```

### Copy outupt (Password to access jenkins)

##### 1. Click on web preview On cloud shell Page
##### 2. Enter Username =admin
##### 3. Password= [Copy from previous Command] 



### Run Below Command in Shell: 

```
kubectl get svc
kubectl get service gceme-frontend -n production
```

##### Click on Manage Jenkins  -->  Manage Credentials

##### Under Stores scoped to Jenkins click on global --> Add some credentials

##### Select Kind  "google service account from metadata"
##### Click ok

##### Click on dashboard and select new item

##### Enter an item name as 'sample-app' and Select Multibranch Pipeline --> ok

##### Under Branch Source Select: Git


##### Project Repository Copy below path and Replace your Project ID with [PROJECT ID]
```

https://source.developers.google.com/p/[PROJECT_ID]/r/sample-app
```


##### Select your project id as credential

##### select Scan Multibranch Pipeline Triggers

##### To select Click on check box of  Periodically if not otherwise run  and select interval as 1 min

##### Now click on sample app (if needed) then click on master 

Run Below Command 

```
git init
git config credential.helper gcloud.sh
git remote add origin https://source.developers.google.com/p/$DEVSHELL_PROJECT_ID/r/sample-app
```



##### Replace email address with your username given in lab 
```
git config --global user.email "[EMAIL_ADDRESS]"
```
##### Replace username  with your username given in credentials
```
git config --global user.name "[USERNAME]"
```
##### Now Run below Command
```
git add .
git commit -m "Initial commit"
git push origin master
```

#### NOTE: Wait for the Pipeline and the master to complete Successfully. To monitor the master, go to Jenkins homepage and click on the Sample-App option, inside that you will see the progress of master. Wait until it turns blue. Refresh the page to see the progress update. Make sure that both turn blue.

##### Click on open Editor On Shell screen 

##### Open file Main.go 

##### Line Number 46: change version 1.0.0 to 2.0.0

##### vi html.go //Change the two instances of "card blue" to "card orange"

```
git add Jenkinsfile html.go main.go
git commit -m "Version 2.0.0"
git push origin new-feature
```

##### Wait for 1-2 mins and you will see the new-feature branch. Monitor the new-feature branch to be completed successfully. It will turn blue once succeeded.

```
kubectl proxy &
```
##### If it stalls, press Ctrl + C to exit out.
```
curl \
http://localhost:8001/api/v1/namespaces/new-feature/services/gceme-frontend:80/proxy/version
```
```
kubectl get service gceme-frontend -n production
```
##### Open the IP in a new browser tab to check the application.
```
git checkout -b canary
git push origin canary
 ```
##### Wait for 1-2 mins and you will see the canary branch. Monitor the canary branch to be completed successfully. It will turn blue once succeeded.
```
export FRONTEND_SERVICE_IP=$(kubectl get -o \
jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)
while true; do curl http://$FRONTEND_SERVICE_IP/version; sleep 1; done
```
##### If you keep seeing 1.0.0, try running the above commands again. Once you've verified that the above works, end the command with Ctrl + C.

```
git checkout master
git merge canary
git push origin master
```
##### Wait for 1-2 mins and you will see the master branch. Monitor the master branch to be completed successfully. It will turn blue once succeeded.
```
export FRONTEND_SERVICE_IP=$(kubectl get -o \
jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)
while true; do curl http://$FRONTEND_SERVICE_IP/version; sleep 1; done
```
##### Once again, if you see instances of 1.0.0 try running the above commands again. You can stop this command by pressing Ctrl + C.

```
kubectl get service gceme-frontend -n production
```
##### Check the IP in a new browser window, you will notice that the color and the version of the app must be updated.


NOTE: Now click on the green Score fly-out on the top right and click Run Step for each TASK.
