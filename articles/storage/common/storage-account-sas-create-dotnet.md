---
title: Vytvoření SAS účtu pomocí .NET
titleSuffix: Azure Storage
description: Naučte se vytvořit sdílený přístupový podpis (SAS) účtu pomocí klientské knihovny .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 1cb882ac1051c41f4d887a9ff4dd8cd64bc9f56c
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593599"
---
# <a name="create-an-account-sas-with-net"></a>Vytvoření SAS účtu pomocí .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

V tomto článku se dozvíte, jak pomocí klíče účtu úložiště vytvořit SAS účtu pomocí [klientské knihovny Azure Storage pro .NET](/dotnet/api/overview/azure/storage).

## <a name="create-an-account-sas"></a>Vytvoření SAS účtu

### <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Podpis SAS účtu se podepisuje pomocí přístupového klíče účtu. Použijte třídu [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) k vytvoření přihlašovacích údajů, které slouží k podepsání SAS. Dále vytvořte nový objekt [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) a zavolejte [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) pro získání řetězce tokenu SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Chcete-li vytvořit přidružení zabezpečení účtu pro kontejner, zavolejte metodu [CloudStorageAccount. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) .

Následující příklad kódu vytvoří podpis SAS účtu, který je platný pro objekty BLOB a souborové služby, a poskytne oprávnění ke čtení, zápisu a vypsání oprávnění klienta pro přístup k rozhraním API na úrovni služby. Podpis SAS účtu omezuje protokol na HTTPS, takže požadavek musí být proveden pomocí protokolu HTTPS. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

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

---

## <a name="use-an-account-sas-from-a-client"></a>Použití SAS účtu z klienta

Pokud chcete použít SAS účtu pro přístup k rozhraním API na úrovni služby pro Blob service, Sestavte objekt klienta Blob service pomocí SAS a koncového bodu BLOB Storage pro váš účet úložiště.

### <a name="net-v12"></a>[\.NET V12](#tab/dotnet)



:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

V tomto fragmentu kódu nahraďte `<storage-account>` zástupný symbol názvem vašeho účtu úložiště.

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

---

## <a name="next-steps"></a>Další kroky

- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md)
- [Vytvoření SAS účtu](/rest/api/storageservices/create-account-sas)
