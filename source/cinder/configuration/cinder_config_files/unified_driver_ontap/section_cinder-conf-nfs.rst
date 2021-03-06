.. _cdot-nfs:

NetApp Unified Driver for Clustered Data ONTAP with NFS
-------------------------------------------------------

The NetApp unified driver for clustered Data ONTAP with NFS is a driver
interface from OpenStack block storage to a Data ONTAP cluster system to
accomplish provisioning and management of OpenStack volumes on NFS
exports provided by the Data ONTAP cluster system. The NetApp unified
driver for the Data ONTAP cluster does not require any additional
management software to achieve the desired functionality. It uses NetApp
APIs to interact with the Data ONTAP cluster.

To set up the NetApp clustered Data ONTAP NFS driver for Cinder, the
following stanza should be added to the Cinder configuration file
(``cinder.conf``)::

    [myNfsBackend]
    volume_backend_name=myNfsBackend
    volume_driver=cinder.volume.drivers.netapp.common.NetAppDriver
    netapp_server_hostname=hostname
    netapp_server_port=80
    netapp_storage_protocol=nfs
    netapp_storage_family=ontap_cluster
    netapp_login=admin_username
    netapp_password=admin_password
    netapp_vserver=svm_name
    nfs_shares_config=path_to_nfs_exports_file
    max_oversubscription_ratio=1.0
    reserved_percentage=5
    nfs_mount_options=lookupcache=pos

-  Be sure that the value of the ``enabled_backends`` option in the
   ``[DEFAULT]`` stanza includes the name of the stanza you chose for
   the backend.

.. caution::

   Please note that exported qtrees are not supported by Cinder.

.. note::

   The file referenced in the ``nfs_shares_config`` configuration
   option should contain the NFS exports in the ``ip:/share`` format,
   for example::

      10.63.165.215:/nfs/test
      10.63.165.215:/nfs2/test2

   where ``ip`` corresponds to the IP address assigned to a Data LIF,
   and ``share`` refers to a junction path for a FlexVol volume within
   an SVM. Make sure that volumes corresponding to exports have
   read/write permissions set on the Data ONTAP controllers. Do *not*
   put mount options in the ``nfs_shares_config`` file; use
   ``nfs_mount_options`` instead. For more information on that and
   other parameters available to affect the behavior of NetApp's NFS
   driver, please refer to
   http://docs.openstack.org/trunk/config-reference/content/nfs-driver-options.html.

Table 4.14, “Configuration options for clustered Data ONTAP with NFS” below lists
the configuration options available for the unified driver for a clustered
Data ONTAP deployment that uses the NFS storage protocol.

.. _table-4.14:

+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Option                             | Type       | Default Value                | Description                                                                                                                                                                                                                                                                                                                                                                                                             |
+====================================+============+==============================+=========================================================================================================================================================================================================================================================================================================================================================================================================================+
| ``netapp_server_hostname``         | Required   |                              | The hostname or IP address for the storage system or proxy server. *The value of this option should be the IP address of either the cluster management LIF or the SVM management LIF.*                                                                                                                                                                                                                                  |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_server_port``             | Optional   |                              | The TCP port to use for communication with the storage system or proxy server. If not specified, Data ONTAP drivers will use 80 for HTTP and 443 for HTTPS; E-Series will use 8080 for HTTP and 8443 for HTTPS.                                                                                                                                                                                                         |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_login``                   | Required   |                              | Administrative user account name used to access the storage system or proxy server.                                                                                                                                                                                                                                                                                                                                     |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_password``                | Required   |                              | Password for the administrative user account specified in the ``netapp_login`` option.                                                                                                                                                                                                                                                                                                                                  |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_storage_protocol``        | Required   |                              | The storage protocol to be used. Valid options are ``nfs``, ``iscsi``, or ``fc``.                                                                                                                                                                                                                                                                                                                                       |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_transport_type``          | Required   | ``http``                     | Transport protocol for communicating with the storage system or proxy server. Valid options include ``http`` and ``https``.                                                                                                                                                                                                                                                                                             |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_copyoffload_tool_path``   | Optional   |                              | This option specifies the path of the NetApp copy offload tool binary. Ensure that the binary has execute permissions set which allow the effective user of the ``cinder-volume`` process to execute the file.                                                                                                                                                                                                          |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_vserver``                 | Required   |                              | This option specifies the storage virtual machine (previously called a Vserver) name on the storage cluster on which provisioning of block storage volumes should occur.                                                                                                                                                                                                                                                |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``netapp_storage_family``          | Optional   | ``ontap_cluster``            | The storage family type used on the storage system; valid values are ``ontap_7mode`` for Data ONTAP operating in 7-Mode, ``ontap_cluster`` for clustered Data ONTAP, or ``eseries`` for E-Series.                                                                                                                                                                                                                       |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``nfs_shares_config``              | Required   | ``/etc/cinder/nfs_shares``   | The file referenced by this configuration option should contain a list of NFS shares, each on their own line, to which the driver should attempt to provision new Cinder volumes into.                                                                                                                                                                                                                                  |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``nfs_mount_options``              | Optional   | None                         | Mount options passed to the nfs client. See section of the nfs man page for details.                                                                                                                                                                                                                                                                                                                                    |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``nas_secure_file_permissions``    | Optional   | ``auto``                     | If 'false', backing files for cinder volumes are readable by owner, group, and world; if 'true', only by owner and group. If 'auto' and there are existing Cinder volumes, value will be set to 'false' (for backwards compatibility); if 'auto' and there are no existing Cinder volumes, the value will be set to 'true'.                                                                                             |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``nas_secure_file_operations``     | Optional   | ``auto``                     | If 'false', operations on the backing files run as root; if 'true', operations on the backing files for cinder volumes run unprivileged, as the cinder user, and are allowed to succeed even when root is squashed. If 'auto' and there are existing Cinder volumes, value will be set to 'false' (for backwards compatibility); if 'auto' and there are no existing Cinder volumes, the value will be set to 'true'.   |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``thres_avl_size_perc_start``      | Optional   | ``20``                       | If the percentage of available space for an NFS share has dropped below the value specified by this option, the NFS image cache will be cleaned.                                                                                                                                                                                                                                                                        |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``thres_avl_size_perc_stop``       | Optional   | ``60``                       | When the percentage of available space on an NFS share has reached the percentage specified by this option, the driver will stop clearing files from the NFS image cache that have not been accessed in the last M minutes, where M is the value of the ``expiry_thres_minutes`` configuration option.                                                                                                                  |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``expiry_thres_minutes``           | Optional   | ``720``                      | This option specifies the threshold for last access time for images in the NFS image cache. When a cache cleaning cycle begins, images in the cache that have not been accessed in the last M minutes, where M is the value of this parameter, will be deleted from the cache to create free space on the NFS share.                                                                                                    |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``reserved_percentage``            | Optional   | ``0``                        | This option represents the amount of total capacity of a storage pool that will be reserved and cannot be utilized for provisioning Cinder volumes.                                                                                                                                                                                                                                                                     |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``max_oversubscription_ratio``     | Optional   | ``20.0``                     | This option is defined as a float, and specifies the amount of over-provisioning to allow when thin provisioning is being used in the storage pool. A value of 1.0 will mean that the provisioned capacity will not be able to exceed the total capacity, while larger values will result in increased levels of allowed over-provisioning.                                                                             |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``filter_function``                | Optional   | (see description)            | This option may be used to override the default filter function, which prevents Cinder from placing new volumes on storage controllers that may become overutilized. The default value is "capabilities.utilization < 70".                                                                                                                                                                                              |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``goodness_function``              | Optional   | (see description)            | This option may be used to override the default goodness function, which allows Cinder to place new volumes on lesser-utilized storage controllers. The default value is "100 - capabilities.utilization".                                                                                                                                                                                                              |
+------------------------------------+------------+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Table 4.14. Configuration options for clustered Data ONTAP with NFS

.. caution::

   If you specify an account in the ``netapp_login`` option that only
   has SVM administration privileges (rather than cluster
   administration privileges), some advanced features of the NetApp
   unified driver will not work and you may see warnings in the Cinder
   logs. See the section called ":ref:`account-permissions`"
   for more details on the required access level permissions for an SVM
   admin account.

.. include:: ../../../common/nfs-cache-warning.rst
