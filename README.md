# relayr-test
# Solution
## Development
The solution can be explained below.
- The solution has been desgined in python language.
- This is a simple python application to disply the user name on http endpoint from REDIS and MYSQL database.
- Webapp chart has been created to deploy a Python Flask container and a Redis container
- mysql chart has been created to deploy MySQL container
- When data is retrieved through the Python REST API, it first checks within Redis cache before accessing MySQL
- Each time data is fetched from MySQL, it gets cached in the Redis container of the same Pod as the Python Flask container

Architecture



### Assumption
- The app has been tested successfully on minikube installed on my windows laptop.
- I have created persistence volume on C:/Users folder.
- Helm is initiated and tiller is running.

### Manifest file for Deployment using Helm
#### Webapp
- Deployment: configuration/helmcharts/webapp/templates/deployment.yaml
- service: configuration/helmcharts/webapp/templates/service.yaml
- values: configuration/helmcharts/webapp/values.yaml
#### mysql
- Deployment: configuration/helmcharts/mysql/templates/deployment.yaml
- service: configuration/helmcharts/mysql/templates/service.yaml
- secrets: configuration/helmcharts/mysql/templates/mysql-secret.yaml
- persistence storage: configuration/helmcharts/mysql/templates/pv.yaml
- persistence volume claim: configuration/helmcharts/mysql/templates/pvc.yaml
- values: configuration/helmcharts/mysql/values.yaml

## Deployment
- Deployment on minikube windows with hard coded image. 
### Installation steps
- git clone https://github.com/vinga2805/relayr-test.git
- cd relayr-test/configurations/helmcharts
- helm install --name mysql mysql
- helm install --name webapp webapp
- minikune service webapp --url
- Initialize the database with sample schema
- curl http://$NODE_IP:$NODE_PORT/init
- Insert some sample data via postman
- curl -i -H "Content-Type: application/json" -X POST -d '{"uid": "1", "user":"Messi"}' http://$NODE_IP:$NODE_PORT/users/add
- curl -i -H "Content-Type: application/json" -X POST -d '{"uid": "2", "user":"Ronaldo"}' http://$NODE_IP:$NODE_PORT/users/add
- curl -i -H "Content-Type: application/json" -X POST -d '{"uid": "3", "user":"Muller"}' http://$NODE_IP:$NODE_PORT/users/add
- curl -i -H "Content-Type: application/json" -X POST -d '{"uid": "4", "user":"Kroos"}' http://$NODE_IP:$NODE_PORT/users/add
- Access the data
- curl http://$NODE_IP:$NODE_PORT/users/1
- The second time you access the data, it appends '(c)' indicating that it is pulled from the Redis cache
- curl http://$NODE_IP:$NODE_PORT/users/1
