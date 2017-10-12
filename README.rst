======
GIMinG
======

GIMinG is Cloud Image Manager for hybrid cloud solutions.

Images are the baseline of any virtualized workload deployment.
Normally images are provided to cloud platform to provide Operating
Environment on top of which the applications are deployed. Each
cloud platform deals with images bit differently and it's not
obvious how to move workloads around to take the benefit of the
offered platforms. Being that small private cloud running
OpenStack or public cloud running OpenStack or some other cloud
technology, the common nominator is that all of them rely on images
to provide their users that Operating Environment.

GIMinG is aiming to fill the gap of tooling to make that as easy as
possible for the user.


Different Cloud Platforms
=========================

Interfacing with myltiple cloud platforms is key with lots of the
hybrid cloud scenarios. Thus GIMinG is aiming to support at least
the following in the foreseeable future.

OpenStack
---------

The MVP of GIMinG is targeted to work with multiple OpenStack clouds.
GIMinG does not try to replace Glance, but rather providing interface
to interact with multiple Glance instaces providing ease for Image
Lifecycle Management.

As Glance is single zone service, there is lots of use cases syncing
image-data between zones or even between multiple clouds. Focus on
Glance project is not to expand into these usecases. Glance community
has been empthatizing multiple times that the image lifecycle
management should be done by some external tooling.

GIMinG utilizes multiple existing projects from OpenStack and might
apply for OpenStack project status in the future. GIMinG policies
and licenses have been aligned with equivalent OpenStack ones from
the beginning.


Google Cloud Platform
---------------------

Google Cloud Platform provides flexible tooling to interface with
compute images and it will be high priority to support in the future
for public cloud usecase.


Amazon Web Services
-------------------

Amazon Web Services is hugely popular public cloud provider and
also provides mechanisms to import and export compute disk images.
Supporting AWS as public cloud provider on GIMinG workflows will
be high priority for the future development.


GIMinG Architecture Design
==========================

The design of GIMinG is to provide service with REsTFul API for
multitenant operations. Also CLI client is provided by the project
to interface with the API.

As per the design GIMinG is multitenant platform. This multitenancy
is achieved by utilizing authentication from OpenStack Keystone.This
enables integration with the existing OpenStack cloud and providing
the service as part of the infrastructure in that cloud.

GIMinG will utilize database to keep track of the images. The image
metadata will not be stored in the GIMinG database but rather the ID
or equivalent information how to access the data from the different
clouds.

When new multi-target image is created in GIMinG either by user or
synced from image in one of the tracked clouds it will aim to unify
the identifiers across the clouds.

Common use for GIMinG would be private cloud owner deploying the
service as part of provided infrastructure to enable the user easily
migrating their workloads between private and public clouds.

Another use would be for large OpenStack deployments using GIMinG for
managing and syncing images between multiple zones as part of the
control plane infrastructure.


Authentication
--------------

GIMinG will always need user interaction to sync the infromation from
the source/target clouds. User can either provide the credentials to
the different source/target clouds via the REsTFul API or the
credentials can be saved to OpenStack Barbican service. In the later
case the user needs to provide only the credentials to the environment
so that GIMinG can fetch the credentials to the clouds it needs.

GIMinG will never store the user credentials itself and thus it cannot
operate long term without the user interactions. For long running jobs
it may utilize the keystone Trust mechanism so that it can finish the
job it started even if the original user token has expired meantime.


Image Creation
--------------

GIMinG can either create image from user provided data to all or
selected clouds or user can select image not tracked by GIMinG to be
created as record and synced between said clouds. This enables the
flexibility for the user to utilize the existing image instead of
downloading it and creating fully new image to all of the targets.

When syncing existing image the source can be only cloud that allows
the image to be exported and the targets can be only clouds that allow
users to create images in them.

User can also do the linking between entries manually to be tracked
in the used clouds. This will allow user to specify the identifiers
of the same image data between clouds as long as the user has access
to those images.

If the Image is created via REsTFul API user needs to upload the data
only once and it will be cached within the service until all
requested images has been created. After that the data will be removed
and GIMinG will not provide possibility to download the image data
through it's API.


Image Monitoring
----------------

GIMinG will provide information such as Image metadata assosiated in
the cloud; availability between the clouds; etc. This will help the
user to determine at which clouds it will be possible to run the
specific workload. This information will be pulled from the different
clouds for each sync to maintain as close to realtime picture as
possible.

The synced data for the session can be cached locally by the client,
to limit the amount of the requests sent to the different clouds. Still
allowing searches and filtering to the data for different
representations.


Client
------

CLI and python clients will be provided to interact with the service.

Client will facilitate interfacing with the REsTFul API and
authentication with Keystone. The new API capabilities will be enabled
simultaneously in the client and the provided client will always be
reference implementation how to interact with the API.


Documentation
=============

For more detailed documentation of the service, client and their
capabilities can be found from the 'docs/' directory of this repo.
