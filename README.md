# IBM API Connect Monitoring with Grafana and Prometheus on Red Hat OpenShift Container Platform

Creating a custom Grafana dasboard on Red Hat OpenShift Container Platform in order to monitor IBM API Connect by using Grafana and Prometheus

## Getting Started

These instructions will guide you to install Grafana on Red Hat OpenShift and import a custom Grafana dashboard. Follow the installation guide and configure your own Grafana Dashboard to monitor your IBM API Connect.

## Prerequisites

* Red Hat OpenShift Container Platform
* Installed and Configured IBM API Connect on Red Hat OpenShift

## Steps
1. [Grafana Installation on Red Hat OpenShift v3](#grafana-installation-on-red-hat-openShift-v3)
2. [Import the custom IBM API Connect Dashboard on Grafana](#import-the-custom-ibm-api-connect-dashboard-on-grafana)

## Installing

### 1. Grafana Installation on Red Hat OpenShift v3

The problem is you can not edit/install or create any additional Grafana dashboard in Red Hat OpenShift v3. There is no options available to import a custom grafana dashboard.

A step by step series of commands will help you to install Grafana on Red Hat OpenShift Container Platform.

Follow the instructions and apply the below commands on Red Hat OpenShift

Create a new project named apic-monitoring.

```
oc new-project apic-monitoring
```

Export secret grafana-datasources from openshift-monitoring namespace.

```
oc get secrets grafana-datasources -n openshift-monitoring --export -o yaml > grafana-datasources.yaml
```

Create new secret using exported secret from openshift-monitoring namespace in your namespace apic-monitoring

```
oc create -f grafana-datasources.yaml
```
> Important Note: Check the Grafana version on your current monitoring stack from openshift-monitoring namespace.

Create a new-app using Grafana Docker image of same version with your Grafana version on your current monitoring stack which you checked on previous step.

```
oc new-app --name=apicdashboard docker.io/grafana/grafana:<YOUR_GRAFANA_VERSION>
```

Add a volume using secret grafana-datasources which we exported earlier in second step of Grafana Installation.

```
oc set volume dc/apicdashboard --add --name=grafana-dashsources --type=secret --secret-name=grafana-datasources --mount-path=/etc/grafana/provisioning/datasources --namespace=apic-monitoring
```

Create one more EmptyDir volume at /var/lib/grafana

```
oc set volume dc/apicdashboard --add --name=grafana-storage --mount-path=/var/lib/grafana --namespace=apic-monitoring
```
> Verify the volumes that you set by describing dc/apicdashboard deploymentconfig  

Expose the service and join the namespace networks to connect to each other

```
oc expose svc/apicdashboard
oc adm pod-network join-projects --to=apic-monitoring openshift-monitoring
```

Find the route on the Red Hat OpenShift Container Platform for your newly created grafana dashboard and use the default credentials <admin:admin> to login your Grafana.

### 2. Import the custom IBM API Connect Dashboard on Grafana 

## Built With

* [Red Hat OpenShift Container Platform](https://www.openshift.com)
* [IBM API Connect](https://www.ibm.com/cloud/api-connect)
* [GRAFANA](https://grafana.com)

## Author

* **Mehmet Esat Cakmak** - *IBM Cloud and Cognitive*
