# Deploy Application

Create an OpenShift project:

```bash
oc new-project aro
```

Clone the application repo:

```bash
git clone https://github.com/dudash/openshiftexamples-aro-webapp-azuresql
cd openshiftexamples-aro-webapp-azuresql
```

Open the `./database/setupCosmos.sh` script in your favorite editor.  Edit the `resource` and `location`.  **Make sure to use the same resource group and location of your ARO cluster.**

> Output (sample)

```bash
uniqueId=$RANDOM
resource="rg-AroWebAppsExample"
location="East US"
...
```

Run the Cosmos script:

```bash
./database/setupCosmos.sh
```

Set the connection string to an environment variable:

```bash
export COSMOS_CONNECTION_STRING=$(az cosmosdb list-connection-strings -g $resource --name $accountName)
```

Build the container for the API

> TODO: Modify build so it doesn't require running maven locally

```bash
mvn package
oc new-build --binary --name=modern-webapp-api -l app=modern-webapp-api
oc patch bc/modern-webapp-api -p "{"spec":{"strategy":{"dockerStrategy":{"dockerfilePath":"src/main/docker/Dockerfile.jvm"}}}}"
oc start-build modern-webapp-api --from-dir=. --follow
```

Deploy the API:

```bash
oc new-app --name=modern-webapp-api --image-stream=modern-webapp-api:latest -e CHECKSUM_SECRET=somethingsecret -e QUICKAUTH_USER=user -e QUICKAUTH_PASSWORD=password
oc expose service modern-webapp-api
```

Expose the API externally:

```bash
oc expose service modern-webapp-api
```

Connect the app to CosmosDB:

```bash
oc set env deployment/modern-webapp-api QUARKUS_MONGODB_CONNECTION_STRING=$COSMOS_CONNECTION_STRING
```

Set API access to environment variables:

```bash
export API_SERVER_URL=$(oc get route modern-webapp-api --template='http://{{.spec.host}}:{{.spec.port}}')
export API_SERVER_WEBSOCKET=$(oc get route modern-webapp-api --template='ws://{{.spec.host}}:{{.spec.port}}')
```

Deploy the UI

```bash
oc new-app nodeshift/ubi8-s2i-web-app:latest~https://github.com/dudash/openshiftexamples-aro-webapp-azuresql \
--context-dir=modern-webapp/ui \
--build-env OUTPUT_DIR=dist \
--build-env DEBUG_INPUT=false \
--build-env API_SERVER_URL=$API_SERVER_URL \
--build-env API_SERVER_WEBSOCKET_URL=$API_SERVER_WEBSOCKET \
--name=modern-webapp-ui
```

Expose the UI externally

```bash
oc expose service openshift-highscores-phaser-ui
```

Navigate to the UI and you should see:

> TODO: Include UI image