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

## Append vs Block vs Page:

# 🎥 Uploading a Video File — Azure Blob Types Explained

When uploading a video to Azure Blob Storage, you can choose between **Block Blob**, **Append Blob**, and **Page Blob**. Each has a different purpose and internal behavior.

---

## 🧱 1️⃣ Block Blob — (✅ Recommended for Video Uploads)

### 💡 How It Works
- The **video is split into chunks** called **blocks**.
- Each block is uploaded individually (can be done in parallel).
- Once all blocks are uploaded, you send a **Commit Block List** request to assemble the final blob.
- You can **replace individual blocks** later if you need to update or resume uploads.

### ⚙️ Example Flow
```
[myvideo.mp4]
   ↓ (split into)
[Block 1] [Block 2] [Block 3] ... [Block N]
   ↓ (upload in parallel)
Commit → Final Video Blob (myvideo.mp4)
```

### ✅ Best For
- Uploading large video files (multi-GB)
- Streaming downloads (video hosting)
- Updating partial files (resume upload support)

### 💡 Key Benefits
- Parallel upload = faster performance  
- Reliable and resumable (can retry failed blocks)  
- Commonly used for **media content**, **backups**, and **static websites**

---

## 📜 2️⃣ Append Blob — (🚫 Not ideal for video files)

### 💡 How It Works
- You **can only append data** to the blob.
- You **cannot modify or replace existing blocks** once appended.
- Each upload **adds data at the end**, like appending to a log file.

### ⚙️ Example Flow
```
AppendBlob myvideo.log
   ↓
Append(Block 1)
Append(Block 2)
Append(Block 3)
```

If you try to update or resume in the middle (say Block 2 failed),  
you can’t replace it — you’d have to **recreate the blob** from scratch.

### ❌ Problems for Video Files
- No random or partial updates allowed.  
- No parallel block upload support.  
- Can’t “resume” from failed upload point — must start over.  

### ✅ Best For
- Log files (application logs, telemetry streams)  
- Continuous write scenarios (e.g., diagnostic logs)

---

## 📄 3️⃣ Page Blob — (⚙️ Technically possible but inefficient for video)

### 💡 How It Works
- Divides data into **512-byte pages**.
- Each page can be written or updated independently (random read/write access).
- Used for **frequent partial updates**, not sequential uploads.

### ⚙️ Example Flow
```
Page Blob (8 TB max)
   ├── Page 0: bytes 0–511
   ├── Page 1: bytes 512–1023
   ├── Page 2: bytes 1024–1535
   └── ...
```

### ❌ Not Recommended For
- Large sequential uploads (like videos, archives)
- Streaming media (no sequential optimization)
- Public content delivery

### ✅ Best For
- Azure VM disks (`.vhd`)
- Databases with random I/O
- Applications that need frequent partial writes

---

## 🧠 Comparison Table

| **Feature** | **Block Blob** | **Append Blob** | **Page Blob** |
|--------------|----------------|-----------------|----------------|
| **Purpose** | General-purpose object storage | Append-only data (logs) | Random read/write (VM disks) |
| **Data structure** | Data stored in blocks | Data appended as blocks | Data stored in 512-byte pages |
| **Write operations** | Upload blocks, then commit | Append data to end only | Write at specific offsets |
| **Modify existing data** | ✅ Yes (replace blocks) | ❌ No | ✅ Yes (by page range) |
| **Max size** | ~200 GB (Standard), larger in Premium | 195 GB | 8 TB |
| **Performance tier** | Standard/Premium | Standard | Premium (SSD-backed) |
| **Common usage** | Files, backups, media | Logs, telemetry, append-only data | VM disks, databases |
| **Access pattern** | Sequential or random | Sequential (append-only) | Random read/write |

---

## 🎬 Practical Scenario: Uploading a 2 GB Video

| Step | **Block Blob** | **Append Blob** | **Page Blob** |
|------|----------------|-----------------|----------------|
| **Upload** | Split into multiple blocks and upload in parallel | Append one block at a time sequentially | Write 512-byte aligned pages sequentially |
| **Speed** | 🚀 Fast (parallel upload supported) | 🐢 Slow (append-only) | 🐌 Very slow (page writes) |
| **Resumable Upload** | ✅ Yes (retry specific blocks) | ❌ No (must restart) | ⚙️ Possible but complex |
| **Cost Efficiency** | ✅ Optimized | ⚠️ Acceptable for small files | ❌ Expensive for large sequential data |
| **Use Case Fit** | 🎥 Perfect for large video files | 🚫 Not suitable | 🚫 Not suitable |

---

## ✅ Best Practice Recommendation

👉 **Always use Block Blob for videos, images, large files, or backups.**

Because:
- It supports **parallelism**, **resumability**, and **optimized throughput**.
- You can **upload partially**, **retry**, and **replace blocks** without re-uploading the entire file.
- It’s natively integrated with **Azure CDN** for video delivery.

---

## ⚙️ Example C# Upload for Video (Block Blob)

```csharp
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        string connectionString = "<STORAGE_CONNECTION_STRING>";
        string containerName = "videos";
        string blobName = "myvideo.mp4";
        string filePath = "C:\Videos\myvideo.mp4";

        BlobServiceClient service = new BlobServiceClient(connectionString);
        BlobContainerClient container = service.GetBlobContainerClient(containerName);
        await container.CreateIfNotExistsAsync();

        BlobClient blobClient = container.GetBlobClient(blobName);

        Console.WriteLine("Uploading video...");
        await blobClient.UploadAsync(filePath, overwrite: true);
        Console.WriteLine("✅ Upload complete (Block Blob used).");
    }
}
```

---

## 🧾 TL;DR Summary

| **Blob Type** | **Best For** | **Why / Behavior** |
|----------------|--------------|--------------------|
| **Block Blob** | Videos, large files | Split into blocks, parallel upload, resumable |
| **Append Blob** | Logs, telemetry | Append-only, no modification |
| **Page Blob** | VM disks | Random I/O, 512-byte pages |




