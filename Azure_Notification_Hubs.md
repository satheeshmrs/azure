# Azure Notification Hubs (ANS)?

- Azure Notification Hubs is a fully managed push notification service that enables you to send instant push messages to millions of mobile devices, across multiple platforms (iOS, Android, Windows, etc.) — all from a single API.
- Think of it as a bridge between your app backend and mobile platform notification systems, such as:
  -  Apple Push Notification Service (APNs)
  -  Firebase Cloud Messaging (FCM) (for Android)
  -  Windows Push Notification Service (WNS)
  -  Huawei Push Kit, etc.
 
  # Notification workflow:

 ```csharp
  Your App Backend (API)
       │
       ▼
 Azure Notification Hub
       │
       ├──► Apple Push Notification Service (APNs)
       │       └──► iOS Devices
       │
       ├──► Firebase Cloud Messaging (FCM)
       │       └──► Android Devices
       │
       └──► Windows Notification Service (WNS)
               └──► Windows Devices
 ```

# Core Concepts

| **Concept** | **Description** |
|--------------|-----------------|
| **Namespace** | The container for one or more Notification Hubs |
| **Notification Hub** | Logical hub that manages registration and delivery of notifications |
| **Registration** | The mapping between a device and its push handle (token) |
| **Tag** | Metadata for targeting specific audiences (e.g., `region:US`, `user:1001`) |
| **Template** | Message format allowing localization and personalization |
| **Push Handle** | Platform-specific device token from FCM, APNs, etc. |
| **Credential** | The key or certificate needed to authenticate with FCM/APNs/WNS |
