---
title: Správa vlastností a metadat pro kontejner objektů BLOB pomocí .NET-Azure Storage
description: Naučte se, jak pomocí klientské knihovny .NET nastavit a načíst systémové vlastnosti a ukládat do Azure Storage kontejnerů objektů BLOB vlastní metadata.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: tamram
ms.openlocfilehash: d63c78fedb8dbd48655d36fecc3544fd512072e3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673375"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Správa vlastností kontejneru a metadat pomocí .NET

Kontejnery objektů BLOB podporují systémové vlastnosti a uživatelsky definovaná metadata kromě dat, která obsahují. V tomto článku se dozvíte, jak spravovat vlastnosti systému a uživatelsky definovaná metadata pomocí [Azure Storage klientské knihovny pro .NET](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>O vlastnostech a metadatech

- **Vlastnosti systému**: V každém prostředku úložiště objektů BLOB existují systémové vlastnosti. Některé z nich je možné číst nebo nastavovat, zatímco jiné jsou jen pro čtení. V rámci pokrývá některé vlastnosti systému odpovídají určitým standardním hlavičkám HTTP. Klientská knihovna Azure Storage pro .NET uchovává tyto vlastnosti za vás.

- **Uživatelsky definovaná metadata**: Uživatelsky definovaná metadata se skládají z jedné nebo více párů název-hodnota, které zadáte pro prostředek úložiště objektů BLOB. Metadata můžete použít k ukládání dalších hodnot s prostředkem. Hodnoty metadat jsou pouze pro vaše vlastní účely a neovlivňují způsob, jakým se prostředek chová.

Načítají se hodnoty vlastností a metadat pro prostředek BLOB Storage je proces se dvěma kroky. Než budete moci číst tyto hodnoty, je nutné je explicitně načíst voláním metody **FetchAttributes** nebo **FetchAttributesAsync** . Výjimkou z tohoto pravidla je, že metody **Exists** a **ExistsAsync** volají odpovídající metodu **FetchAttributes** v rámci pokrývání. Při volání jedné z těchto metod není nutné volat také **FetchAttributes**.

> [!IMPORTANT]
> Pokud zjistíte, že vlastnost nebo hodnoty metadat pro prostředek úložiště nebyly naplněny, zkontrolujte, zda váš kód volá metodu **FetchAttributes** nebo **FetchAttributesAsync** .

Páry název-hodnota metadat jsou platné hlavičky protokolu HTTP, takže by měly splňovat všechna omezení řízení hlaviček protokolu HTTP. Názvy metadat musí být platné názvy hlaviček protokolu HTTP a C# platné identifikátory, může obsahovat pouze znaky ASCII a měly by se považovat za nerozlišování velkých a malých písmen. Hodnoty metadat obsahující znaky jiné než ASCII by měly být zakódované v kódování Base64 nebo v kódování URL.

## <a name="retrieve-container-properties"></a>Načíst vlastnosti kontejneru

Chcete-li načíst vlastnosti kontejneru, zavolejte jednu z následujících metod:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

Následující příklad kódu načte vlastnosti systému kontejneru a zapisuje některé hodnoty vlastností do okna konzoly:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

Metadata můžete zadat jako jednu nebo více párů název-hodnota u prostředku BLOB nebo kontejneru. Chcete-li nastavit metadata, přidejte páry název-hodnota do kolekce **metadat** v prostředku a pak zavolejte jednu z následujících metod pro zápis hodnot:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Název vašich metadat musí odpovídat konvencím pojmenování C# identifikátorů. Názvy metadat zachovávají případ, se kterým byly vytvořeny, ale při nastavení nebo čtení se nerozlišují malá a velká písmena. Pokud se pro prostředek odešlou dvě nebo víc hlaviček metadat se stejným názvem, úložiště objektů BLOB vrátí kód chyby HTTP 400 (chybný požadavek).

Následující příklad kódu nastaví metadata na kontejneru. Jedna hodnota je nastavena pomocí metody **Add** kolekce. Druhá hodnota je nastavena pomocí implicitní syntaxe klíč/hodnota. Obě jsou platné.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

Chcete-li načíst metadata, zavolejte metodu **FetchAttributes** nebo **FetchAttributesAsync** v objektu BLOB nebo kontejneru pro naplnění kolekce **metadat** a pak hodnoty přečtěte, jak je znázorněno v následujícím příkladu.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
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

- [Získat operaci vlastností kontejneru](/rest/api/storageservices/get-container-properties)
- [Operace nastavení metadat kontejneru](/rest/api/storageservices/set-container-metadata)
- [Získat operaci metadat kontejneru](/rest/api/storageservices/set-container-metadata)
