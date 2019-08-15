---
title: Vytvoření SAS pro delegování uživatelů pro kontejner nebo objekt BLOB pomocí .NET (Preview) – Azure Storage
description: Naučte se, jak vytvořit SAS delegování uživatelů pomocí Azure Active Directory přihlašovacích údajů v Azure Storage pomocí klientské knihovny .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: bed95c070649785a701f9d08a98faf29c8ee1413
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990686"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Vytvoření SAS pro delegování uživatelů pro kontejner nebo objekt BLOB pomocí .NET (Preview)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

V tomto článku se dozvíte, jak pomocí pověření Azure Active Directory (Azure AD) vytvořit přidružení zabezpečení delegování uživatelů pro kontejner nebo objekt BLOB pomocí [Azure Storage klientské knihovny pro .NET](https://www.nuget.org/packages/Azure.Storage.Blobs).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>Instalace balíčků verze Preview

Příklady v tomto článku využívají nejnovější verzi Preview klientské knihovny Azure Storage pro úložiště objektů BLOB. Chcete-li nainstalovat balíček verze Preview, spusťte následující příkaz z konzoly Správce balíčků NuGet:

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Příklady v tomto článku také využívají nejnovější verzi Preview [klientské knihovny Azure identity pro .NET](https://www.nuget.org/packages/Azure.Identity/) k ověřování pomocí přihlašovacích údajů Azure AD. Klientská knihovna identit Azure ověřuje objekt zabezpečení. Ověřený objekt zabezpečení pak může vytvořit přidružení zabezpečení delegování uživatele. Další informace o klientské knihovně identit Azure najdete v tématu [Klientská knihovna Azure identity pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

K ověřování pomocí přihlašovacích údajů Azure AD pomocí klientské knihovny Azure identity použijte instanční objekt nebo spravovanou identitu jako objekt zabezpečení, v závislosti na tom, kde je váš kód spuštěný. Pokud je váš kód spuštěn ve vývojovém prostředí, použijte instanční objekt pro účely testování. Pokud je váš kód spuštěný v Azure, použijte spravovanou identitu. V tomto článku se předpokládá, že spouštíte kód z vývojového prostředí, a ukazuje, jak pomocí instančního objektu vytvořit přidružení zabezpečení delegování uživatele.

Pokud chcete vytvořit instanční objekt pomocí Azure CLI a přiřadit roli RBAC, zavolejte příkaz [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Zadejte Azure Storage roli přístupu k datům, která se přiřadí k novému instančnímu objektu. Role musí zahrnovat akci **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Další informace o předdefinovaných rolích, které jsou k dispozici pro Azure Storage, najdete v tématu [předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

Kromě toho zadejte obor pro přiřazení role. Instanční objekt vytvoří klíč delegování uživatele, což je operace prováděná na úrovni účtu úložiště, takže přiřazení role by mělo být vymezeno na úrovni účtu úložiště, skupiny prostředků nebo předplatného. Další informace o oprávněních RBAC pro vytvoření SAS delegování uživatele najdete v části **přiřazení oprávnění s RBAC** v tématu [Vytvoření sas uživatele (REST API)](/rest/api/storageservices/create-a-user-delegation-sas).

Pokud nemáte dostatečná oprávnění k přiřazení role k instančnímu objektu, může být nutné požádat vlastníka nebo správce účtu, aby provedl přiřazení role.

V následujícím příkladu se pomocí Azure CLI vytvoří nový instanční objekt a přiřadí se k němu role **čtečky dat objektů BLOB úložiště** s rozsahem účtu.

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

`az ad sp create-for-rbac` Příkaz vrátí seznam vlastností instančního objektu ve formátu JSON. Zkopírujte tyto hodnoty, abyste je mohli použít k vytvoření potřebných proměnných prostředí v dalším kroku.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Rozšiřování přiřazení rolí RBAC může trvat několik minut.

## <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Klientská knihovna Azure identity načítá při ověřování instančního objektu hodnoty ze tří proměnných prostředí za běhu. Následující tabulka popisuje hodnotu, která se má nastavit pro každou proměnnou prostředí.

|Proměnná prostředí|Value
|-|-
|`AZURE_CLIENT_ID`|ID aplikace pro instanční objekt
|`AZURE_TENANT_ID`|ID tenanta Azure AD objektu služby
|`AZURE_CLIENT_SECRET`|Heslo generované pro instanční objekt

> [!IMPORTANT]
> Po nastavení proměnných prostředí zavřete a znovu otevřete okno konzoly. Pokud používáte aplikaci Visual Studio nebo jiné vývojové prostředí, může být nutné restartovat vývojové prostředí, aby bylo možné zaregistrovat nové proměnné prostředí.

## <a name="add-using-directives"></a>Přidání direktiv using

Přidejte následující `using` direktivy do kódu pro použití verze Preview klientských knihoven Azure identity a Azure Storage.

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

## <a name="authenticate-the-service-principal"></a>Ověření instančního objektu

Chcete-li ověřit instanční objekt, vytvořte instanci třídy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) . `DefaultAzureCredential` Konstruktor přečte proměnné prostředí, které jste vytvořili dříve.

Následující fragment kódu ukazuje, jak získat ověřené pověření a použít ho k vytvoření klienta služby pro úložiště objektů BLOB.

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

## <a name="example-get-a-user-delegation-sas"></a>Příklad: Získat SAS pro delegování uživatelů

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

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Příklad: Čtení objektu BLOB s SAS delegování uživatele

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

## <a name="see-also"></a>Viz také:

- [Získat operaci klíče delegování uživatele](/rest/api/storageservices/get-user-delegation-key)
- [Vytvoření SAS delegování uživatele (REST API)](/rest/api/storageservices/create-a-user-delegation-sas)
