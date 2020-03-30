---
title: Použití rozhraní .NET ke správě vlastností a metadat pro kontejner objektů blob
titleSuffix: Azure Storage
description: Zjistěte, jak nastavit a načíst vlastnosti systému a ukládat vlastní metadata do kontejnerů objektů blob v účtu Azure Storage pomocí knihovny klienta .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135917"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Správa vlastností kontejneru a metadat pomocí rozhraní .NET

Kontejnery objektů blob podporují vlastnosti systému a uživatelem definovaná metadata, kromě dat, která obsahují. Tento článek ukazuje, jak spravovat vlastnosti systému a uživatelem definovaná metadata pomocí [klientské knihovny Azure Storage pro rozhraní .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Vlastnosti a metadata

- **Vlastnosti systému**: Systémové vlastnosti existují na každém prostředku úložiště objektů Blob. Některé z nich lze číst nebo nastavit, zatímco jiné jsou jen pro čtení. Pod kryty některé vlastnosti systému odpovídají určitým standardním hlavičkám HTTP. Klientská knihovna Azure Storage pro rozhraní .NET tyto vlastnosti za vás udržuje.

- **Uživatelem definovaná metadata**: Uživatelem definovaná metadata se skládají z jednoho nebo více párů název-hodnota, které zadáte pro prostředek úložiště objektů Blob. Metadata můžete použít k uložení dalších hodnot s prostředkem. Hodnoty metadat jsou pouze pro vaše vlastní účely a nemají vliv na chování prostředku.

Načítání hodnot vlastností a metadat pro prostředek úložiště objektů blob je dvoustupňový proces. Než budete moci číst tyto hodnoty, musíte explicitně načíst voláním **FetchAttributes** nebo **FetchAttributesAsync** metoda. Výjimkou z tohoto pravidla je, že **exists** a **ExistsAsync** metody volat příslušné **FetchAttributes** metoda pod kryty. Při volání jedné z těchto metod není nutné volat také **FetchAttributes**.

> [!IMPORTANT]
> Pokud zjistíte, že hodnoty vlastnosti nebo metadat pro prostředek úložiště nebyly naplněny, zkontrolujte, zda váš kód volá metodu **FetchAttributes** nebo **FetchAttributesAsync.**

Dvojice názvů a hodnot metadat jsou platná hlavičky HTTP, a proto by měla dodržovat všechna omezení, kterými se řídí hlavičky HTTP. Názvy metadat musí být platné názvy hlaviček PROTOKOLU HTTP a platné identifikátory jazyka C#, mohou obsahovat pouze znaky ASCII a měly by být považovány za nerozlišující malá a velká písmena. Hodnoty metadat obsahující znaky, které nejsou znaky ASCII, by měly být kódovány jako Base64 nebo kódované adresou URL.

## <a name="retrieve-container-properties"></a>Načíst vlastnosti kontejneru

Chcete-li načíst vlastnosti kontejneru, zavolejte jednu z následujících metod:

- [Atributy Fetch](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

Následující příklad kódu načte systémové vlastnosti kontejneru a zapíše některé hodnoty vlastností do okna konzoly:

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

Metadata můžete zadat jako jeden nebo více párů název-hodnota na objektu blob nebo prostředek kontejneru. Chcete-li nastavit metadata, přidejte dvojice název-hodnota do kolekce **metadat** na prostředek a pak zavolejte jednu z následujících metod pro zápis hodnot:

- [Nastavit metadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Název metadat musí odpovídat konvencím pojmenování identifikátorů jazyka C#. Názvy metadat zachovávají případ, se kterým byly vytvořeny, ale při nastavení nebo čtení nerozlišují malá a velká písmena. Pokud jsou pro prostředek odeslána dvě nebo více záhlaví metadat se stejným názvem, vrátí úložiště objektů BLOB kód chyby HTTP 400 (Chybný požadavek).

Následující příklad kódu nastaví metadata v kontejneru. Jedna hodnota je nastavena pomocí metody **Add** kolekce. Druhá hodnota je nastavena pomocí implicitní syntaxe klíč/hodnota. Oba jsou platné.

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

Chcete-li načíst metadata, zavolejte metodu **FetchAttributes** nebo **FetchAttributesAsync** na objektblob nebo kontejner, abyste **naplnili kolekci metadat,** a pak si přečtěte hodnoty, jak je znázorněno v příkladu níže.

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

## <a name="see-also"></a>Viz také

- [Operace Získat vlastnosti kontejneru](/rest/api/storageservices/get-container-properties)
- [Nastavit operaci metadat kontejneru](/rest/api/storageservices/set-container-metadata)
- [Operace získání metadat kontejneru](/rest/api/storageservices/set-container-metadata)
