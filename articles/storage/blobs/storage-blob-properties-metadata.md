---
title: Správa vlastností a metadat pro objekt BLOB pomocí .NET-Azure Storage
description: Naučte se, jak nastavit a načíst systémové vlastnosti a ukládat do objektů BLOB ve vašem účtu Azure Storage vlastní metadata pomocí klientské knihovny .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137658"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Správa vlastností a metadat objektů BLOB pomocí .NET

Kromě dat, která obsahují, podporují objekty blob vlastnosti systému a uživatelsky definovaná metadata. V tomto článku se dozvíte, jak spravovat vlastnosti systému a uživatelsky definovaná metadata pomocí [Azure Storage klientské knihovny pro .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>O vlastnostech a metadatech

- **Vlastnosti systému**: systémové vlastnosti existují v každém prostředku úložiště objektů BLOB. Některé z nich je možné číst nebo nastavovat, zatímco jiné jsou jen pro čtení. V rámci pokrývá některé vlastnosti systému odpovídají určitým standardním hlavičkám HTTP. Klientská knihovna Azure Storage pro .NET uchovává tyto vlastnosti za vás.

- **Uživatelsky definovaná metadata**: uživatelsky definovaná metadata se skládají z jedné nebo více párů název-hodnota, které zadáte pro prostředek BLOB Storage. Metadata můžete použít k ukládání dalších hodnot s prostředkem. Hodnoty metadat jsou pouze pro vaše vlastní účely a neovlivňují způsob, jakým se prostředek chová.

Načítání metadat a hodnot vlastností prostředku BLOB Storage je proces se dvěma kroky. Než budete moci číst tyto hodnoty, je nutné je explicitně načíst voláním metody `FetchAttributes` nebo `FetchAttributesAsync`. Výjimkou z tohoto pravidla je, že metody `Exists` a `ExistsAsync` na základě pokrývá volání příslušné `FetchAttributes` metody. Při volání jedné z těchto metod nemusíte také volat `FetchAttributes`.

> [!IMPORTANT]
> Pokud zjistíte, že vlastnost nebo hodnoty metadat pro prostředek úložiště nejsou naplněné, ověřte, že váš kód volá metodu `FetchAttributes` nebo `FetchAttributesAsync`.

## <a name="set-and-retrieve-properties"></a>Nastavení a načtení vlastností

Následující příklad kódu nastaví `ContentType` a vlastnosti systému `ContentLanguage` v objektu BLOB.

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

Chcete-li načíst vlastnosti objektu blob, zavolejte do objektu BLOB metodu `FetchAttributes` nebo `FetchAttributesAsync` a naplňte vlastnost `Properties`. Následující příklad kódu získá vlastnosti systému objektu BLOB a zobrazí některé z těchto hodnot:

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

Metadata můžete zadat jako jednu nebo více párů název-hodnota u prostředku BLOB nebo kontejneru. Chcete-li nastavit metadata, přidejte páry název-hodnota do kolekce `Metadata` v prostředku. Pak zavolejte jednu z následujících metod pro zápis hodnot:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Páry název-hodnota metadat jsou platné hlavičky protokolu HTTP a měly by splňovat všechna omezení, kterými se řídí hlavičky protokolu HTTP. Názvy metadat musí být platné názvy hlaviček protokolu HTTP a C# platné identifikátory, může obsahovat pouze znaky ASCII a měly by se považovat za nerozlišování velkých a malých písmen. Hodnoty metadat kódování [Base64](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) nebo [URL](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) , které obsahují znaky jiné než ASCII.

Název vašich metadat musí odpovídat konvencím pojmenování C# identifikátorů. Názvy metadat udržují případ použitý při jejich vytvoření, ale při nastavení nebo čtení se nerozlišují malá a velká písmena. Pokud se pro prostředek odešlou dvě nebo víc hlaviček metadat s použitím stejného názvu, Azure Blob Storage vrátí kód chyby HTTP 400 (chybný požadavek).

Následující příklad kódu nastavuje metadata v objektu BLOB. Jedna hodnota je nastavena pomocí `Add` metody kolekce. Druhá hodnota je nastavena pomocí implicitní syntaxe klíč/hodnota.

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

Chcete-li načíst metadata, zavolejte metodu `FetchAttributes` nebo `FetchAttributesAsync` v objektu BLOB nebo kontejneru pro naplnění kolekce `Metadata` a pak hodnoty přečtěte, jak je znázorněno v následujícím příkladu.

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

## <a name="see-also"></a>Viz také:

- [Operace nastavení vlastností objektu BLOB](/rest/api/storageservices/set-blob-properties)
- [Operace získání vlastností objektu BLOB](/rest/api/storageservices/get-blob-properties)
- [Operace nastavení metadat objektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Operace získání metadat objektu BLOB](/rest/api/storageservices/get-blob-metadata)
