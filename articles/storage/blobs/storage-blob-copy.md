---
title: Kopírování objektu blob pomocí rozhraní .NET – Azure Storage
description: Zjistěte, jak zkopírovat objekt blob v účtu Azure Storage pomocí knihovny klienta .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135883"
---
# <a name="copy-a-blob-with-net"></a>Kopírování objektu blob pomocí rozhraní .NET

Tento článek ukazuje, jak zkopírovat objekt blob s účtem Azure Storage. Také ukazuje, jak přerušit operaci asynchronní kopie. Ukázkový kód používá [klientskou knihovnu Azure Storage pro rozhraní .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-copying-blobs"></a>Kopírování objektů BLOB

Když zkopírujete objekt blob v rámci stejného účtu úložiště, jedná se o synchronní operaci. Při kopírování přes účty je asynchronní operace. Metody [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) a [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) vrátí hodnotu ID kopie, která se používá ke kontrole stavu nebo k přerušení operace kopírování.

Zdrojový objekt blob pro operaci kopírování může být objekt blob bloku, objekt blob připojení, objekt blob stránky nebo snímek. Pokud cílový objekt blob již existuje, musí být stejného typu objektu blob jako zdrojový objekt blob. Všechny existující cílové objektblob budou přepsány. 

Cílový objekt blob nelze změnit, zatímco probíhá operace kopírování. Cílový objekt blob může mít pouze jednu nevyřízené operace objektu blob kopírování. Jinými slovy objekt blob nemůže být cílem pro více čekajících operací kopírování.

Celý zdrojový objekt blob nebo soubor je vždy zkopírován. Kopírování rozsahu bajtů nebo sady bloků není podporováno.

Při kopírování objektu blob jsou systémové vlastnosti zkopírovány do cílového objektu blob se stejnými hodnotami.

Pro všechny typy objektů blob můžete zkontrolovat [Vlastnost CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) na cílovém objektu blob, abyste získali stav operace kopírování. Konečný objekt blob bude potvrzena po dokončení kopírování.

Operace kopírování může mít některou z následujících forem:

  - Zdrojový objekt blob můžete zkopírovat do cílového objektu blob s jiným názvem. Cílový objekt blob může být existující objekt blob stejného typu objektu blob (blok, připojit nebo stránku) nebo může být nový objekt blob vytvořený operací kopírování.
  - Zdrojový objekt blob můžete zkopírovat do cílového objektu blob se stejným názvem, který efektivně nahradí cílový objekt blob. Taková operace kopírování odebere všechny nepotvrzené bloky a přepíše metadata cílového objektu blob.
  - Zdrojový soubor ve službě Azure File můžete zkopírovat do cílového objektu blob. Cílový objekt blob může být existující objekt blob bloku nebo může být nový objekt blob bloku vytvořený operací kopírování. Kopírování ze souborů na objekty BLOB stránky nebo objekty BLOB není podporováno.
  - Snímek můžete zkopírovat přes jeho základní objekt blob. Povýšením snímku na pozici základního objektu blob můžete obnovit starší verzi objektu blob.
  - Snímek můžete zkopírovat do cílového objektu blob s jiným názvem. Výsledný cílový objekt blob je zapisovatelný objekt blob a není snímek.

## <a name="copy-a-blob"></a>Kopírování objektu blob

Chcete-li zkopírovat objekt blob, zavolejte jednu z následujících metod:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

Následující příklad kódu získá odkaz na objekt blob vytvořený dříve a zkopíruje jej do nového objektu blob ve stejném kontejneru:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>Přerušení operace kopírování objektu blob

Přerušení operace kopírování má za následek cílový objekt blob nulové délky pro objekty BLOB bloku, objekty BLOB připojit a objekty BLOB stránky. Metadata pro cílový objekt blob však budou mít nové hodnoty zkopírované ze zdrojového objektu blob nebo explicitně nastavené ve volání [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) nebo [StartCopyAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) Chcete-li zachovat původní metadata před kopií, vytvořte snímek `StartCopy` `StartCopyAsync`cílového objektu blob před voláním nebo .

Při přerušení probíhající operace kopírování objektů blob je cílový objekt blob [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) nastaven na [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

Metody [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) a [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) zruší probíhající operaci kopírování objektů blob a ponechají cílový objekt blob s nulovou délkou a úplnými metadaty.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Další kroky

Následující témata obsahují informace o kopírování objektů BLOB a přerušení probíhajících operací kopírování pomocí azure rest API.

- [Zkopírování objektu blob](/rest/api/storageservices/copy-blob)
- [Přerušit kopírování objektů blob](/rest/api/storageservices/abort-copy-blob)
