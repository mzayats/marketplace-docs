.. _service_k8s:

==================
Service Kubernetes
==================

OpenNebula `Marketplace Appliance <https://marketplace.opennebula.systems/appliance/placeholder>`_  with preinstalled `Kubernetes <https://kubernetes.io/>`_ service.

Without any parameters provided, the appliance becomes a single kubernetes master node. Kubernetes service can be configured with :ref:`contextualization <k8s_context_param>` parameters. The main reason behind the contextualization parameters is to be able to add other Kubernetes appliances as worker nodes into one cluster with ease. Having this in mind, the appliance supports `OpenNebula's OneFlow service <http://docs.opennebula.org/5.6/advanced_components/application_flow_and_auto-scaling/appflow_use_cli.html>`_ feature.

.. include:: shared/general.txt

Quick Start
===========

.. include:: shared/import.txt
.. include:: shared/update.txt
.. include:: shared/run.txt

The initial configuration can be customized with :ref:`contextualization <k8s_context_param>` parameters:

|image-context-vars|

.. note::

    The first input (master node address) is very important and it's (exact) value may differ for the master node and the worker node in the same cluster - master node's value can be a network from which an ip will be derived (for example), but worker node's must be an actual ip address (in every case). More about this in :ref:`contextualization <k8s_context_param>`. The following two inputs (token and hash) are relevant for worker nodes only and they need to be set with values found on the master node in :ref:`/etc/one-appliance/config <one_service_logs>`. Below is an :ref:`example <report_file_example>` of a such file. These three inputs are mandatory for the worker node to join the cluster - **all of them must be set** - otherwise a new single master node will be created.

After you are done, click on the button **Instantiate**. A virtual machine with the running service should be ready in a few minutes.

.. include:: shared/report.txt

.. _report_file_example:

.. code::

       ___   _ __    ___
      / _ \ | '_ \  / _ \   OpenNebula Service Appliance
     | (_) || | | ||  __/
      \___/ |_| |_| \___|

    All set and ready to serve 8)

    [root@onekube-ip-192-168-122-10 ~]# cat /etc/one-appliance/config
    [Kubernetes]
    k8s_connection = https://192.168.122.10:6443
    k8s_master = 192.168.122.10
    k8s_join_command = kubeadm join 192.168.122.10:6443 --token meis0p.gxa5e4kvk8pv1xx6 --discovery-token-ca-cert-hash sha256:3b5441641c37af01c9847641c4024f1312755fb4dde1f66a388b9b4eb6ebe1a6
    k8s_token = meis0p.gxa5e4kvk8pv1xx6
    k8s_hash = 3b5441641c37af01c9847641c4024f1312755fb4dde1f66a388b9b4eb6ebe1a6
    k8s_ui_proxy_url = http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
    k8s_ui_login_token = eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJvc3AtdG9rZW4taHZjam0iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoib3NwIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMzdjOTc4NjctNDVhOS0xMWU5LWJlMTEtMDIwMGMwYTg3YTBhIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOm9zcCJ9.Z-zh1WdPrOGctKnjDs79R9DphFrN-5QGsa4NXvRH9112H5iZNjNZP_HE_MakSVnGn6lF1FEmUJppFmqEfsonz5FyP-fDvBzBnb3Xvo-Em12bYXfDfdcWQ90bvLpYIiYY8NhjJFXmAJmkZPmtiv1Eto_sK9ZlXNSTuBRsMAeF0Q7PeymSLKPHcwGCEGzPJQRZCxsBwZ7oKI4_LdcNopR6btULO0B9DesyAZaMPUbMs75KySOxloryockINFZoYYHmckB5fuiBQI_8Pni3mSwrEPU2lwnUrajRQDJuSSbNtbL1fSrMnj2suB20xLF9382yuJJ-2S9VjqrGiIAvDJ7F8g

.. _k8s_context_param:

Contextualization
=================

Contextualization parameters provided in the Virtual Machine template controls the initial VM configuration. Except for the `common set <http://docs.opennebula.org/stable/operation/references/template.html#context-section>`_ of parameters supported by every appliance on the OpenNebula Marketplace, there are few specific to the particular service appliance. The parameters should be provided in the ``CONTEXT`` section of the Virtual Machine template, read the OpenNebula `Operation Guide <http://docs.opennebula.org/stable/operation/vm_setup/kvm.html#set-up-the-virtual-machine-template>`__ for more details.

===================================== ========= =================== ========= ============= ===========
Parameter                             Mandatory Default             Stage     Applicable to  Description
===================================== ========= =================== ========= ============= ===========
``ONEAPP_K8S_ADDRESS``                          routable IP         configure all           Kubernetes master node address or network (in CIDR format)
``ONEAPP_K8S_TOKEN``                                                configure worker        Kubernetes token - to join worker node to the cluster
``ONEAPP_K8S_HASH``                                                 configure worker        Kubernetes hash - to join worker node to the cluster
``ONEAPP_K8S_NODENAME``                         ``onekube-ip-...``  configure master        Kubernetes master node name
``ONEAPP_K8S_PORT``                             ``6443``            configure master        Kubernetes API port on which nodes communicate
``ONEAPP_K8S_PODS_NETWORK``                     ``10.244.0.0/16``   configure master        Kubernetes pods network - pods will have ip from this range
``ONEAPP_K8S_ADMIN_USERNAME``                   ``admin-user``      configure master        UI dashboard admin account - K8S secret's token is prefixed with this name
===================================== ========= =================== ========= ============= ===========

If no contextualization parameter is provisioned then the appliance will create a :ref:`single node kubernetes cluster <k8s_master_node>`. By default it will listen on port ``6443`` of an ip address assigned to a default interface. You can still add more nodes to this cluster later by providing contextualization parameters required by a :ref:`worker node <k8s_worker_node>`.

.. _k8s_master_node:

Single/Master node
------------------

In a simple scenario you don't need to setup anything beforehand. The appliance will bootstrap a fully functional single node kubernetes cluster which can be extended by other nodes any time later.

For other cases when the default behaviour is not sufficient, you can configure the appliance with a few parameters. The most important is the ``ONEAPP_K8S_ADDRESS``.

.. important::

   The following description of the ``ONEAPP_K8S_ADDRESS`` applies to a master node only! In the case of a worker node the value must be strictly the advertised ip address of the master node!

Master node's ``ONEAPP_K8S_ADDRESS`` parameter can contain:

   * an ip address: ``192.168.122.10``
   * a network address: ``192.168.122.0`` (it will pick an ip on an interface which has route for this network)
   * a subnet: ``192.168.122.0/24`` (as above)
   * or an ip address with a subnet prefix: ``192.168.122.10/24``

Use any format which makes sense for you or your scenario. The purpose of this option is to pin kubernetes communication to a designated interface and a network. So it will not be exposed on the default interface.

``ONEAPP_K8S_NODENAME`` is an option solely for a convenience (to easily distinct multiple clusters by a mere look on the name of the master node). It will set a name for the master node in the kubernetes cluster and can be seen when you run:

.. prompt:: text $ auto

    # kubectl get nodes

If you don't set this parameter then the master node (and worker nodes in either of the cases) will have assigned it's hostname as a node name.

``ONEAPP_K8S_PORT`` provides a facility to change the default kubernetes API port (``6443``) to something else.

``ONEAPP_K8S_PODS_NETWORK`` defines a range of private addresses on which pods will be deployed. By default it is ``10.244.0.0/16``. Kubernetes service appliance uses the CNI networking plugin `Canal (Calico+Flannel) <https://docs.projectcalico.org/v3.6/getting-started/kubernetes/installation/flannel>`_ which combines two projects:

* `Calico <https://github.com/projectcalico>`_
* `Flannel <https://github.com/coreos/flannel>`_

.. important::

   Please, **DO NOT** change the default value of ``ONEAPP_K8S_PODS_NETWORK`` if you are not sure what you are doing! You can break the internal kubernetes networking!

``ONEAPP_K8S_ADMIN_USERNAME`` defaults to ``admin-user``. It is the service account name under which a kubernetes token is created to enable login into :ref:`UI dashboard <k8s_dashboard>`. The token is stored in :ref:`/etc/one-appliance/config <one_service_logs>` as ``k8s_ui_login_token`` (you can see one in the :ref:`example <report_file_example>` above).

The only reason to change the default is to easily find and manage the said token with a command:

.. prompt:: text $ auto

    # kubectl -n kube-system get secret | grep ${ONEAPP_K8S_ADMIN_USERNAME}-token

.. important::

   Be warn that the created kubernetes cluster token (``k8s_token`` in :ref:`/etc/one-appliance/config <one_service_logs>`) **never** expires - so handle it carefully. This can be arguably a bad practice - if you wish to administer this by yourself and create a short-lived tokens then delete the current one and create a new token. You can find more info in the `kubeadm documentation <https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#join-nodes>`_.

.. _k8s_worker_node:

Worker node
-----------

In order to join another Kubernetes service appliance to the already running kubernetes cluster, you must provide values for these parameters:

* ``ONEAPP_K8S_ADDRESS`` - actual master node's ip address and **not** a network address or a subnet!
* ``ONEAPP_K8S_TOKEN``
* ``ONEAPP_K8S_HASH``

.. important::

   ``ONEAPP_K8S_ADDRESS`` must be (in the case of a worker node) an explicit ip address of the master node on which the kubernetes advertises it's API port!

.. note::

    All these three parameters must be defined together to take an effect (with correct values). Otherwise a new single master node is created.

You can find all values for these parameters on the master node in the :ref:`/etc/one-appliance/config <one_service_logs>` (actual values will differ from our :ref:`example <report_file_example>`):

* ``k8s_master``
* ``k8s_token``
* ``k8s_hash``

The actual usage of these parameters can be seen in this example:

|image-worker-values|

When you check the status of the nodes in the cluster you should see one master node and all worker nodes which you joined (one in our case)::

   [root@onekube-ip-192-168-122-10 ~]# kubectl get nodes
   NAME                                    STATUS   ROLES    AGE     VERSION
   onekube-ip-192-168-122-10.localdomain   Ready    master   10h     v1.13.4
   onekube-ip-192-168-122-11.localdomain   Ready    <none>   7h57m   v1.13.4

.. _k8s_oneflow:

OneFlow service template
------------------------

We can leverage the OpenNebula's feature of a `OneFlow service <http://docs.opennebula.org/5.6/advanced_components/application_flow_and_auto-scaling/appflow_use_cli.html>`_ and deploy a multi-node kubernetes cluster with one click.

Create a new service template
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Open your OpenNebula and log in
- Go to **Templates → Services** tab
- Click on the green **+** button at the top
- Fill the name of the new service template
- Fill the role name as ``master`` (**!!!**)
- Pick the ``Service Kubernetes - KVM`` (VM template)

.. important::

   The role name for the master node **MUST** be **master** - otherwise the OneFlow deployment will not work!

The current status should resemble this picture:

|image-oneflow-part1|

As the next step we setup a worker role:

- Click on the blue button **+** next to **Roles**
- Fill the role name as ``worker``
- Optionally set the **Number of VMs** to your liking (we will set it to two)
- Pick the ``Service Kubernetes - KVM`` (VM template)

The next status should resemble this picture:

|image-oneflow-part2|

Still on the **worker** role we must tell the OneFlow service that for this role the parent is **master** role:

|image-oneflow-part3|

And as a last step we cannot forget to check this button:

|image-oneflow-part4|

.. important::

   Both the parent relationship and the checkbox **Wait for VMs to report that they are READY via OneGate to consider them running** must be set properly and/or enabled - otherwise all nodes defined in the service will be spawned together and all of them become a kubernetes master node. That means that instead of a one cluster with one master node and two worker nodes, we would get three single node clusters...

Finally click on the green **Create** button at the top and we are done.

Instantiate the service template
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We will instantiate the service template like the following picture demonstrates - notice that we are telling the template to use a specific network for kubernetes:

|image-oneflow-part5|

.. important::

   **DO NOT** set any input fields for the **worker** role - those will be provisioned automatically via OneFlow feature of the Kubernetes service appliance.

After a few minutes you should see all nodes deployed and ready:

|image-oneflow-part6|

Check the cluster status
~~~~~~~~~~~~~~~~~~~~~~~~

Log into the master node:

.. prompt:: text $ auto

    % ssh root@192.168.122.10 # change to your master node address

Now we can check that our cluster truly consists of the three nodes (or a count you defined)::

   [root@onekube-ip-192-168-122-10 ~]# kubectl get nodes
   NAME                                    STATUS   ROLES    AGE     VERSION
   onekube-ip-192-168-122-10.localdomain   Ready    master   15m     v1.13.4
   onekube-ip-192-168-122-11.localdomain   Ready    <none>   9m53s   v1.13.4
   onekube-ip-192-168-122-12.localdomain   Ready    <none>   9m34s   v1.13.4

We can also check that kubernetes is using the second network interface as we requested (internal ip - ``192.168.233.0/24``)::

   [root@onekube-ip-192-168-122-10 ~]# kubectl get nodes -o wide
   NAME                                    STATUS   ROLES    AGE     VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION              CONTAINER-RUNTIME
   onekube-ip-192-168-122-10.localdomain   Ready    master   16m     v1.13.4   192.168.233.100   <none>        CentOS Linux 7 (Core)   3.10.0-957.5.1.el7.x86_64   docker://18.6.3
   onekube-ip-192-168-122-11.localdomain   Ready    <none>   10m     v1.13.4   192.168.233.101   <none>        CentOS Linux 7 (Core)   3.10.0-957.5.1.el7.x86_64   docker://18.6.3
   onekube-ip-192-168-122-12.localdomain   Ready    <none>   9m49s   v1.13.4   192.168.233.102   <none>        CentOS Linux 7 (Core)   3.10.0-957.5.1.el7.x86_64   docker://18.6.3

.. _k8s_tutorial:

Kubernetes tutorial
-------------------

In this section we show you how you can work with the kubernetes appliance.

After you deploy a kubernetes **master** node - log into it via ssh:

.. prompt:: text $ auto

    % ssh root@192.168.122.10 # change to your master node address

.. note::

    The following instructions works only for a **master** node and they will fail on a worker node!

In the VM (the kubernetes master node) check the status of the cluster:

.. prompt:: text $ auto

    # kubectl get nodes

You should get something similar to this::

   NAME                                    STATUS   ROLES    AGE     VERSION
   onekube-ip-192-168-122-10.localdomain   Ready    master   4m38s   v1.13.4

If the previous command ran successfully then you can logout from the master node and we can continue by configuring the ``kubectl`` CLI utility. So you can apply kubernetes manifests (yaml formatted files with kubernetes configuration) and access :ref:`UI dashboard <k8s_dashboard>` comfortably from your workstation.

.. _k8s_tutorial_kubectl:

kubectl
~~~~~~~

Firstly install the binary via a preferred way for your system: `Install and setup kubectl <https://kubernetes.io/docs/tasks/tools/install-kubectl/>`_.

You should be able to run this command now:

.. prompt:: text $ auto

    % kubectl --help

We will now prepare our local kubectl config file (notice the master node's ip address!):

.. prompt:: text $ auto

    % mkdir -p ~/.kube
    % scp root@192.168.122.10:/etc/kubernetes/admin.conf ~/.kube/config

.. note::

    You can do this step above for any machine from which you want to run ``kubectl`` commands. The point is to distribute the ``admin.conf`` from the master node.

This time you should be able to check kubernetes cluster status without logging into the VM:

.. prompt:: text $ auto

    % kubectl get nodes

And get the same result as before.

Alternatively, you can also just copy the ``admin.conf`` and run ``kubectl`` like this (all methods are equal):

.. prompt:: text $ auto

    % KUBECONFIG=${HOME}/admin.conf kubectl get nodes
    % kubectl --kubeconfig=${HOME}/admin.conf get nodes


Example application
~~~~~~~~~~~~~~~~~~~

We will loosely follow the `official guide <https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/>`_ as a demonstration how to deploy an application to our newly created kubernetes cluster.

Start with creating a manifest file (change the ``externalIPs`` to your master node ip **!**):

.. prompt:: text $ auto

    % cat > ~/onekube-test-manifest.yaml <<EOF
    apiVersion: v1
    kind: Service
    metadata:
      name: kubetest-service
    spec:
      selector:
        app: nginx
      ports:
      - port: 80
        targetPort: http
      externalIPs:
      - 192.168.122.10
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: nginx-deployment
    spec:
      selector:
        matchLabels:
          app: nginx
      replicas: 2 # tells deployment to run 2 pods matching the template
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.15.9-alpine
            command:
            - /bin/sh
            - "-c"
            - |
              cat > /usr/share/nginx/html/index.html <<EOF
              <!DOCTYPE html>
              <html>
              <head>
              <title>Welcome to OpenNebula's Kubernetes appliance!</title>
              <style>
                  body {
                      width: 35em;
                      margin: 0 auto;
                      font-family: Tahoma, Verdana, Arial, sans-serif;
                  }
              </style>
              </head>
              <body>
              <h1>Welcome to OpenNebula's Kubernetes appliance!</h1>
              <p>If you see this page then your kubernetes cluster is up and running.</p>
              </body>
              </html>
              EOF
              exec nginx -g 'daemon off;'
            ports:
            - name: http
              containerPort: 80
    EOF

Then just simply apply this manifest and kubernetes will take care of the rest (it may take a while to download docker images):

.. prompt:: text $ auto

    % kubectl apply -f ~/onekube-test-manifest.yaml

You can check that your application is ready if you see an output like this::

   % kubectl get pods
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-64cf7456d5-9s86k   1/1     Running   0          2m11s
   nginx-deployment-64cf7456d5-zm2wc   1/1     Running   0          2m11s

Test it from the command line:

.. prompt:: text $ auto

    % curl 192.168.122.10 # master node address

Which should result in::

   <!DOCTYPE html>
   <html>
   <head>
   <title>Welcome to OpenNebula's Kubernetes appliance!</title>
   <style>
       body {
           width: 35em;
           margin: 0 auto;
           font-family: Tahoma, Verdana, Arial, sans-serif;
       }
   </style>
   </head>
   <body>
   <h1>Welcome to OpenNebula's Kubernetes appliance!</h1>
   <p>If you see this page then your kubernetes cluster is up and running.</p>
   </body>
   </html>

Or fire up your internet browser and go to ``http://192.168.122.10`` (adjust to your master node!):

|image-kubetest|


.. _k8s_dashboard:

UI dashboard
------------

.. note::

    UI dashboard is not accessible by default and must be enabled by the following procedure described in this section.

.. note::

    You must firstly setup :ref:`kubectl <k8s_tutorial_kubectl>` before you can continue.

On your workstation run and let running this command:

.. prompt:: text $ auto

    % kubectl proxy

The command will not exit (you must hit ``CTRL-C``) and it should print::

   Starting to serve on 127.0.0.1:8001

Now you can reach the UI dashboard in your browser:

   `http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/ <http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/>`_

The dashboard's login screen will greet you there. Select the ``Token`` option and copy-paste the value of ``k8s_ui_login_token`` from :ref:`/etc/one-appliance/config <one_service_logs>` to this form. You should see something like this:

|image-ui-login|

The UI dashboard is very handy to explore your kubernetes cluster and it also enables you another way to deploy manifests by clicking on the **CREATE** button in the top-right corner:

|image-ui-create|

More info about UI dashboard can be found in the `web/UI documentation <https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/>`_.

.. |image-download| image:: /images/kubernetes/kubernetes-download.png
.. |image-worker-values| image:: /images/kubernetes/kubernetes-worker-values.png
.. |image-context-vars| image:: /images/kubernetes/kubernetes-context-vars.png
.. |image-ui-login| image:: /images/kubernetes/kubernetes-ui-login.png
.. |image-ui-create| image:: /images/kubernetes/kubernetes-ui-create.png
.. |image-kubetest| image:: /images/kubernetes/kubernetes-kubetest.png
.. |image-oneflow-part1| image:: /images/kubernetes/kubernetes-oneflow-part1.png
.. |image-oneflow-part2| image:: /images/kubernetes/kubernetes-oneflow-part2.png
.. |image-oneflow-part3| image:: /images/kubernetes/kubernetes-oneflow-part3.png
.. |image-oneflow-part4| image:: /images/kubernetes/kubernetes-oneflow-part4.png
.. |image-oneflow-part5| image:: /images/kubernetes/kubernetes-oneflow-part5.png
.. |image-oneflow-part6| image:: /images/kubernetes/kubernetes-oneflow-part6.png
.. |image-ssh-context| image:: /images/appliance-ssh-context.png
.. |image-custom-vars-password| image:: /images/appliance-custom-vars-password.png
