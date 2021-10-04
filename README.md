# Websocat server

## **THIS IS A BETA TEMPLATE, not supported**

This template deploys a websocket proxy server using `websocat`. This proxy server
will run in Kubernetes (OpenShift OKD/Rahti), and it will create a connection to the
service configured by the user. Then it will create a Websocket entry point
listening to traffic. The "other side of the tunnel", in the client side, requires
to run `websocat` as well. The following drawing example uses mongoDB as `service`.

![Proxy](websocat-diagram.drawio.png)

Traffic will come to the `websocat` client to any given configured port, it will be
then translated to be transmitted suing a "binary websocket", and relayed to the
websocket entry point in OpenShift OKD/Rahti. This traffic will be "translated back"
to the original format before `websocat` was involved and relayed to the configured
service in the configured port.

This template is written with OpenShift OKD/Rahti in mind, but it should work in any Kubernetes with minor modifications.

## Deploy this template

The process has 3 steps:

1. Login into Rahti/Kubernetes, you can follow these guides: to [get access to Rahti](https://docs.csc.fi/cloud/rahti/access/) and to [login with OC](https://docs.csc.fi/cloud/rahti/usage/cli/#how-to-login-with-oc).

2. Create a namespace, follow the [Creating a project](https://docs.csc.fi/cloud/rahti/usage/projects_and_quota/#creating-a-project) guide.

3. Deploy the database or service you want `websocat` to proxy. In this example we use a `mongodb` service deployed in the standard port `27017`.

4. Deploy the template

```bash
oc process -f websocat-template.yaml -p DATABASE_SERVICE=mongodb \
           -p DATABASE_PORT=27017 | oc create -f -
```

Change `mongodb` and `27017` for the service and port where the deployment is. The websocket should be ready in few minutes in:

```bash
wss://websocat-<project-name>.rahtiapp.fi
```

Where `<project-name>` is the name of the project you created.

## Build the image

A prebuilt image is already included in the template. These instructions are in case you need to rebuild it.

As `websocat` is coded on `rust`, the docker file simply uses `cargo` to build the image:

```Dockerfile
FROM rust

RUN cargo install --features=ssl websocat

ENTRYPOINT ["websocat"]
```

to build it simply do:

```bash
docker build . -t lvarin/websocat
```

Change `lvarin/websocat` to the name and tag of the image you want to build.
