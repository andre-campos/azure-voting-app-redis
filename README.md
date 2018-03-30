# Azure Voting App

Forked from https://github.com/Azure-Samples/azure-voting-app-redis

# Running this with minikube
Install [minikube](https://github.com/kubernetes/minikube/releases) (OS dependent). Then run minikube:
```
$ minikube start --vm-driver="virtualbox" --insecure-registry=192.168.99.1:5000
```


## Creating the images
It's necessary to create the images within minikube's image registry (as opposed to your local docker registry). You'll need to do this:
```
eval $(minikube docker-env)
cd azure-vote
docker build . -t microsoft/azure-vote-front:v1
```

## Running the application
```
kubectl create -f azure-vote-all-in-one-redis.yaml
```
This will create a deployment with 3 replicas of the frontend. You can access minikube's dashboard to make sure the application is healthy using this command:
```
minikube dashboard
```
Given that you've got curl installed, you can use it to verify the application is actually running:
```
curl $(minikube service azure-vote-front --url)
```
The output is plain HTML, but you should be able to see the instance information:
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Azure Voting App</title>

    <script language="JavaScript">
        function send(form){
        }
    </script>

</head>
<body>
    <div id="container">
        <form id="form" name="form" action="/"" method="post"><center>
        <div id="logo">Azure Voting App</div>
        <div id="space"></div>
        `**`<p>Instance Version 1; Instance 7050</p>`**`
        <div id="form">
        <button name="vote" value="Cats" onclick="send()" class="button button1">Cats</button>
        <button name="vote" value="Dogs" onclick="send()" class="button button2">Dogs</button>
        <button name="vote" value="reset" onclick="send()" class="button button3">Reset</button>
        <div id="space"></div>
        <div id="space"></div>
        <div id="results"> Cats - 10 | Dogs - 2 </div> 
        </form>        
        </div>
    </div>     
</body>
</html>
```
## Updating the Application

Change the version variable in `main.py` and then rebuild the image with a different tag:
```
docker build . -t microsoft/azure-vote-front:v2
```
After that, use kubectl to update the image:
```
kubectl set image deployments/azure-vote-front azure-vote-front=microsoft/azure-vote-front:v2
```
Wait a few minutes and you should be able to see the new version running with curl:
```
curl $(minikube service azure-vote-front --url)

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Azure Voting App</title>

    <script language="JavaScript">
        function send(form){
        }
    </script>

</head>
<body>
    <div id="container">
        <form id="form" name="form" action="/"" method="post"><center>
        <div id="logo">Azure Voting App</div>
        <div id="space"></div>
        `**`<p>Instance Version 2; Instance 723</p>`**`
        <div id="form">
        <button name="vote" value="Cats" onclick="send()" class="button button1">Cats</button>
        <button name="vote" value="Dogs" onclick="send()" class="button button2">Dogs</button>
        <button name="vote" value="reset" onclick="send()" class="button button3">Reset</button>
        <div id="space"></div>
        <div id="space"></div>
        <div id="results"> Cats - 10 | Dogs - 2 </div> 
        </form>        
        </div>
    </div>     
</body>
</html>
```