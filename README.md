# 网络存储开发相关总结
```
graph TD
    subgraph "Initiator Side (K8s Worker Node)"
        Pod[Pod / Application] -->|System Call| VFS
        VFS -->|Block Layer| Bio
        Bio -->|Multipath| DM[Device Mapper / Multipath]
        DM -->|Driver| TCP_Driver[NVMe-TCP / iSCSI Driver]
    end

    TCP_Driver -->|Network Fabric (TCP/RDMA)| NIC[Network Interface Card]

    subgraph "Target Side (Storage Cluster)"
        NIC -->|Polling| SPDK_App[SPDK Target Application]
        
        subgraph "User Space (High Performance)"
            SPDK_App -->|Protocol Parsing| Proto[iSCSI / NVMe-oF Layer]
            Proto -->|Bdev Layer| Bdev[Bdev Abstraction]
            Bdev -->|Zero-Copy| Driver[NVMe User Driver]
        end
        
        subgraph "Kernel Space (Legacy/Hybrid)"
            Kernel_LIO[LIO / TGT] -->|Context Switch| VFS_K
            VFS_K -->|File System| OSD[Ceph OSD / Backend]
        end
    end

    Driver -->|PCIe| SSD[NVMe SSD]
```
