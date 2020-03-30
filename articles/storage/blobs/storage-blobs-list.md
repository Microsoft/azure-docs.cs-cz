---
title: Seznam objektů BLOB s rozhraním .NET – Azure Storage
description: Zjistěte, jak vypsat objekty BLOB v kontejneru v účtu Azure Storage pomocí knihovny klienta .NET. Příklady kódu ukazují, jak vypsat objekty BLOB v plochém výpisu nebo jak hierarchicky vypsat objekty BLOB, jako by byly uspořádány do adresářů nebo složek.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137916"
---
# <a name="list-blobs-with-net"></a>Seznam objektů BLOB s rozhraním .NET

Když vypíšete objekty BLOB z vašeho kódu, můžete zadat řadu možností, které vám pomůžou spravovat způsob vrácení výsledků z Azure Storage. Můžete zadat počet výsledků, které se mají vrátit v každé sadě výsledků, a potom načíst následující sady. Můžete zadat předponu pro vrácení objektů BLOB, jejichž názvy začínají tímto znakem nebo řetězcem. A můžete seznam objektů BLOB ve struktuře plochý výpis nebo hierarchicky. Hierarchický výpis vrací objekty BLOB, jako by byly uspořádány do složek. 

Tento článek ukazuje, jak seznam objektů BLOB pomocí [knihovny klienta Azure Storage pro .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-blob-listing-options"></a>Principy možností výpisu objektu blob

Chcete-li vypsat objekty BLOB v účtu úložiště, zavolejte jednu z těchto metod:

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Chcete-li vypsat objekty BLOB v kontejneru, zavolejte jednu z těchto metod:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Přetížení pro tyto metody poskytují další možnosti pro správu, jak objekty BLOB jsou vráceny operace výpisu. Tyto možnosti jsou popsány v následujících částech.

### <a name="manage-how-many-results-are-returned"></a>Správa počtu vrácených výsledků

Ve výchozím nastavení vrátí operace výpisu až 5000 výsledků najednou. Chcete-li vrátit menší sadu výsledků, zadejte `maxresults` nenulovou hodnotu parametru při volání jedné z metod **ListBlobs.**

Pokud výpis operace vrátí více než 5000 objektů BLOB, `maxresults` nebo pokud jste zadali hodnotu pro takové, že výpis operace vrátí podmnožinu kontejnerů v účtu úložiště, pak Azure Storage vrátí *token pokračování* se seznamem objektů BLOB. Token pokračování je neprůhledná hodnota, kterou můžete použít k načtení další sady výsledků z Azure Storage.

V kódu zkontrolujte hodnotu tokenu pokračování k určení, zda je null. Pokud je token pokračování null, je dokončena sada výsledků. Pokud token pokračování není null, pak volání operace výpis znovu, předání v tokenu pokračování načíst další sadu výsledků, dokud token pokračování je null.

### <a name="filter-results-with-a-prefix"></a>Filtrování výsledků pomocí předpony

Chcete-li filtrovat seznam kontejnerů, `prefix` zadejte řetězec pro parametr. Řetězec předpony může obsahovat jeden nebo více znaků. Azure Storage pak vrátí pouze objekty BLOB, jejichž názvy začínají s tímto předponou.

### <a name="return-metadata"></a>Vrátit metadata

Chcete-li vrátit metadata objektu blob s výsledky, zadejte hodnotu **Metadata** pro výčet [BlobListingDetails.](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) Azure Storage obsahuje metadata s každým vráceným objektem blob, takže v tomto kontextu nemusíte volat jednu z metod **FetchAttributes,** abyste načetli metadata objektu blob.

### <a name="flat-listing-versus-hierarchical-listing"></a>Plochý výpis versus hierarchický výpis

Objekty BLOB ve službě Azure Storage jsou uspořádané v plochém paradigmatu, nikoli v hierarchickém paradigmatu (jako je klasický systém souborů). Objekty BLOB však můžete uspořádat do *virtuálních adresářů,* abyste napodobili strukturu složek. Virtuální adresář je součástí názvu objektu blob a je označen znakem oddělovače.

Chcete-li objekty BLOB uspořádat do virtuálních adresářů, použijte znak oddělovače v názvu objektu blob. Výchozí znak oddělovače je lomítko (/), ale jako oddělovač můžete zadat libovolný znak.

Pokud objekty BLOB pojmenujete pomocí oddělovače, můžete seznam objektů BLOB hierarchicky. Pro operaci hierarchického výpisu Azure Storage vrátí všechny virtuální adresáře a objekty BLOB pod nadřazeným objektem. Můžete volat výpis operace rekurzivně procházet hierarchii, podobně jako by procházet klasický systém souborů programově.

## <a name="use-a-flat-listing"></a>Použití plochého výpisu

Ve výchozím nastavení vrátí operace výpisu objekty BLOB v plochém výpisu. V plochém výpisu nejsou objekty BLOB uspořádány podle virtuálního adresáře.

Následující příklad uvádí objekty BLOB v zadaném kontejneru pomocí plochého výpisu s volitelnou velikostí segmentu a zapíše název objektu blob do okna konzoly.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

Výstup vzorku je podobný:

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Použití hierarchického zápisu

Když voláte výpis operace hierarchicky, Azure Storage vrátí virtuální adresáře a objekty BLOB na první úrovni hierarchie. Vlastnost [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) každého virtuálního adresáře je nastavena tak, abyste mohli předat předponu v rekurzivním volání a načíst další adresář.

Chcete-li seznam objektů BLOB `useFlatBlobListing` hierarchicky, nastavte parametr metody výpisu na **hodnotu false**.

Následující příklad uvádí objekty BLOB v zadaném kontejneru pomocí plochého výpisu s volitelnou velikostí segmentu a zapíše název objektu blob do okna konzoly.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

Výstup vzorku je podobný:

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Snímky objektů blob nelze uvést v operaci hierarchického výpisu.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Další kroky

- [Seznam objektů Blob](/rest/api/storageservices/list-blobs)
- [Výčet prostředků objektů Blob](/rest/api/storageservices/enumerating-blob-resources)
