---
title: Seznam kontejnerů objektů blob s rozhraním .NET – Azure Storage
description: Zjistěte, jak vypsat kontejnery objektů blob ve vašem účtu Azure Storage pomocí knihovny klienta .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 155b8f5d50c7b106daff8dab4df17200b844c988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135900"
---
# <a name="list-blob-containers-with-net"></a>Seznam kontejnerů objektů blob s rozhraním .NET

Když zobrazíte seznam kontejnerů v účtu Azure Storage z vašeho kódu, můžete zadat řadu možností, jak spravovat, jak se vrátí výsledky z Azure Storage. Tento článek ukazuje, jak seznam kontejnerů pomocí [knihovny klienta Azure Storage pro .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-container-listing-options"></a>Principy možností výpisu kontejneru

Chcete-li v účtu úložiště uvést kontejnery, zavolejte jednu z následujících metod:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Přetížení pro tyto metody poskytují další možnosti pro správu, jak kontejnery jsou vráceny operace výpisu. Tyto možnosti jsou popsány v následujících částech.

### <a name="manage-how-many-results-are-returned"></a>Správa počtu vrácených výsledků

Ve výchozím nastavení vrátí operace výpisu až 5000 výsledků najednou. Chcete-li vrátit menší sadu výsledků, zadejte `maxresults` nenulovou hodnotu parametru při volání jedné z metod **ListContainerSegmented.**

Pokud váš účet úložiště obsahuje více než 5000 kontejnerů `maxresults` nebo pokud jste zadali hodnotu pro takové, že výpis operace vrátí podmnožinu kontejnerů v účtu úložiště, pak Azure Storage vrátí *token pokračování* se seznamem kontejnerů. Token pokračování je neprůhledná hodnota, kterou můžete použít k načtení další sady výsledků z Azure Storage.

V kódu zkontrolujte hodnotu tokenu pokračování k určení, zda je null. Pokud je token pokračování null, je dokončena sada výsledků. Pokud token pokračování není null, pak volání **ListContainersSegmented** nebo **ListContainersSegmentedAsync** znovu, předání v tokenu pokračování načíst další sadu výsledků, dokud token pokračování je null.

### <a name="filter-results-with-a-prefix"></a>Filtrování výsledků pomocí předpony

Chcete-li filtrovat seznam kontejnerů, `prefix` zadejte řetězec pro parametr. Řetězec předpony může obsahovat jeden nebo více znaků. Azure Storage pak vrátí pouze kontejnery, jejichž názvy začínají s tímto předponou.

### <a name="return-metadata"></a>Vrátit metadata

Chcete-li vrátit metadata kontejneru s výsledky, zadejte hodnotu **Metadata** pro výčet [ContainerListingDetails.](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) Azure Storage obsahuje metadata s každým vráceným kontejnerem, takže nemusíte volat také jednu z metod **FetchAttributes** k načtení metadat kontejneru.

## <a name="example-list-containers"></a>Příklad: Seznam kontejnerů

Následující příklad asynchronně uvádí kontejnery v účtu úložiště, které začínají zadanou předponou. Příklad uvádí kontejnery v přírůstcích 5 výsledků najednou a používá token pokračování získat další segment výsledků. Příklad také vrátí metadata kontejneru s výsledky.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

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

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Viz také

[Seznam kontejnerů](/rest/api/storageservices/list-containers2)
[výčtu prostředků objektu Blob](/rest/api/storageservices/enumerating-blob-resources)
