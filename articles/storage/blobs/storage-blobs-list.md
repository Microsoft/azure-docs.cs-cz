---
title: Výpis objektů BLOB pomocí rozhraní Azure Storage API
description: Naučte se vypsat objekty blob ve svém účtu úložiště pomocí klientských knihoven Azure Storage. Příklady kódu ukazují, jak zobrazit seznam objektů BLOB v nestrukturovaném seznamu nebo jak zobrazit seznam objektů BLOB hierarchicky, jako kdyby byly uspořádány do adresářů nebo složek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/24/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ff20b8bd0aab94cadadddbb7a4b7b32b1db1ee85
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046938"
---
# <a name="list-blobs-with-azure-storage-client-libraries"></a>Výpis objektů BLOB pomocí Azure Storage klientských knihoven

Při výpisu objektů BLOB z kódu můžete zadat několik možností pro správu způsobu, jakým jsou vráceny výsledky z Azure Storage. Můžete zadat počet výsledků, které se mají vrátit v každé sadě výsledků, a pak načíst následné sady. Můžete zadat předponu pro návrat objektů blob, jejichž názvy začínají daným znakem nebo řetězcem. A můžete vytvořit seznam objektů BLOB ve strukturách s plochým výpisem nebo hierarchicky. Hierarchický výpis vrátí objekty blob, jako kdyby byly uspořádány do složek.

## <a name="understand-blob-listing-options"></a>Vysvětlení možností výpisu objektů BLOB

Pokud chcete zobrazit seznam objektů BLOB v účtu úložiště, zavolejte jednu z těchto metod:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [BlobContainerClient. getblobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs)
- [BlobContainerClient.GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync)
- [BlobContainerClient.GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)
- [BlobContainerClient.GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [CloudBlobClient. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Chcete-li zobrazit seznam objektů BLOB v kontejneru, zavolejte jednu z těchto metod:

- [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

# <a name="python-v12"></a>[Python V12](#tab/python)

- [ContainerClient.list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-)

---

### <a name="manage-how-many-results-are-returned"></a>Spravujte, kolik výsledků se vrátí.

Ve výchozím nastavení vrací operace výpisu po dobu až 5000 výsledků, ale můžete zadat počet výsledků, které mají vracet jednotlivé operace výpisu. Příklady uvedené v tomto článku ukazují, jak vracet výsledky na stránkách.

### <a name="filter-results-with-a-prefix"></a>Filtrovat výsledky s předponou

Chcete-li filtrovat seznam objektů blob, zadejte řetězec pro `prefix` parametr. Řetězec předpony může obsahovat jeden nebo více znaků. Azure Storage pak vrátí pouze objekty blob, jejichž názvy začínají touto předponou.

### <a name="return-metadata"></a>Návratová metadata

Můžete vracet metadata objektu BLOB s výsledky.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Zadejte hodnotu **metadat** pro výčet [BlobTraits](/dotnet/api/azure.storage.blobs.models.blobtraits) .

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Zadejte hodnotu **metadat** pro výčet [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) . Azure Storage zahrnuje metadata s vrácenými objekty blob, takže nemusíte v tomto kontextu volat jednu z metod **FetchAttributes** k načtení metadat objektu BLOB.

# <a name="python-v12"></a>[Python V12](#tab/python)

Zadejte `metadata` pro `include=` parametr při volání [list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-).

---

### <a name="list-blob-versions-or-snapshots"></a>Vypsat verze nebo snímky objektů BLOB

- Chcete-li vypsat verze nebo snímky objektů BLOB pomocí klientské knihovny .NET V12, zadejte parametr [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) s polem **verze** nebo **snímku** . Verze a snímky jsou uvedeny z nejstarší na nejnovější. Další informace o výpisu verzí najdete v tématu [seznam verzí objektů BLOB](versioning-enable.md#list-blob-versions).

- Chcete-li vypsat počet snímků pomocí klientské knihovny Python V12, zadejte `num_snapshots` v `include=` parametru při volání [list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-).

### <a name="flat-listing-versus-hierarchical-listing"></a>Plochý výpis versus hierarchický výpis

Objekty BLOB v Azure Storage jsou uspořádány do plochého paradigma, nikoli z hierarchického paradigmata (jako klasický systém souborů). Můžete ale uspořádat objekty blob do *virtuálních adresářů* , aby se napodobly struktuře složek. Virtuální adresář tvoří část názvu objektu BLOB a je označen znakem oddělovače.

K uspořádání objektů blob do virtuálních adresářů použijte v názvu objektu BLOB znak oddělovače. Výchozím znakem oddělovače je lomítko (/), ale můžete zadat libovolný znak jako oddělovač.

Pokud objekty blob pojmenujte pomocí oddělovače, můžete si vybrat možnost zobrazit seznam objektů BLOB hierarchicky. V případě operace hierarchického výpisu Azure Storage vrátí všechny virtuální adresáře a objekty blob pod nadřazeným objektem. Můžete volat operaci výpisu rekurzivně pro procházení hierarchie, podobně jako při programovém procházení klasického systému souborů.

## <a name="use-a-flat-listing"></a>Použít plochý seznam

Ve výchozím nastavení operace výpisu vrací objekty BLOB v nestrukturovaném seznamu. V nestrukturovaném seznamu nejsou objekty blob uspořádány pomocí virtuálního adresáře.

Následující příklad vypíše seznam objektů BLOB v zadaném kontejneru pomocí plochého seznamu, který má zadanou volitelnou velikost segmentu, a zapíše název objektu blob do okna konzoly.

Pokud jste na svém účtu povolili funkci hierarchického oboru názvů, adresáře nejsou virtuální. Místo toho se jedná o konkrétní, nezávislé objekty. Proto se adresáře zobrazí v seznamu jako objekty BLOB s nulovou délkou.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsFlatListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Pokud operace výpisu vrátí více než 5000 objektů BLOB nebo pokud počet dostupných objektů BLOB překračuje zadaný počet, pak Azure Storage vrátí *token pro pokračování* se seznamem objektů BLOB. Token pokračování je neprůhledná hodnota, kterou můžete použít k načtení další sady výsledků z Azure Storage.

V kódu zkontrolujte hodnotu tokenu pokračování a určete, zda má hodnotu null. Pokud má token pokračování hodnotu null, sada výsledků je dokončena. Pokud token pro pokračování není null, pak znovu spusťte operaci výpisu, která předá token pro pokračování pro načtení další sady výsledků, dokud token pro pokračování nemá hodnotu null.

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

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/list_blobs.py" id="Snippet_ListBlobs":::

---

Vzorový výstup je podobný následujícímu:

```console
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

Když zavoláte operaci výpisu hierarchicky, Azure Storage vrátí virtuální adresáře a objekty blob na první úrovni hierarchie.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Chcete-li zobrazit seznam objektů BLOB hierarchicky, zavolejte metodu [BlobContainerClient. GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)nebo [BlobContainerClient. GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync) .

Následující příklad vypíše seznam objektů BLOB v zadaném kontejneru pomocí hierarchického výpisu, který má zadanou volitelnou velikost segmentu, a zapíše název objektu blob do okna konzoly.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsHierarchicalListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Vlastnost [prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) každého virtuálního adresáře je nastavena tak, aby bylo možné předat předponu v rekurzivním volání a načíst další adresář.

Chcete-li zobrazit seznam objektů BLOB hierarchicky, nastavte `useFlatBlobListing` parametr metody výpisu na **false**.

Následující příklad vypíše seznam objektů BLOB v zadaném kontejneru pomocí plochého seznamu, který má zadanou volitelnou velikost segmentu, a zapíše název objektu blob do okna konzoly.

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

# <a name="python-v12"></a>[Python V12](#tab/python)

Chcete-li zobrazit seznam objektů BLOB hierarchicky, zavolejte metodu [walk_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#walk-blobs-name-starts-with-none--include-none--delimiter--------kwargs-) .

Následující příklad vypíše seznam objektů BLOB v zadaném kontejneru pomocí hierarchického výpisu, který má zadanou volitelnou velikost segmentu, a zapíše název objektu blob do okna konzoly.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/list_blobs.py" id="Snippet_WalkHierarchy":::

---

Vzorový výstup je podobný následujícímu:

```console
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