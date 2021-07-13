
# How to run this 3scale CI/CD demo:

## Configuring 3scale toolbox

Indicate the 3scale DEV and TEST instances, you need to define two remotes in the 3scale toolbox installation (locally installed or running the 3scale toobox contanier):
 
This is an example of the format used to point each 3scale admin instance:  

- **3scale remote add <em>name</em> <em>url</em>** where <em>url</em> consist of the access tokens generated in the administration console and the 3scale admin URL of each instance. 
```sh
   3scale remote add quitaladev https://XXXXXXXXXXXXXXXXXXXX@3scale-admin.apps.ocp4.quitala.eu 
```  
```sh  
   3scale remote add quitalauat https://YYYYYYYYYYYYYYYYYYYY@red-hat-admin.apps.ocp4.quitala.eu 
```  
- Upload the 3scale toolbox configuration (<em>3scalerc.yaml</em>) into the OpenShift project used to run the pipeline. 

```sh
oc create secret generic 3scale-toolbox --from-file=$HOME/.3scalerc.yaml
```

## Deploying and Configuring the BuildConfig 

Deploy the JenkinsPipeline BuildConfig defined in this file <em>cicd-demo-pipeline.yaml</em> in your project.
This field includes a lot of parameters that allows you customize the behaviour of your pipelines.

Please take special attention to the following parameters:
  -   **API_BASE_SYSTEM_NAME**: unique identified in 3scale, if it already exists the pipeline re-import the last version of the API spec file.
  -   **DEV_STAGING_PUBLIC_BASE_URL,DEV_PRODUCTION_PUBLIC_BASE_URL...:** these URLs should be created upfront running the pipeline.
  -   **TARGET_INSTANCE_DEV, TARGET_INSTANCE_TEST**: 3scale remote name which identifies the environment, in the example before "quitaladev" and "quitalauat".
  -   **DEV_DEVELOPER_ACCOUNT_ID, TEST_DEVELOPER_ACCOUNT_ID** : 3scale account id which will create the test application (usually john identifier).

```sh
oc new-app -f cicd-demo-pipeline.yaml -p API_BASE_SYSTEM_NAME=stockAPI3 -p DEV_STAGING_PUBLIC_BASE_URL=https://stock-dev-staging.apps.my-cluster.ocp4.openshift.es -p DEV_PRODUCTION_PUBLIC_BASE_URL=https://stock-dev-production.apps.my-cluster.ocp4.openshift.es -p TEST_STAGING_PUBLIC_BASE_URL=https://stock-test-staging.apps.my-cluster.ocp4.openshift.es -p TEST_PRODUCTION_PUBLIC_BASE_URL=https://stock-test-production.apps.my-cluster.ocp4.openshift.es -p OPENAPI_SPECIFICATION_FILE=stock-spec-v1.0.json -p TARGET_INSTANCE_DEV=devenv -p TARGET_INSTANCE_TEST=testenv -p DEV_DEVELOPER_ACCOUNT_ID=8

oc start-build cicd-demo-api-pipeline
```

## For deleting the pipeline

```sh
oc delete buildconfig cicd-demo-api-pipeline
```

