# Azure Storage Account
- the backbone of many Azure services like Virtual Machines, Functions, Event Grid, Event Hubs (capture), and more.
- An Azure Storage Account is a secure cloud-based container that holds all your Azure storage data objects:
  - Blobs (files, media, data)
  - Queues (messages)
  - Tables (NoSQL data)
  - Files (SMB shares)
  - Disks (VM storage) 
- It provides a unique namespace in Azure for storing and accessing data over the internet using HTTP/HTTPS and REST APIs.
- Each storage account:
  -  Has a unique name and endpoint:
    https://<yourstorageaccount>.blob.core.windows.net
  - Is highly available, durable, and scalable to exabytes of data.
 
# Core Components of Azure Storage
| **Service** | **Description** | **Typical Use Case** |
|--------------|-----------------|----------------------|
| **Blob Storage** | Stores unstructured data (text, images, videos, backups). | Store large files, logs, or static website content. |
| **File Storage** | SMB file shares accessible via the cloud or on-prem. | Lift-and-shift applications, shared drives, Azure Files with AD auth. |
| **Queue Storage** | Simple message queue system for decoupling components. | Asynchronous background task communication. |
| **Table Storage** | NoSQL key-value data store. | Lightweight structured data, logs, IoT metadata. |
| **Disk Storage** | Persistent storage for Azure VMs. | OS and data disks for virtual machines. |

# Storage Account Types:
| **Type** | **Supports** | **Performance** | **Use Case** |
|-----------|---------------|------------------|---------------|
| **General-purpose v2 (GPv2)** | Blobs, Files, Queues, Tables, Disks | Standard/Premium | ✅ Recommended for most workloads |
| **Blob Storage Account** | Only blobs (block, append, page) | Standard/Premium | Data lake, object storage |
| **FileStorage Account** | Only file shares | Premium | Low-latency file shares |
| **BlockBlobStorage Account** | Only block blobs | Premium SSD-backed | High-performance object storage |
| **Premium Storage (GPv1)** | Legacy | Premium | Older VMs, deprecated for new accounts |

# Performance Tiers
| **Tier** | **Description** | **Best for** |
|-----------|----------------|--------------|
| **Hot** | Frequently accessed data | Real-time apps, active files |
| **Cool** | Infrequently accessed (≥30 days) | Backups, medium-term storage |
| **Archive** | Rarely accessed (≥180 days) | Long-term archival, compliance |

# Redundancy (Replication) Options:
| **Type** | **Description** | **Durability** | **Geo-Replication** |
|-----------|----------------|----------------|----------------------|
| **LRS (Locally Redundant Storage)** | Copies data 3× in a single datacenter | 99.999999999% (11 nines) | ❌ No |
| **ZRS (Zone Redundant Storage)** | Copies data across 3 availability zones | 99.9999999999% (12 nines) | ❌ No |
| **GRS (Geo-Redundant Storage)** | LRS + replication to secondary region | 99.99999999999999% (16 nines) | ✅ Yes |
| **GZRS (Geo-Zone Redundant Storage)** | ZRS + replication to secondary region | 99.99999999999999% | ✅ Yes |
| **RA-GRS / RA-GZRS** | Adds read access to secondary region | Same | ✅ Yes (read secondary) |


💡 Tip:

- Use ZRS for high availability within a region.

- Use GZRS for mission-critical, multi-region durability.

  ## 🔒 Security Features

| **Feature** | **Description** |
|--------------|-----------------|
| **Encryption at rest** | Data encrypted using AES-256 (default). |
| **Encryption in transit** | HTTPS required for all operations. |
| **Private endpoints** | Access via private IP in your VNet. |
| **Azure AD Authentication** | RBAC-based identity access control. |
| **Shared Access Signatures (SAS)** | Time-limited access tokens for fine-grained permissions. |
| **Firewalls & IP restrictions** | Restrict access by IP or subnet. |
| **Managed Keys** | Use Microsoft-managed or customer-managed keys (CMK). |

---

## 🧩 Important Concepts

- **Storage Tiers (Hot, Cool, Archive):** Manage cost vs. performance tradeoff  
- **Redundancy Options (LRS, ZRS, GRS, GZRS):** Choose data durability & replication scope  
- **SAS Tokens:** Provide temporary secure access to storage resources  
- **Access Policies:** Automate data lifecycle and retention rules  
- **Blob Versioning & Soft Delete:** Protect against accidental deletions  
- **Data Lake Gen2:** Adds hierarchical namespace for big data analytics

---

## ✅ Summary

| **Category** | **Details** |
|---------------|-------------|
| **Purpose** | Unified platform for object, file, queue, table, and disk storage |
| **Core Types** | Blob, File, Queue, Table, Disk |
| **Performance Tiers** | Hot, Cool, Archive |
| **Redundancy Options** | LRS, ZRS, GRS, GZRS, RA-GZRS |
| **Security** | Encryption, SAS, Private Endpoints, RBAC |
| **Best for** | Cloud-native storage, backups, big data, app integration |

# Sample Code:
```csharp
  using Azure.Storage.Blobs;

  var connectionString = "<your_connection_string>";
  var containerName = "mycontainer";

  BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);
  BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

  await containerClient.CreateIfNotExistsAsync();
  Console.WriteLine("✅ Container created!");
```



