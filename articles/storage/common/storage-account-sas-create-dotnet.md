---
title: Vytvoření účtu SAS s rozhraním .NET
titleSuffix: Azure Storage
description: Zjistěte, jak vytvořit podpis sdíleného přístupu účtu (SAS) pomocí klientské knihovny .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9da27cef7bafa94715a42db86fc5a5675a049eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137865"
---
# <a name="create-an-account-sas-with-net"></a>Vytvoření účtu SAS s rozhraním .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Tento článek ukazuje, jak pomocí klíče účtu úložiště vytvořit účet SAS s [klientskou knihovnou Úložiště Azure pro .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="create-an-account-sas"></a>Vytvoření SAS účtu

Chcete-li vytvořit účet SAS pro kontejner, zavolejte metodu [CloudStorageAccount.GetSharedAccessSignature.](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature)

Následující příklad kódu vytvoří účet SAS, který je platný pro služby blob a soubor a poskytuje oprávnění klienta ke čtení, zápisu a seznamu oprávnění pro přístup k souborům API na úrovni služby. Účet SAS omezuje protokol na HTTPS, takže požadavek musí být proveden pomocí protokolu HTTPS. Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

## <a name="use-an-account-sas-from-a-client"></a>Použití účtu SAS od klienta

Chcete-li použít účet SAS pro přístup k api na úrovni služby pro službu Blob, vytvořte objekt klienta služby Blob pomocí Koncového bodu úložiště SAS a úložiště objektů blob pro váš účet úložiště. Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

## <a name="next-steps"></a>Další kroky

- [Udělit omezený přístup k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md)
- [Vytvoření SAS účtu](/rest/api/storageservices/create-account-sas)
