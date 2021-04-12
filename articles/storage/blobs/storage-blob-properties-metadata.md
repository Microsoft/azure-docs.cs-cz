---
title: Správa vlastností a metadat pro objekt BLOB pomocí .NET-Azure Storage
description: Naučte se, jak nastavit a načíst systémové vlastnosti a ukládat do objektů BLOB ve vašem účtu Azure Storage vlastní metadata pomocí klientské knihovny .NET.
services: storage
author: twooley
ms.author: twooley
ms.date: 09/25/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 24ec646c2928570c67a7f71481f2ca0191f1c8b9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280203"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Správa vlastností a metadat objektů BLOB pomocí .NET

Kromě dat, která obsahují, podporují objekty blob vlastnosti systému a uživatelsky definovaná metadata. V tomto článku se dozvíte, jak spravovat vlastnosti systému a uživatelsky definovaná metadata pomocí [Azure Storage klientské knihovny pro .NET](/dotnet/api/overview/azure/storage).

## <a name="about-properties-and-metadata"></a>O vlastnostech a metadatech

- **Vlastnosti systému**: systémové vlastnosti existují v každém prostředku úložiště objektů BLOB. Některé z nich je možné číst nebo nastavovat, zatímco jiné jsou jen pro čtení. V rámci pokrývá některé vlastnosti systému odpovídají určitým standardním hlavičkám HTTP. Klientská knihovna Azure Storage pro .NET uchovává tyto vlastnosti za vás.

- **Uživatelsky definovaná metadata**: uživatelsky definovaná metadata se skládají z jedné nebo více párů název-hodnota, které zadáte pro prostředek BLOB Storage. Metadata můžete použít k ukládání dalších hodnot s prostředkem. Hodnoty metadat jsou pouze pro vaše vlastní účely a neovlivňují způsob, jakým se prostředek chová.

> [!NOTE]
> Značky indexu objektů BLOB také umožňují ukládat libovolné uživatelsky definované atributy klíč/hodnota společně s prostředkem úložiště objektů BLOB v Azure. I když jsou podobné metadatům, jsou automaticky indexovány a prohledány pomocí nativní služby BLOB Service pouze značky indexu objektů BLOB. Metadata nelze indexovat a dotazovat, pokud nevyužíváte samostatnou službu, například Azure Search.
>
> Další informace o této funkci najdete v tématu [Správa a hledání dat v úložišti objektů BLOB v Azure pomocí indexu objektů BLOB (Preview)](storage-manage-find-blobs.md).

## <a name="set-and-retrieve-properties"></a>Nastavení a načtení vlastností

Následující příklad kódu nastavuje `ContentType` `ContentLanguage` Vlastnosti systému a v objektu BLOB.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Chcete-li nastavit vlastnosti objektu blob, zavolejte [SetHttpHeaders](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheaders) nebo [SetHttpHeadersAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheadersasync). Všechny vlastnosti, které nejsou explicitně nastaveny, jsou vymazány. Následující příklad kódu nejprve získá existující vlastnosti objektu BLOB a pak je použije k naplnění hlaviček, které se neaktualizují.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_SetBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

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
---

Následující příklad kódu získá vlastnosti systému objektu BLOB a zobrazí některé z těchto hodnot.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Načítání metadat a hodnot vlastností prostředku BLOB Storage je proces se dvěma kroky. Než budete moci číst tyto hodnoty, je nutné je explicitně načíst voláním `FetchAttributes` metody nebo `FetchAttributesAsync` . Výjimkou z tohoto pravidla je, že `Exists` `ExistsAsync` metody a volají odpovídající `FetchAttributes` metodu v rámci pokrývání. Při volání jedné z těchto metod není nutné volat současně `FetchAttributes` .

> [!IMPORTANT]
> Pokud zjistíte, že vlastnost nebo hodnoty metadat pro prostředek úložiště nejsou naplněné, ověřte, že váš kód volá `FetchAttributes` `FetchAttributesAsync` metodu nebo.

Chcete-li načíst vlastnosti objektu blob, zavolejte `FetchAttributes` `FetchAttributesAsync` metodu or objektu BLOB a naplňte `Properties` vlastnost.

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
---

## <a name="set-and-retrieve-metadata"></a>Nastavení a načtení metadat

Metadata můžete zadat jako jednu nebo více párů název-hodnota u prostředku BLOB nebo kontejneru. Chcete-li nastavit metadata, přidejte páry název-hodnota do `Metadata` kolekce v prostředku. Pak zavolejte jednu z následujících metod pro zápis hodnot:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [SetMetadata](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadataasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)
---

Páry název-hodnota metadat jsou platné hlavičky protokolu HTTP a měly by splňovat všechna omezení, kterými se řídí hlavičky protokolu HTTP. Názvy metadat musí být platné názvy hlaviček protokolu HTTP a platné identifikátory jazyka C#, mohou obsahovat pouze znaky ASCII a měly by se považovat za nerozlišování velkých a malých písmen. Hodnoty metadat kódování [Base64](/dotnet/api/system.convert.tobase64string) nebo [URL](/dotnet/api/system.web.httputility.urlencode) , které obsahují znaky jiné než ASCII.

Název vašich metadat musí odpovídat konvencím pojmenování identifikátorů C#. Názvy metadat udržují případ použitý při jejich vytvoření, ale při nastavení nebo čtení se nerozlišují malá a velká písmena. Pokud se pro prostředek odešlou dvě nebo víc hlaviček metadat s použitím stejného názvu, Azure Blob Storage vrátí kód chyby HTTP 400 (chybný požadavek).

Následující příklad kódu nastavuje metadata v objektu BLOB. Jedna hodnota je nastavena pomocí `Add` metody kolekce. Druhá hodnota je nastavena pomocí implicitní syntaxe klíč/hodnota.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

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
---

Následující příklad kódu čte metadata v objektu BLOB.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Chcete-li načíst metadata, zavolejte metodu [GetProperties](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getproperties) nebo [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getpropertiesasync) objektu BLOB nebo Container pro naplnění kolekce [metadat](/dotnet/api/azure.storage.blobs.models.blobproperties.metadata) a pak hodnoty přečtěte, jak je znázorněno v následujícím příkladu. Metody **GetProperties** načítají vlastnosti objektu BLOB a metadata v jednom volání. To se liší od rozhraní REST API, která vyžadují samostatná volání pro [získání vlastností objektu BLOB](/rest/api/storageservices/get-blob-properties) a [získání metadat objektu BLOB](/rest/api/storageservices/get-blob-metadata).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Chcete-li načíst metadata, `FetchAttributes` zavolejte `FetchAttributesAsync` metodu or v objektu BLOB nebo kontejneru pro naplnění `Metadata` kolekce a pak hodnoty přečtěte, jak je znázorněno v následujícím příkladu.

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
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Viz také

- [Operace nastavení vlastností objektu BLOB](/rest/api/storageservices/set-blob-properties)
- [Operace získání vlastností objektu BLOB](/rest/api/storageservices/get-blob-properties)
- [Operace nastavení metadat objektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Operace získání metadat objektu BLOB](/rest/api/storageservices/get-blob-metadata)