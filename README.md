# oke-couchbase

This is a walkthrough of setting the [Couchbase Operator](https://developer.couchbase.com/documentation/server/current/operator/overview.html) up on [Oracle Kubernetes Engine (OKE)](https://cloud.oracle.com/containers/kubernetes-engine).

## Prerequisites
First you're going to need to setup an Oracle Cloud account, your environmental variables, an OKE cluster and your kubectl.  It sounds like a lot, but there's a nice walkthrough [here](https://github.com/cloud-partners/oke-how-to) that should help.

## Deploying the Operator
Great, you made it!

Once you have an OKE cluster deployed and a running kubectl, you're ready to deploy the Operator.  The documentation on that is [here](https://docs.couchbase.com/operator/1.0/install-kubernetes.html).

First, you need to download stuff.  This includes a customer kubectl as Couchbase has forked it and created their own.  There are per platform binaries for those.  This downloads the Mac one:

    curl -O https://s3.amazonaws.com/packages.couchbase.com/kubernetes/1.0.0/couchbase-autonomous-operator-kubernetes_1.0.0-macos_x86_64.zip
    unzip couchbase-autonomous-operator-kubernetes_1.0.0-macos_x86_64.zip
    cd couchbase-autonomous-operator-kubernetes_1.0.0-macos_x86_64
    ls

That gives this:

![](images/01%20-%20download.png)

To create the deployment and check it deployed, run this:

    kubectl create -f cluster-role.yaml
    kubectl create serviceaccount couchbase-operator --namespace default
    kubectl create clusterrolebinding couchbase-operator --clusterrole couchbase-operator --serviceaccount default:couchbase-operator
    kubectl create -f operator.yaml

When you've done all that, you can check the Operator deployed with:

    kubectl get deployments

You should see something like this:

![](images/02%20-%20get%20deployments.png)

## Deploying a Couchbase Cluster

We're there!  Time to get a live cluster.  Run this:

    kubectl create -f secret.yaml
    kubectl create -f couchbase-cluster.yaml

That should give this:

![](images/03%20-%20cluster%20created.png)

You can view the Couchbase and operator pods by running:

    kubectl get pods

This gives:

![](images/04%20-%20get%20pods.png)

## Accessing the Couchbase Web UI

You've now got a cluster.  But to use it you probably want to set up port forwarding.  To do that run:

    kubectl port-forward cb-example-0000 8091:8091

Leave that command running:

![](images/05%20-%20port%20forward.png)

Now open up a browser to http://localhost:8091

![](images/06%20-%20login%20screen.png)

The username is `Administrator` and password is `password`.  And now you're in!

![](images/07%20-%20console.png)

## Deleting the Operator
If you want to delete the Operator, you can run this:

    kubectl delete deployment couchbase-operator
    kubectl delete crd couchbaseclusters.couchbase.com

That gives this:

![](images/08%20-%20delete.png)
