Cloud Infrastructure 
====================

:term:`RGVFlood.com` cloud deployment relies on conversion of its component services to be converted for delivery via a :term:`Microservices Architecture`. Several options are available for deploying the microservices version of :term:`RGVFlood.com`, including an on-premise hardware cluster, Google Cloud Platform, Amazon Web Services and Microsoft's Azure.

Google Cloud Platform 
---------------------

Google Cloud Platfrom (:term:`GCP`) was selected as the initial platform for :term:`RGVFlood.com`, based on Cost of Service and ease of integration with existing :term:`RATES` operations.

Google App Engine 
-----------------

Google App Engine (:term:`GAE`) allows users to deploy containerized apps on :term:`GCP`, whithout have to worry about underlying server management. Other than providing a cloud testing environment for individual microservices, :term:`GAE` currently has limited utility for use in the :term:`RGVFlood.com` ecosystem.

Google Compute Engine
---------------------

:term:`Google` Compute Engine (:term:`GCE`) provides access to virtual machines (:term:`VM`), and serves as the infrastructure basis for the :term:`GAE` environment. A :term:`GCE` :term:`VM` was used to support the transition from a self-contained docker-stack to deploy a fully cloud-leverage :term:`RGVFlood.com` deployment.

Google Kubernetes Engine
------------------------

:term:`Google` Kubernetes Engine (:term:`GKE`) provides the user access to the container orchestration facilities of :term:`GCP`. Google's Autopilot custer configuration was selected for cluster creation, allowing for ease of horizontal (node/cpu) and vertical (memory) scaling. As a result, cluster size is reported in terms of the number of :term:`vCPU` and memory rather than the traditional node count. Running the following applications to support continuous development:

* geonodegcp-app
* reonode-app
* waterwizard-app
* rgvflood-app

with nominal use, the cluster scaled to 9.75 :term:`vCPU` and 38.2 GB of memory. With one :term:`vCPU` being roughly equivalent to one hardware core, this is similar in capacity to a single standard bare-metal server. With the integration of user-applications (e.g. RTHS Data API and Flood Wizard), along with anticipated end-user access and demand, horizontal scaling needs are expected to quadruple at a minimum.

CloudSQL
--------

Rather than rely on containerized database services, the decision was made to switch to Google CloudSQL managed database services. Similar services are available though :term:`AWS` and :term:`Azure`. Unlike a single-stack :term:`Docker` deployment, switching to a :term:`K8s` with potentialy multiple replicas needing to access the database services, reliance on managed database services eliminates the need to construct and manage a separate workload specically for database services.

The first step in transtioning to :term:`k8s` involved deploying the :code:`docker-compose.yml` stack on a :term:`GCE` :term:`VM`. The database service was then replaced with a CloudQSL-Proxy service, allowing the containers to access the databases managed by CloudDQL and permitting the number of replicas to be scale with no collisions or impacts in performance.

Filestore
---------

Persistent file storage is handled differently between standard :term:`Docker` desktop deployments and scalable :term:`K8s` clusters. Implelementing persistent storage between reboots and between containers for the :term:`K8s` deplyoment involved changing from volume mounts to and :term:`NFS` share. This :term:`NFS` share is also mounted by as :term:`GCE` :term:`VM` used during the development process for debugging. It is anticipated that the volume of filestorage needed will eventually be in excess of 1TB, more once real-time forecast data is produced.
