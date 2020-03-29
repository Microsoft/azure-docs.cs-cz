---
title: Použití rozhraní .NET k vytvoření uživatelského delegování SAS pro kontejner nebo objekt blob
titleSuffix: Azure Storage
description: Zjistěte, jak vytvořit delegování uživatele SAS s přihlašovacími údaji služby Azure Active Directory pomocí knihovny klienta .NET pro Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371832"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Vytvoření uživatelskédelegování SAS pro kontejner nebo objekt blob s rozhraním .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Tento článek ukazuje, jak pomocí přihlašovacích údajů služby Azure Active Directory (Azure AD) vytvořit delegování uživatele SAS pro kontejner nebo objekt blob s klientskou knihovnou Azure Storage pro .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>Přiřazení rolí RBAC pro přístup k datům

Když se objekt zabezpečení Azure AD pokusí o přístup k datům objektů blob, tento objekt zabezpečení musí mít oprávnění k prostředku. Ať už je objekt zabezpečení spravovanou identitou v Azure nebo uživatelský účet Azure AD spuštěný kód ve vývojovém prostředí, musí být objektu zabezpečení přiřazena role RBAC, která uděluje přístup k datům objektů blob ve službě Azure Storage. Informace o přiřazování oprávnění prostřednictvím služby RBAC naleznete v části **Přiřazení rolí RBAC pro přístupová práva** v [tématu Autorizace přístupu k objektům BLOB a frontám Azure pomocí služby Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Další informace o tom, jak se ověřovat pomocí klientské knihovny Azure Identity z Azure Storage, najdete v části **Ověření pomocí knihovny Azure Identity** v [tématu Autorizace přístupu k objektům BLOB a frontám s Azure Active Directory a spravovaným i pro prostředky Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="add-using-directives"></a>Přidání direktiv using

Přidejte `using` do kódu následující direktivy, abyste použili klientské knihovny Azure Identity a Azure Storage.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Získání ověřeného pověření tokenu

Chcete-li získat pověření tokenu, které váš kód můžete použít k autorizaci požadavků na Azure Storage, vytvořte instanci třídy [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential)

Následující fragment kódu ukazuje, jak získat pověření ověřeného tokenu a použít ho k vytvoření klienta služby pro úložiště objektů Blob:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Získání klíče delegování uživatele

Každý SAS je podepsán klíčem. Chcete-li vytvořit uživatelské delegování SAS, musíte nejprve požádat o klíč delegování uživatele, který se pak používá k podepsání SAS. Klíč delegování uživatele je obdobou klíče účtu, který se používá k podepsání služby SAS nebo s účtem SAS, s tím rozdílem, že závisí na pověření Azure AD. Když klient požádá o klíč delegování uživatele pomocí tokenu OAuth 2.0, Azure Storage vrátí klíč delegování uživatele jménem uživatele.

Jakmile budete mít klíč delegování uživatele, můžete tento klíč použít k vytvoření libovolného počtu podpisů sdíleného přístupu delegování uživatelů po dobu životnosti klíče. Klíč delegování uživatele je nezávislý na tokenu OAuth 2.0, který se používá k jeho získání, takže token nemusí být obnoven, dokud je klíč stále platný. Můžete určit, že klíč je platný po dobu až 7 dnů.

Pomocí jedné z následujících metod požádejte o klíč delegování uživatele:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Následující fragment kódu získá klíč delegování uživatele a zapíše jeho vlastnosti:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Vytvoření tokenu SAS

Následující fragment kódu zobrazuje vytvořit nový [Objekt blobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) a poskytnout parametry pro delegování uživatele SAS. Úryvek pak volá [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) získat řetězec tokenu SAS. Nakonec kód vytvoří úplný identifikátor URI, včetně adresy prostředku a tokenu SAS.

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

// Use the key to get the SAS token.
string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

// Construct the full URI, including the SAS token.
UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Příklad: Získání uživatelské delegace SAS

Následující příklad metody ukazuje kompletní kód pro ověření objektu zabezpečení a vytvoření uživatelského delegování SAS:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Příklad: Čtení objektu blob s uživatelem delegování SAS

Následující příklad testuje delegování uživatele SAS vytvořené v předchozím příkladu ze simulované klientské aplikace. Pokud je SAS platný, klientská aplikace je schopna číst obsah objektu blob. Pokud je SAS neplatný, například pokud vypršela jeho platnost, Azure Storage vrátí kód chyby 403 (Zakázáno).

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid,
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Viz také

- [Udělit omezený přístup k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md)
- [Získat operaci klíče delegování uživatele](/rest/api/storageservices/get-user-delegation-key)
- [Vytvoření uživatelskédelegace SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
