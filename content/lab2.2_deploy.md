# Deploy Application

Create an OpenShift project for the application:

```bash
oc new-project aro
```

Delete any limit ranges:

```bash
oc delete limitrange --all -n aro
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

Note the `Primary MongoDB Connection String`.  Set the connection string to an environment variable:

> Note: Make sure to include the double quotes

```bash
export COSMOS_CONNECTION_STRING=<YOUR_PRIMARY_CONNECTION_STRING>
```

Build the container for the API:

```bash
oc import-image --confirm ubi8/openjdk-11 --from=registry.access.redhat.com/ubi8/openjdk-11
```

```bash
oc new-build openjdk-11 https://github.com/dudash/openshiftexamples-aro-webapp-azuresql --context-dir=modern-webapp/api --name=modern-webapp-api -l app=modern-webapp-api
```

Wait for the build to complete:

```bash
oc wait builds/modern-webapp-api-1  --for=condition=Complete --timeout=300s
```

Deploy the API:

```bash
oc new-app --name=modern-webapp-api --image-stream=modern-webapp-api:latest -e CHECKSUM_SECRET=somethingsecret -e QUICKAUTH_USER=user -e QUICKAUTH_PASSWORD=password
oc expose service modern-webapp-api
```

Expose the API externally:

```bash
oc expose service modern-webapp-api --port=8080
```

Connect the app to CosmosDB:

```bash
oc set env deployment/modern-webapp-api QUARKUS_MONGODB_CONNECTION_STRING=$COSMOS_CONNECTION_STRING
```

Set API access to environment variables:

```bash
export API_SERVER_URL=$(oc get route modern-webapp-api --template='http://{{.spec.host}}')
export API_SERVER_WEBSOCKET=$(oc get route modern-webapp-api --template='ws://{{.spec.host}}')
```

> TODO: Add Data Grid caching layer

Deploy the UI:

```bash
oc new-app nodeshift/ubi8-s2i-web-app:latest~https://github.com/dudash/openshiftexamples-aro-webapp-azuresql \
--context-dir=modern-webapp/ui \
--build-env OUTPUT_DIR=dist \
--build-env DEBUG_INPUT=false \
--build-env API_SERVER_URL=$API_SERVER_URL \
--build-env API_SERVER_WEBSOCKET_URL=$API_SERVER_WEBSOCKET \
--name=modern-webapp-ui
```

Wait for the deployment to complete:

```bash
oc wait deploy/modern-webapp-ui  --for=condition=Available --timeout=300s
```

Expose the UI externally

```bash
oc expose service modern-webapp-ui
```

Navigate to the UI in the browser:

```bash
echo $(oc get route modern-webapp-ui --template='http://{{.spec.host}}')
```

You should see:

![Application UI](images/app_ui.png)
