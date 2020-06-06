---
title: Kopírování objektu BLOB pomocí .NET-Azure Storage
description: Naučte se, jak kopírovat objekt blob do účtu Azure Storage pomocí klientské knihovny .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: f4e6e2f2732d1c90e8fe669788d82692c8016fd6
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2020
ms.locfileid: "84463446"
---
# <a name="copy-a-blob-with-net"></a>Kopírování objektu BLOB s využitím .NET

Tento článek ukazuje, jak kopírovat objekt BLOB s účtem Azure Storage. Také ukazuje, jak přerušit asynchronní operaci kopírování. Vzorový kód používá [klientskou knihovnu Azure Storage pro .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-copying-blobs"></a>O kopírování objektů BLOB

Když kopírujete objekt BLOB v rámci stejného účtu úložiště, jedná se o synchronní operaci. Při kopírování mezi účty se jedná o asynchronní operaci. Metody [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) a [STARTCOPYASYNC](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) vracejí hodnotu ID kopie, která se používá ke kontrole stavu nebo přerušení operace kopírování.

Zdrojový objekt BLOB pro operaci kopírování může být objekt blob bloku, doplňovací objekt blob, objekt blob stránky nebo snímek. Pokud cílový objekt BLOB už existuje, musí se jednat o stejný typ objektu BLOB jako zdrojový objekt BLOB. Veškerý existující cílový objekt BLOB bude přepsán. 

Cílový objekt BLOB se nedá změnit, když probíhá operace kopírování. Cílový objekt BLOB může mít jenom jednu nevyřízenou operaci kopírování objektů BLOB. Jinými slovy, objekt BLOB nemůže být cílem pro více operací kopírování, které čekají na zpracování.

Celý zdrojový objekt BLOB nebo soubor se vždycky kopíruje. Kopírování rozsahu bajtů nebo sady bloků se nepodporuje.

Po zkopírování objektu BLOB se do cílového objektu BLOB zkopírují systémové vlastnosti se stejnými hodnotami.

U všech typů objektů blob můžete v cílovém objektu BLOB ověřit vlastnost [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) a získat tak stav operace kopírování. Konečný objekt BLOB bude potvrzen po dokončení kopírování.

Operace kopírování může mít některou z následujících forem:

  - Zdrojový objekt blob můžete zkopírovat do cílového objektu BLOB s jiným názvem. Cílový objekt BLOB může být existující objekt BLOB stejného typu objektu BLOB (blok, připojit nebo stránka), nebo může být nový objekt BLOB vytvořený operací kopírování.
  - Zdrojový objekt blob můžete zkopírovat do cílového objektu BLOB se stejným názvem a efektivně nahradit cílový objekt BLOB. Taková operace kopírování odstraní všechny nepotvrzené bloky a přepíše metadata cílového objektu BLOB.
  - Zdrojový soubor v Souborové službě Azure můžete zkopírovat do cílového objektu BLOB. Cílový objekt BLOB může být existující objekt blob bloku nebo může být nový objekt blob bloku vytvořený operací kopírování. Kopírování ze souborů do objektů blob stránky nebo doplňovací objekty BLOB se nepodporuje.
  - Snímek můžete zkopírovat přes svůj základní objekt BLOB. Zvýšením úrovně snímku na pozici základního objektu blob můžete obnovit předchozí verzi objektu BLOB.
  - Snímek můžete zkopírovat do cílového objektu BLOB s jiným názvem. Výsledný cílový objekt BLOB je zapisovatelný objekt blob, který není snímkem.

## <a name="copy-a-blob"></a>Kopírování objektu BLOB

Chcete-li kopírovat objekt blob, zavolejte jednu z následujících metod:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

Následující příklad kódu získá odkaz na objekt blob, který jste vytvořili dříve, a zkopíruje ho do nového objektu BLOB ve stejném kontejneru:

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

## <a name="abort-a-blob-copy-operation"></a>Přerušení operace kopírování objektu BLOB

Přerušení operace kopírování má za následek cílový objekt BLOB s nulovou délkou pro objekty blob bloku, doplňovací objekty BLOB a objekty blob stránky. Metadata pro cílový objekt BLOB ale budou mít nové hodnoty zkopírované ze zdrojového objektu BLOB nebo explicitně nastavené ve volání [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) nebo [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) . Chcete-li zachovat původní metadata před kopírováním, vytvořte snímek cílového objektu BLOB před voláním `StartCopy` nebo `StartCopyAsync` .

Při přerušení probíhající operace kopírování objektů BLOB je cílový objekt BLOB [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) nastavený na [CopyStatus. Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

Metody [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) a [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) zruší probíhající operaci kopírování objektů BLOB a ponechají cílový objekt BLOB s nulovou délkou a úplnými metadaty.

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

Následující témata obsahují informace o kopírování objektů BLOB a přerušování probíhajících operací kopírování pomocí rozhraní Azure REST API.

- [Zkopírování objektu blob](/rest/api/storageservices/copy-blob)
- [Přerušit kopii objektu BLOB](/rest/api/storageservices/abort-copy-blob)
