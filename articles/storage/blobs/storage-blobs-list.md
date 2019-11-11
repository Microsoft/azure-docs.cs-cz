---
title: Výpis objektů BLOB pomocí .NET-Azure Storage
description: Přečtěte si, jak vypsat objekty BLOB v kontejneru ve vašem Azure Storage účtu pomocí klientské knihovny .NET. Příklady kódu ukazují, jak zobrazit seznam objektů BLOB v nestrukturovaném seznamu nebo jak zobrazit seznam objektů BLOB hierarchicky, jako kdyby byly uspořádány do adresářů nebo složek.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/08/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4b6dc9d80cfe96e501e575d265b9fa383b1c4d2c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73902003"
---
# <a name="list-blobs-with-net"></a>Výpis objektů BLOB pomocí .NET

Při výpisu objektů BLOB z kódu můžete zadat několik možností pro správu způsobu, jakým jsou vráceny výsledky z Azure Storage. Můžete zadat počet výsledků, které se mají vrátit v každé sadě výsledků, a pak načíst následné sady. Můžete zadat předponu pro návrat objektů blob, jejichž názvy začínají daným znakem nebo řetězcem. A můžete vytvořit seznam objektů BLOB ve strukturách s plochým výpisem nebo hierarchicky. Hierarchický výpis vrátí objekty blob, jako kdyby byly uspořádány do složek. 

Tento článek ukazuje, jak zobrazit seznam objektů BLOB pomocí [klientské knihovny Azure Storage pro .NET](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-blob-listing-options"></a>Vysvětlení možností výpisu objektů BLOB

Pokud chcete zobrazit seznam objektů BLOB v účtu úložiště, zavolejte jednu z těchto metod:

- [CloudBlobClient. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Chcete-li zobrazit seznam objektů BLOB v kontejneru, zavolejte jednu z těchto metod:

- [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Přetížení těchto metod poskytují další možnosti pro správu způsobu, jakým operace výpisu vrací objekty blob. Tyto možnosti jsou popsány v následujících částech.

### <a name="manage-how-many-results-are-returned"></a>Spravujte, kolik výsledků se vrátí.

Ve výchozím nastavení vrací operace výpisu po dobu až 5000 výsledků. Chcete-li vrátit menší sadu výsledků, zadejte nenulovou hodnotu parametru `maxresults` při volání jedné z metod **ListBlobs** .

Pokud operace výpisu vrátí více než 5000 objektů blob, nebo pokud jste zadali hodnotu pro `maxresults` tak, že operace výpisu vrátí podmnožinu kontejnerů v účtu úložiště, pak Azure Storage vrátí *token pro pokračování* se seznamem objektů BLOB. Token pokračování je neprůhledná hodnota, kterou můžete použít k načtení další sady výsledků z Azure Storage.

V kódu zkontrolujte hodnotu tokenu pokračování a určete, zda má hodnotu null. Pokud má token pokračování hodnotu null, sada výsledků je dokončena. Pokud token pro pokračování není null, pak znovu spusťte operaci výpisu, která předá token pro pokračování pro načtení další sady výsledků, dokud token pro pokračování nemá hodnotu null.

### <a name="filter-results-with-a-prefix"></a>Filtrovat výsledky s předponou

Chcete-li filtrovat seznam kontejnerů, zadejte řetězec pro parametr `prefix`. Řetězec předpony může obsahovat jeden nebo více znaků. Azure Storage pak vrátí pouze objekty blob, jejichž názvy začínají touto předponou.

### <a name="return-metadata"></a>Návratová metadata

Pokud chcete vracet metadata objektu BLOB s výsledky, zadejte hodnotu **metadat** pro výčet [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) . Azure Storage zahrnuje metadata s vrácenými objekty blob, takže nemusíte v tomto kontextu volat jednu z metod **FetchAttributes** k načtení metadat objektu BLOB.

### <a name="flat-listing-versus-hierarchical-listing"></a>Plochý výpis versus hierarchický výpis

Objekty BLOB v Azure Storage jsou uspořádány do plochého paradigma, nikoli z hierarchického paradigmata (jako klasický systém souborů). Můžete ale uspořádat objekty blob do *virtuálních adresářů* , aby se napodobly struktuře složek. Virtuální adresář tvoří část názvu objektu BLOB a je označen znakem oddělovače.

K uspořádání objektů blob do virtuálních adresářů použijte v názvu objektu BLOB znak oddělovače. Výchozím znakem oddělovače je lomítko (/), ale můžete zadat libovolný znak jako oddělovač.

Pokud objekty blob pojmenujte pomocí oddělovače, můžete si vybrat možnost zobrazit seznam objektů BLOB hierarchicky. V případě operace hierarchického výpisu Azure Storage vrátí všechny virtuální adresáře a objekty blob pod nadřazeným objektem. Můžete volat operaci výpisu rekurzivně pro procházení hierarchie, podobně jako při programovém procházení klasického systému souborů.

## <a name="use-a-flat-listing"></a>Použít plochý seznam

Ve výchozím nastavení operace výpisu vrací objekty BLOB v nestrukturovaném seznamu. V nestrukturovaném seznamu nejsou objekty blob uspořádány pomocí virtuálního adresáře.

Následující příklad vypíše seznam objektů BLOB v zadaném kontejneru pomocí plochého seznamu, který má zadanou volitelnou velikost segmentu, a zapíše název objektu blob do okna konzoly.

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

Vzorový výstup je podobný následujícímu:

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

## <a name="use-a-hierarchical-listing"></a>Použití hierarchického výpisu

Když zavoláte operaci výpisu hierarchicky, Azure Storage vrátí virtuální adresáře a objekty blob na první úrovni hierarchie. Vlastnost [prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) každého virtuálního adresáře je nastavena tak, aby bylo možné předat předponu v rekurzivním volání a načíst další adresář.

Chcete-li zobrazit seznam objektů BLOB hierarchicky, nastavte parametr `useFlatBlobListing` metody výpisu na **hodnotu false**.

Následující příklad vypíše seznam objektů BLOB v zadaném kontejneru pomocí plochého seznamu, který má zadanou volitelnou velikost segmentu, a zapíše název objektu blob do okna konzoly.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and 
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, null, null, null);
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

Vzorový výstup je podobný následujícímu:

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
> Snímky objektů BLOB nelze uvést v hierarchické operaci výpisu.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Další kroky

- [Výpis objektů BLOB](/rest/api/storageservices/list-blobs)
- [Vytváření výčtu prostředků objektů BLOB](/rest/api/storageservices/enumerating-blob-resources)
