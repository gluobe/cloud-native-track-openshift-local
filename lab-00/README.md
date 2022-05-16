# Lab 01 - OpenShift Local

To use OpenShift on our laptop we're going to use CRC (OpenShift Local):

https://github.com/code-ready/crc

CRC is a tool that helps you run OpenShift locally by running a single-node OpenShift cluster inside a VM. You can try out OpenShift or develop with it, day-to-day, on your local host.

## Task 0: Installing OpenShift Local

If you already followed all steps in the prerequisites repo you can skip this step and move to `Task 1`.

If you did not, follow all steps in the [prerequisites repo](https://github.com/gluobe/cloud-native-track-prerequisites/tree/main/prereq-03-openshift-local)

## Task 1: Enable OpenShift Local

```
crc start
```

## Task 2: Test OpenShift Local

Configure oc (The openshiftconfig binary) on your terminal.

```
eval $(crc oc-env)
```

Now login using `oc` as the `kubeadmin` user:

```
oc login -u kubeadmin https://api.crc.testing:6443
```

> NOTE: the password of the `kubeadmin` user is shown as output when running the `crc start` command in task 1

Verify that this works:

```
oc get cm   

---

NAME                       DATA   AGE
kube-root-ca.crt           1      15d
openshift-service-ca.crt   1      15d
```

## Task 3: Login as the developer user

```
oc login -u developer https://api.crc.testing:6443
```

> NOTE: the password of the `developer` user is also `developer`

## Task 4: Add local DNS records

In order for the labs to work you will need to add the following line to your `/etc/hosts` file:

```
127.0.0.1 welcome-lab-01.apps-crc.testing time-lab-02.apps-crc.testing php-lab-03.apps-crc.testing 
```

### Manual

You can open the file using `vi`, `nano` or your favorite editor and add the complete line at the end of the file:

```
sudo vi /etc/hosts
or
sudo nano /etc/hosts
```

> NOTE: you will be prompted for your MacBook password

### Scripted

Issue the following command:

```
sudo sh -c 'echo "127.0.0.1 welcome-lab-01.apps-crc.testing time-lab-02.apps-crc.testing php-lab-03.apps-crc.testing " >> /etc/hosts'
```

> NOTE: you will be prompted for your MacBook password


pricelist-lab-04
pricelist2-lab-04
blue-route-lab-06
bluegreen-lab-07
scm-web-hooks-lab-08
node-app-lab-10