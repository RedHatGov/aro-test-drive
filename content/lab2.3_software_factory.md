# Software Factory

Let's deploy the Software Factory.  These instructions are modified from the [official quick start][1].

Create an OpenShift project for the software factory:

```bash
oc new-project devsecops
```

Import the RedHatGov operator catalog:

```bash
oc apply -f - << EOF
apiVersion: operators.coreos.com/v1alpha1
kind: CatalogSource
metadata:
  name: redhatgov-operators
  namespace: openshift-marketplace
spec:
  sourceType: grpc
  image: quay.io/redhatgov/operator-catalog:latest
  displayName: Red Hat NAPS Community Operators
  publisher: RedHatGov
EOF
```

Delete any limit ranges:

```bash
oc delete limitrange --all -n devsecops
```

Install the operator:

```bash
oc apply -f - << EOF
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: ploigos-software-factory-operator
  namespace: devsecops
spec:
  channel: alpha
  installPlanApproval: Automatic
  name: ploigos-software-factory-operator
  source: redhatgov-operators
  sourceNamespace: openshift-marketplace
EOF
```

Create the Software Factory Infrastructure:

```bash
oc apply -f - << EOF
apiVersion: redhatgov.io/v1alpha1
kind: TsscPlatform
metadata:
  name: tsscplatform
spec:
  tsscPlatform:
    helmRepository: 'https://ploigos.github.io/ploigos-charts'
    services:
      artifactRepository:
        managed: true
        name: nexus
      containerRegistry:
        managed: true
        name: nexus
      continuousDeployment:
        managed: true
        name: argocd
      continuousIntegration:
        managed: true
        name: jenkins
      sourceControl:
        managed: true
        name: gitea
      staticCodeAnalysis:
        managed: true
        name: sonarqube
      uat:
        managed: true
        name: selenium
EOF
```

Deploy a Software Factory Pipeline:

> TODO: Modify for the Web Application API

```bash
oc apply -f - << EOF
apiVersion: redhatgov.io/v1alpha1
kind: TsscPipeline
metadata:
  name: tsscpipeline-reference-app
spec:
  appName: ref-quarkus-mvn-jenkins-std
  appRepo:
    destinationRepoName: reference-quarkus-mvn_jenkins_workflow-standard
    sourceUrl: >-
      http://gitea.tssc.rht-set.com/akrohg/reference-quarkus-mvn_jenkins_workflow-standard.git
  autoStartPipeline: false
  helmRepo:
    destinationRepoName: reference-quarkus-mvn-cloud-resources_jenkins_workflow-standard
    sourceUrl: >-
      http://gitea.tssc.rht-set.com/akrohg/reference-quarkus-mvn-cloud-resources_jenkins_workflow-standard.git
  serviceName: fruit
EOF
```

Navigate to Jenkins to view the pipeline:

```bash
oc get route jenkins --template "https://{{.spec.host}}"
```

> TODO: Make a change and push start a new pipeline

[1]: https://github.com/ploigos/ploigos-software-factory-operator#quick-start