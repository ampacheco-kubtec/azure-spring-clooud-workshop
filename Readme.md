# Simple demo de Azure Spring Service

## Initial Setup
```Bash
az login
```

### Install az spring module if not present
```Bash
az extension add -n spring-cloud -y
```

### Setup env vars
```
echo ${AZ_GROUP:=spring-rg} # Resource Group
echo ${AZ_SPRING_SERVICE:=spring-service} # Azure Spring Service
```

### Create Resource Group
```
az group create -g ${AZ_GROUP} -l eastus
az spring-cloud create -g "$AZ_GROUP" -n "$AZ_SPRING_SERVICE" --sku standard
```

### Setup Defaults
```
az configure --defaults group=${AZ_GROUP}
az configure --defaults spring-cloud=${AZ_SPRING_SERVICE}
```
## Setup Config Server

## Create the App
```
az spring-cloud app create -n todo-service
```

## DB Setup
```
az cosmosdb create --name ${AZ_SPRING_SERVICE}-cosmosdb --kind MongoDB
az cosmosdb mongodb database create --account-name ${AZ_SPRING_SERVICE}-cosmosdb --name "cosmosdb-todos"
```

## Build the App
```
curl https://start.spring.io/starter.tgz -d dependencies=web,data-mongodb,cloud-eureka,cloud-config-client -d baseDir=todo-service -d bootVersion=2.2.5.RELEASE | tar -xzvf -
```

## Deploy the App
```
./mvnw clean package -DskipTests -Pcloud
az spring-cloud app deploy -n todo-service --jar-path target/demo-0.0.1-SNAPSHOT.jar
```

## Troubleshoot Deployment
```
az spring-cloud app logs -n todo-service -f
```


## Integrate Gateway
:arrow_right: [Gateway Setup](https://github.com/ampacheco-kubtec/azure-spring-clooud-gateway)
