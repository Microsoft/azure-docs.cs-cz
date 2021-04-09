---
title: Výpis kontejnerů objektů BLOB pomocí Azure Storage .NET
description: Přečtěte si, jak zobrazit seznam kontejnerů objektů BLOB v účtu Azure Storage pomocí klientské knihovny .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a12fc991734fe74e450aa14a477f3a4500ba659c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96937249"
---
# <a name="list-blob-containers-with-net"></a>Výpis kontejnerů objektů BLOB pomocí .NET

Když vytvoříte seznam kontejnerů v účtu Azure Storage z kódu, můžete zadat několik možností pro správu, jak se výsledky vrátí z Azure Storage. Tento článek ukazuje, jak zobrazit seznam kontejnerů pomocí [klientské knihovny Azure Storage pro .NET](/dotnet/api/overview/azure/storage).  

## <a name="understand-container-listing-options"></a>Principy možností výpisu kontejneru

Pokud chcete zobrazit seznam kontejnerů ve vašem účtu úložiště, zavolejte jednu z následujících metod:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

Přetížení pro tyto metody poskytují další možnosti pro správu způsobu, jakým operace výpisu vrací kontejnery. Tyto možnosti jsou popsány v následujících částech.

### <a name="manage-how-many-results-are-returned"></a>Spravujte, kolik výsledků se vrátí.

Ve výchozím nastavení vrací operace výpisu po dobu až 5000 výsledků. Chcete-li vrátit menší sadu výsledků, zadejte nenulovou hodnotu pro velikost stránky výsledků, která se má vrátit.

Pokud váš účet úložiště obsahuje více než 5000 kontejnerů, nebo pokud jste zadali velikost stránky tak, že operace výpisu vrátí podmnožinu kontejnerů v účtu úložiště, pak Azure Storage vrátí *token pro pokračování* se seznamem kontejnerů. Token pokračování je neprůhledná hodnota, kterou můžete použít k načtení další sady výsledků z Azure Storage.

Ve vašem kódu zkontrolujte hodnotu tokenu pro pokračování, abyste zjistili, jestli je prázdný (pro .NET V12) nebo null (pro .NET V11 a starší). Pokud má token pokračování hodnotu null, sada výsledků je dokončena. Pokud token pro pokračování není null, zavolejte metodu výpisu znovu a předejte do tokenu pokračování, aby se načetla další sada výsledků, dokud token pro pokračování nemá hodnotu null.

### <a name="filter-results-with-a-prefix"></a>Filtrovat výsledky s předponou

Chcete-li filtrovat seznam kontejnerů, zadejte řetězec pro `prefix` parametr. Řetězec předpony může obsahovat jeden nebo více znaků. Azure Storage pak vrátí pouze kontejnery, jejichž názvy začínají předponou.

### <a name="return-metadata"></a>Návratová metadata

Chcete-li vrátit metadata kontejneru s výsledky, zadejte hodnotu **metadat** pro výčet [BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) (pro .NET V12) nebo výčet [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) (pro .NET V11 a starší). Azure Storage zahrnuje metadata s každým vráceným kontejnerem, takže nemusíte také načítat metadata kontejneru.

## <a name="example-list-containers"></a>Příklad: Seznam kontejnerů

Následující příklad asynchronně vypíše kontejnery v účtu úložiště, který začíná zadanou předponou. Příklad zobrazí seznam kontejnerů, které začínají zadanou předponou a vrátí zadaný počet výsledků na volání operace výpisu. Potom pomocí tokenu pro pokračování získá další segment výsledků. Příklad také vrátí metadata kontejneru s výsledky.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="Snippet_ListContainers":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Viz také

- [Seznam kontejnerů](/rest/api/storageservices/list-containers2)
- [Vytváření výčtu prostředků objektů BLOB](/rest/api/storageservices/enumerating-blob-resources)
