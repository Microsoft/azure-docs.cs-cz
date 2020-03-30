---
title: Správa vlastností a metadat pro objekt blob pomocí rozhraní .NET – Azure Storage
description: Zjistěte, jak nastavit a načíst vlastnosti systému a ukládat vlastní metadata na objekty BLOB v účtu azure úložiště pomocí knihovny klienta .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137658"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Správa vlastností objektu blob a metadat pomocí rozhraní .NET

Kromě dat, která obsahují, objekty BLOB podporují vlastnosti systému a uživatelem definovaná metadata. Tento článek ukazuje, jak spravovat vlastnosti systému a uživatelem definovaná metadata pomocí [klientské knihovny Azure Storage pro rozhraní .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Vlastnosti a metadata

- **Vlastnosti systému**: Systémové vlastnosti existují na každém prostředku úložiště objektů Blob. Některé z nich lze číst nebo nastavit, zatímco jiné jsou jen pro čtení. Pod kryty některé vlastnosti systému odpovídají určitým standardním hlavičkám HTTP. Klientská knihovna Azure Storage pro rozhraní .NET tyto vlastnosti za vás udržuje.

- **Uživatelem definovaná metadata**: Uživatelem definovaná metadata se skládají z jednoho nebo více párů název-hodnota, které zadáte pro prostředek úložiště objektů Blob. Metadata můžete použít k uložení dalších hodnot s prostředkem. Hodnoty metadat jsou pouze pro vaše vlastní účely a nemají vliv na chování prostředku.

Načítání metadat a hodnot vlastností pro prostředek úložiště objektů blob je dvoustupňový proces. Před čtením těchto hodnot je nutné explicitně `FetchAttributes` `FetchAttributesAsync` načíst voláním metody nebo. Výjimkou z tohoto pravidla `Exists` je, že metody a `ExistsAsync` volají příslušnou `FetchAttributes` metodu pod kryty. Při volání jedné z těchto metod není nutné `FetchAttributes`volat také .

> [!IMPORTANT]
> Pokud zjistíte, že hodnoty vlastnosti nebo metadat pro prostředek úložiště nebyly `FetchAttributes` naplněny, zkontrolujte, zda váš kód volá metodu nebo. `FetchAttributesAsync`

## <a name="set-and-retrieve-properties"></a>Nastavení a načtení vlastností

Následující příklad kódu `ContentType` nastaví vlastnosti a `ContentLanguage` systému na objekt blob.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Chcete-li načíst vlastnosti `FetchAttributesAsync` objektu blob, `Properties` zavolejte metodu `FetchAttributes` nebo na objektblou k naplnění vlastnosti. Následující příklad kódu získá vlastnosti systému objektu blob a zobrazí některé hodnoty:

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>Nastavení a načtení metadat

Metadata můžete zadat jako jeden nebo více párů název-hodnota na objektu blob nebo prostředek kontejneru. Chcete-li nastavit metadata, přidejte `Metadata` dvojice název-hodnota do kolekce na prostředek. Potom zavolejte jednu z následujících metod pro zápis hodnot:

- [Nastavit metadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Dvojice názvů a hodnot metadat jsou platná hlavičky HTTP a měla by dodržovat všechna omezení, kterými se řídí hlavičky HTTP. Názvy metadat musí být platné názvy hlaviček PROTOKOLU HTTP a platné identifikátory jazyka C#, mohou obsahovat pouze znaky ASCII a měly by být považovány za nerozlišující malá a velká písmena. [Base64-encode](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) nebo [URL-encode](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) metadata hodnoty obsahující non-ASCII znaky.

Název metadat musí odpovídat konvencím pojmenování identifikátorů jazyka C#. Názvy metadat zachovat případ používaný při jejich vytvoření, ale jsou malá a velká písmena při nastavení nebo čtení. Pokud dvě nebo více záhlaví metadat se stejným názvem jsou odeslány pro prostředek, Azure Blob storage vrátí kód chyby HTTP 400 (Chybný požadavek).

Následující příklad kódu nastaví metadata na objekt blob. Jedna hodnota je nastavena `Add` pomocí metody kolekce. Druhá hodnota je nastavena pomocí implicitní syntaxe klíč/hodnota.

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Chcete-li načíst `FetchAttributes` metadata, zavolejte metodu `FetchAttributesAsync` nebo `Metadata` na objektblob nebo kontejner k naplnění kolekce a pak si přečtěte hodnoty, jak je znázorněno v příkladu níže.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Viz také

- [Nastavit operaci vlastností objektu blob](/rest/api/storageservices/set-blob-properties)
- [Získat operaci Vlastnosti objektu blob](/rest/api/storageservices/get-blob-properties)
- [Nastavení operace metadat objektu blob](/rest/api/storageservices/set-blob-metadata)
- [Získání operace metadat objektu Blob](/rest/api/storageservices/get-blob-metadata)
