.. _7mode-fc:

NetApp Unified Driver for Data ONTAP operating in 7-Mode with Fibre Channel
---------------------------------------------------------------------------

The NetApp unified driver for Data ONTAP operating in 7-Mode with Fibre
Channel is a driver interface from OpenStack Cinder to NetApp Data ONTAP
operating in 7-Mode storage controllers to accomplish provisioning and
management of a storage-area network (SAN) block storage entity; that
is, a NetApp LUN that uses the Fibre Channel protocol.

.. important::

   The NetApp unified driver in Cinder currently provides integration
   for two major generations of the ONTAP operating system: the current
   “clustered” ONTAP and the legacy 7-mode. NetApp’s “full support” for
   7-mode ended in August of 2015 and the current “limited support”
   period will end in February of 2017 [1].

   In accordance with community policy [2], we are initiating the
   deprecation process for the 7-mode components of the Cinder NetApp
   unified driver set to conclude with their removal in the Queens
   release. This will apply to all three protocols currently supported
   in this driver: iSCSI, FC and NFS.

   -  ``What is being deprecated:`` Cinder drivers for NetApp Data
      ONTAP 7-mode NFS, iSCSI, FC

   -  ``Period of deprecation:`` 7-mode drivers will be around in
      stable/ocata and stable/pike and will be removed in the Queens
      release (All milestones of this release)

   -  ``What should users/operators do:`` Follow the recommended
      migration path to upgrade to Clustered Data ONTAP [3] or get in
      touch with your NetApp support representative.

   1. `Transition Fundamentals and
      Guidance <https://transition.netapp.com/>`__

   2. `OpenStack deprecation
      policy <https://governance.openstack.org/tc/reference/tags/assert_follows-standard-deprecation.html>`__

   3. `Clustered Data ONTAP for 7-Mode
      Administrators <https://mysupport.netapp.com/info/web/ECMP1658253.html>`__

.. note::

   Both nodes in a 7-Mode HA pair *must* be independently declared as
   separate Cinder backends with an appropriate cross-reference to one
   another using the ``netapp_partner_backend_name`` option.

To set up the NetApp Data ONTAP operating in 7-Mode Fibre Channel driver
for Cinder, the following stanza should be added to the Cinder
configuration file (``cinder.conf``) for the first node in the HA Pair::

    [myFCBackend]
    volume_backend_name=myFCBackend
    netapp_partner_backend_name=myOtherFCBackend
    volume_driver=cinder.volume.drivers.netapp.common.NetAppDriver
    netapp_server_hostname=hostname
    netapp_server_port=80
    netapp_storage_protocol=fc
    netapp_storage_family=ontap_7mode
    netapp_login=admin_username
    netapp_password=admin_password
    max_oversubscription_ratio=1.0
    reserved_percentage=5

-  Be sure that the value of the ``enabled_backends`` option in the
   ``[DEFAULT]`` stanza includes the name of the stanza you chose for
   the backend.

-  Be sure that the value of the ``netapp_partner_backend_name`` is set
   to the HA Pair's ``volume_backend_name`` value and that the HA Pair
   has this node's ``volume_backend_name`` value in its configuration
   stanza under the ``netapp_partner_backend_name`` option.

-  The value of ``netapp_storage_protocol`` MUST be set to ``fc``.

-  The value of ``netapp_storage_family`` MUST be set to
   ``ontap_7mode``, as the default value for this option is
   ``ontap_cluster``.

To set up the second node in the HA Pair, add the following stanza to
the Cinder configuration file (``cinder.conf``)::

    [myOtherFCBackend]
    volume_backend_name=myOtherFCBackend
    netapp_partner_backend_name=myFCBackend
    volume_driver=cinder.volume.drivers.netapp.common.NetAppDriver
    netapp_server_hostname=hostname
    netapp_server_port=80
    netapp_storage_protocol=fc
    netapp_storage_family=ontap_7mode
    netapp_login=admin_username
    netapp_password=admin_password

-  Be sure that the value of the ``enabled_backends`` option in the
   ``[DEFAULT]`` stanza includes the name of the stanza you chose for
   the backend.

-  Be sure that the value of the ``netapp_partner_backend_name`` is set
   to the HA Pair's ``volume_backend_name`` value and that the HA Pair
   has this node's ``volume_backend_name`` value in its configuration
   stanza under the ``netapp_partner_backend_name`` option.

-  The value of ``netapp_storage_protocol`` MUST be set to ``fc``.

-  The value of ``netapp_storage_family`` MUST be set to
   ``ontap_7mode``, as the default value for this option is
   ``ontap_cluster``.

Table 4.18, “Configuration options for Data ONTAP operating in 7-Mode
with Fibre Channel” below lists the configuration options available
for the unified driver for a clustered Data ONTAP deployment that uses
the Fibre Channel storage protocol.

+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Option                                | Type         | Default Value       | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
+=======================================+==============+=====================+=================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
| ``netapp_server_hostname``            | Required     |                     | The hostname or IP address for the storage system or proxy server. *The value of this option should be the IP address of either the cluster management LIF or the SVM management LIF.*                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_server_port``                | Optional     |                     | The TCP port to use for communication with the storage system or proxy server. If not specified, Data ONTAP drivers will use 80 for HTTP and 443 for HTTPS; E-Series will use 8080 for HTTP and 8443 for HTTPS.                                                                                                                                                                                                                                                                                                                                                                                                                                 |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_login``                      | Required     |                     | Administrative user account name used to access the storage system or proxy server.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_password``                   | Required     |                     | Password for the administrative user account specified in the ``netapp_login`` option.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_storage_protocol``           | Required     |                     | The storage protocol to be used. Valid options are ``nfs``, ``iscsi`` or ``fc``.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_transport_type``             | Required     | ``http``            | Transport protocol for communicating with the storage system or proxy server. Valid options include ``http`` and ``https``.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_size_multiplier``            | Optional     | ``1.2``             | When creating volumes, the quantity to be multiplied to the requested OpenStack volume size to ensure enough space is available on the SVM (aka Vserver). *This value is currently only used when ISCSI has been selected as the storage protocol to be used.*                                                                                                                                                                                                                                                                                                                                                                                  |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_volume_list``                | Deprecated   |                     | This option has been deprecated in preference of ``netapp_pool_name_search_pattern``. Backwards compatibility for this option remains, but this option will be removed in a future release of OpenStack.                                                                                                                                                                                                                                                                                                                                                                                                                                        |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_vfiler``                     | Optional     |                     | The vFiler unit on which provisioning of block storage volumes will be done. This option is only used by the driver when connecting to an instance with a storage family of Data ONTAP operating in 7-Mode. Only use this option when utilizing the MultiStore feature on the NetApp storage system.                                                                                                                                                                                                                                                                                                                                            |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_storage_family``             | Required     | ``ontap_cluster``   | The storage family type used on the storage system; valid values are ``ontap_7mode`` for Data ONTAP operating in 7-Mode, ``ontap_cluster`` for clustered Data ONTAP, or ``eseries`` for E-Series.                                                                                                                                                                                                                                                                                                                                                                                                                                               |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_partner_backend_name``       | Required     |                     | The name of the ``cinder.conf`` stanza for a Data ONTAP operating in 7-Mode HA partner. This option is only used by the driver when connecting to an instance with a ``netapp_storage_family`` value of ``ontap_7mode`` and is required when ``netapp_storage_protocol`` is set to ``fc``.                                                                                                                                                                                                                                                                                                                                                      |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_lun_space_reservation``      | Optional     | ``enabled``         | This option specifies whether space will be reserved when creating Cinder volumes on NetApp backends using the iSCSI or FC storage protocols. If this option is set to ``enabled``, LUNs created during volume creation or volume cloning workflows will always be thick provisioned. If this option is set to ``disabled``, LUNs created during volume creation or volume cloning workflows will always be thin provisioned. Note that this option does not affect the implementation of Cinder snapshots, where the LUN clone that represents the snapshot will always be thin provisioned. Valid options are ``enabled`` and ``disabled``.   |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``reserved_percentage``               | Optional     | ``0``               | This option represents the amount of total capacity of a storage pool that will be reserved and cannot be utilized for provisioning Cinder volumes.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``max_oversubscription_ratio``        | Optional     | ``20.0``            | This option is defined as a float, and specifies the amount of over-provisioning to allow when thin provisioning is being used in the storage pool. A value of 1.0 will mean that the provisioned capacity will not be able to exceed the total capacity, while larger values will result in increased levels of allowed over-provisioning.                                                                                                                                                                                                                                                                                                     |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_pool_name_search_pattern``   | Optional     | ``(.+)``            | This option is only utilized when the Cinder driver is configured to use iSCSI or Fibre Channel. It is used to restrict provisioning to the specified FlexVol volumes. Specify the value of this option as a regular expression which will be applied to the names of FlexVol volumes from the storage backend which represent pools in Cinder. ``^`` (beginning of string) and ``$`` (end of string) are implicitly wrapped around the regular expression specified before filtering.                                                                                                                                                          |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``filter_function``                   | Optional     | (see description)   | This option may be used to override the default filter function, which prevents Cinder from placing new volumes on storage controllers that may become overutilized. The default value is "capabilities.utilization < 70".                                                                                                                                                                                                                                                                                                                                                                                                                      |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``goodness_function``                 | Optional     | (see description)   | This option may be used to override the default goodness function, which allows Cinder to place new volumes on lesser-utilized storage controllers. The default value is "100 - capabilities.utilization".                                                                                                                                                                                                                                                                                                                                                                                                                                      |
+---------------------------------------+--------------+---------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Table 4.18. Configuration options for Data ONTAP operating in 7-Mode with
Fibre Channel

.. important::

    In order for Fibre Channel to be set up correctly, you also need to
    set up Fibre Channel zoning for your backends. See
    the section called ":ref:`fc-switch`" for more details on configuring Fibre
    Channel zoning.
