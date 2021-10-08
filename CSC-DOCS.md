# Launching MongoDB and websocat for end-users

In order to run mongoDB in Rahti and make it available in Puhti (and anywhere else that Rahti), a websocket proxy must me used. The process is the following:

# Using `oc`

`oc` is the command line tool used by OpenShift, it is similar to the tool `kubectl` used by Kubernetes but with added support for the extra features OpenShift provides over Kubernetes. Puhti should have available the `oc` tool, but it is possible to install it in any computer. see the guide on [how to install the oc tool](https://docs.csc.fi/cloud/rahti/usage/cli/#how-to-install-the-oc-tool)

* Login

```
oc login https://rahti.csc.fi:8443 -u <USER> -p <PASSOWRD>
```
**NOTE**: Replace the values between `<>` with your CSC account user and password.

![oc login](oc-login.png)

It is also possible to use a TOKEN to login, see the article on [how to login with oc]*https://docs.csc.fi/cloud/rahti/usage/cli/#how-to-login-with-oc)

* Create a OpenShift project/namepace

```
oc new project <NAME> --description='csc_project: <NUMBER>'
```
 **NOTE**: Replace the values between `<>` with the project name, and the CSC project number for billing.

![oc new-project](oc-new-project.png)

* Launch mongoDB template

```
oc get template mongodb-persistent -n openshift -o yaml | oc process -f - | oc create -f -
```

![oc template mongodb](oc-template-mongodb.png)

* Launch websocat template

```
git clone https://github.com/lvarin/websocat-template.git
oc process -f websocat-template/websocat-template.yaml -p DATABASE_SERVICE=mongodb -p DATABASE_PORT=27017 | oc create -f -
```

![oc template websocat](oc-template-websocat.png)

* Write down URL

```
oc get route
```

![oc get route](oc-get-route.png)

The Host can be found under `HOST/PORT`.
