---
title: Vytvoření SAS pro delegování uživatelů pro kontejner nebo objekt BLOB pomocí .NET (Preview) – Azure Storage
description: Naučte se, jak vytvořit SAS delegování uživatelů pomocí Azure Active Directory přihlašovacích údajů v Azure Storage pomocí klientské knihovny .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c75a13a20c1dbb222db69145e24838deb111fb66
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595216"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Vytvoření SAS pro delegování uživatelů pro kontejner nebo objekt BLOB pomocí .NET (Preview)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

V tomto článku se dozvíte, jak pomocí pověření Azure Active Directory (Azure AD) vytvořit přidružení zabezpečení delegování uživatelů pro kontejner nebo objekt BLOB pomocí Azure Storage klientské knihovny pro .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Ověřování pomocí knihovny identit Azure (Preview)

Klientská knihovna Azure identity pro .NET (Preview) ověřuje objekt zabezpečení. Když váš kód běží v Azure, je objekt zabezpečení spravovaná identita pro prostředky Azure.

Když váš kód běží ve vývojovém prostředí, ověřování může být zpracováno automaticky nebo může vyžadovat přihlášení prohlížeče v závislosti na tom, které nástroje používáte. Microsoft Visual Studio podporuje jednotné přihlašování (SSO), aby se aktivní uživatelský účet Azure AD automaticky používal pro ověřování. Další informace o JEDNOTNÉm přihlašování najdete v tématu [jednotné přihlašování k aplikacím](../../active-directory/manage-apps/what-is-single-sign-on.md).

Jiné vývojové nástroje vás můžou vyzvat k přihlášení přes webový prohlížeč. K ověření z vývojového prostředí můžete také použít instanční objekt. Další informace najdete v tématu věnovaném [vytvoření identity pro aplikaci Azure na portálu](../../active-directory/develop/howto-create-service-principal-portal.md).

Po ověření získá Klientská knihovna identity Azure přihlašovací údaje tokenu. Tyto přihlašovací údaje tokenu se pak zapouzdřují v objektu klienta služby, který vytvoříte k provádění operací s Azure Storage. Knihovna to zvládne bezproblémově tím, že získá příslušné přihlašovací údaje tokenu.

Další informace o klientské knihovně identit Azure najdete v tématu [Klientská knihovna Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Přiřazení rolí RBAC pro přístup k datům

Když se objekt zabezpečení služby Azure AD pokusí získat přístup k datům objektu blob, musí mít tento objekt zabezpečení oprávnění k prostředku. Bez ohledu na to, jestli je objekt zabezpečení spravovaná identita v Azure nebo uživatelský účet Azure AD, který spouští kód ve vývojovém prostředí, musí být objektu zabezpečení přiřazená role RBAC, která uděluje přístup k datům objektu BLOB v Azure Storage. Informace o přiřazování oprávnění přes RBAC najdete v části s názvem **přiřazení rolí RBAC pro přístupová práva** v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>Instalace balíčků verze Preview

Příklady v tomto článku využívají nejnovější verzi Preview [klientské knihovny Azure Storage pro úložiště objektů BLOB](https://www.nuget.org/packages/Azure.Storage.Blobs). Chcete-li nainstalovat balíček verze Preview, spusťte následující příkaz z konzoly Správce balíčků NuGet:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Příklady v tomto článku také využívají nejnovější verzi Preview [klientské knihovny Azure identity pro .NET](https://www.nuget.org/packages/Azure.Identity/) k ověřování pomocí přihlašovacích údajů Azure AD. Chcete-li nainstalovat balíček verze Preview, spusťte následující příkaz z konzoly Správce balíčků NuGet:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="add-using-directives"></a>Přidání direktiv using

Do kódu přidejte následující direktivy `using`, abyste mohli používat verze Preview klientských knihoven identit Azure a Azure Storage.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Získání přihlašovacích údajů ověřeného tokenu

Chcete-li získat přihlašovací údaje tokenu, které může váš kód použít k autorizaci požadavků na Azure Storage, vytvořte instanci třídy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) .

Následující fragment kódu ukazuje, jak získat pověření ověřeného tokenu a použít ho k vytvoření klienta služby pro úložiště objektů BLOB:

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Získat klíč delegování uživatele

Každé přidružení zabezpečení je podepsáno klíčem. Chcete-li vytvořit SAS delegování uživatele, je třeba nejprve požádat o klíč pro delegování uživatele, který je pak použit k podepsání SAS. Klíč pro delegování uživatelů je podobný klíči účtu, který se používá k podepsání SAS služby nebo k přidružení zabezpečení účtu, s tím rozdílem, že spoléhá na vaše přihlašovací údaje Azure AD. Když klient požádá o klíč delegování uživatele pomocí tokenu OAuth 2,0, Azure Storage vrátí klíč delegování uživatele jménem uživatele.

Po získání klíče pro delegování uživatelů můžete pomocí tohoto klíče vytvořit libovolný počet sdílených přístupových podpisů uživatelů po celou dobu životnosti klíče. Klíč pro delegování uživatelů je nezávislý na tokenu OAuth 2,0, který se používá k získání, takže token není nutné obnovit, pokud je klíč stále platný. Můžete určit, že klíč je platný po dobu až 7 dnů.

Pro vyžádání klíče delegování uživateli použijte jednu z následujících metod:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Následující fragment kódu získá klíč delegování uživatele a zapíše jeho vlastnosti:

```csharp
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Vytvoření tokenu SAS

Následující fragment kódu ukazuje vytvořit novou [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) a zadat parametry pro SAS delegování uživatele. Fragment kódu pak zavolá [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) , aby získal řetězec tokenu SAS. Nakonec kód vytvoří úplný identifikátor URI, včetně adresy prostředku a tokenu SAS.

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Příklad: získání SAS uživatele pro delegování

Následující příklad metody ukazuje úplný kód pro ověření objektu zabezpečení a vytvoření SAS delegování uživatele:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

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
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Příklad: čtení objektu BLOB s SAS delegování uživatele

Následující příklad testuje SAS uživatele z simulované klientské aplikace vytvořené v předchozím příkladu. Pokud je podpis SAS platný, klientská aplikace může číst obsah objektu BLOB. Pokud je SAS neplatné, například pokud platnost vypršela, Azure Storage vrátí kód chyby 403 (zakázáno).

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
    catch (StorageRequestFailedException e)
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

## <a name="see-also"></a>Další informace najdete v tématech

- [Získat operaci klíče delegování uživatele](/rest/api/storageservices/get-user-delegation-key)
- [Vytvoření SAS delegování uživatele (REST API)](/rest/api/storageservices/create-user-delegation-sas)
