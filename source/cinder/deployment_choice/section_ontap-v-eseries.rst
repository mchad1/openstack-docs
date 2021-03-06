Deployment Choices: ONTAP vs. E-Series
======================================

**ONTAP**


If rich data management, deep data protection, and storage efficiency
are desired and should be availed directly by the storage, the NetApp
FAS product line is a natural fit for use within Cinder deployments.
Massive scalability, nondisruptive operations, proven storage
efficiencies, and a unified architecture (NAS and SAN) are key features
offered by the Data ONTAP storage operating system. These capabilities
are frequently leveraged in existing virtualization deployments and thus
align naturally to OpenStack use cases.

**E-Series**


For cloud environments where higher performance is critical, or where
higher-value data management features are not needed or are implemented
within an application, the NetApp E-Series product line can provide a
cost-effective underpinning for a Cinder deployment. NetApp E-Series
storage offers a feature called Dynamic Disk Pools, which simplifies
data protection by removing the complexity of configuring RAID groups
and allocating hot spares. Utilization is improved by dynamically
spreading data, parity, and spare capacity across all drives in a pool,
reducing performance bottlenecks due to hot-spots. Additionally, should
a drive failure occur, DDP enables the pool to return to an optimal
state significantly faster than RAID6, while reducing the performance
impact during the reconstruction of a failed drive.
