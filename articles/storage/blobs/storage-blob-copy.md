---
title: Kopírování objektu BLOB s rozhraními API Azure Storage
description: Naučte se Kopírovat objekt BLOB pomocí klientských knihoven Azure Storage.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/10/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 2c474ed4d4158356075f861c3c0d5ace69173255
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90014646"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>Kopírování objektu BLOB s Azure Storage klientskými knihovnami

Tento článek ukazuje, jak kopírovat objekt blob do účtu Azure Storage. Také ukazuje, jak přerušit asynchronní operaci kopírování. Vzorový kód používá klientské knihovny Azure Storage.

## <a name="about-copying-blobs"></a>O kopírování objektů BLOB

Když kopírujete objekt BLOB v rámci stejného účtu úložiště, jedná se o synchronní operaci. Při kopírování mezi účty se jedná o asynchronní operaci.

Zdrojový objekt BLOB pro operaci kopírování může být objekt blob bloku, doplňovací objekt blob, objekt blob stránky nebo snímek. Pokud cílový objekt BLOB už existuje, musí se jednat o stejný typ objektu BLOB jako zdrojový objekt BLOB. Existující cílový objekt BLOB bude přepsán.

Cílový objekt BLOB se nedá změnit, když probíhá operace kopírování. Cílový objekt BLOB může mít jenom jednu nevyřízenou operaci kopírování. Jinými slovy, objekt BLOB nemůže být cílem pro více operací kopírování, které čekají na zpracování.

Celý zdrojový objekt BLOB nebo soubor se vždycky kopíruje. Kopírování rozsahu bajtů nebo sady bloků se nepodporuje.

Po zkopírování objektu BLOB se do cílového objektu BLOB zkopírují systémové vlastnosti se stejnými hodnotami.

Operace kopírování může mít některou z následujících forem:

- Zkopírujte zdrojový objekt blob do cílového objektu BLOB s jiným názvem. Cílový objekt BLOB může být existující objekt BLOB stejného typu objektu BLOB (blok, připojit nebo stránka), nebo může být nový objekt BLOB vytvořený operací kopírování.
- Zkopírujte zdrojový objekt blob do cílového objektu BLOB se stejným názvem a efektivně nahraďte cílový objekt BLOB. Taková operace kopírování odstraní všechny nepotvrzené bloky a přepíše metadata cílového objektu BLOB.
- Zkopírujte zdrojový soubor ve službě souborové služby Azure do cílového objektu BLOB. Cílový objekt BLOB může být existující objekt blob bloku nebo může být nový objekt blob bloku vytvořený operací kopírování. Kopírování ze souborů do objektů blob stránky nebo doplňovací objekty BLOB se nepodporuje.
- Zkopírování snímku přes svůj základní objekt BLOB. Zvýšením úrovně snímku na pozici základního objektu blob můžete obnovit předchozí verzi objektu BLOB.
- Zkopírování snímku do cílového objektu BLOB s jiným názvem. Výsledný cílový objekt BLOB je zapisovatelný objekt blob, který není snímkem.

## <a name="copy-a-blob"></a>Kopírování objektu BLOB

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Chcete-li kopírovat objekt blob, zavolejte jednu z následujících metod:

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

`StartCopyFromUri`Metody a `StartCopyFromUriAsync` vrátí objekt [CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation) , který obsahuje informace o operaci kopírování.

Následující příklad kódu získá [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) představující dříve vytvořený objekt BLOB a zkopíruje ho do nového objektu BLOB ve stejném kontejneru:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Chcete-li kopírovat objekt blob, zavolejte jednu z následujících metod:

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

`StartCopy`Metody a `StartCopyAsync` VRACEJÍ hodnotu ID kopie, která se používá ke kontrole stavu nebo přerušení operace kopírování.

Následující příklad kódu získá odkaz na dřív vytvořený objekt BLOB a zkopíruje ho do nového objektu BLOB ve stejném kontejneru:

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

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
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

# <a name="python-v12"></a>[Python V12](#tab/python)

Chcete-li zkopírovat objekt blob, zavolejte metodu [start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) . `start_copy_from_url`Metoda vrací slovník obsahující informace o operaci kopírování.

Následující příklad kódu získá [BlobClient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) představující dříve vytvořený objekt BLOB a zkopíruje ho do nového objektu BLOB ve stejném kontejneru:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>Přerušení operace kopírování

Přerušení operace kopírování má za následek, že cílový objekt BLOB nemá nulovou délku. Metadata pro cílový objekt BLOB ale budou mít nové hodnoty zkopírované ze zdrojového objektu BLOB nebo se nastaví explicitně během operace kopírování. Chcete-li zachovat původní metadata před kopírováním, vytvořte snímek cílového objektu BLOB před voláním jedné z metod kopírování.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Pokud chcete získat stav operace kopírování, podívejte se do vlastnosti [BlobProperties. CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) v cílovém objektu BLOB. Konečný objekt BLOB bude potvrzen po dokončení kopírování.

Při přerušení operace kopírování je stav kopírování cílového objektu BLOB nastavený na [CopyStatus. Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus).

Metody [AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) a [AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) zruší probíhající operaci kopírování.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Pokud chcete získat stav operace kopírování, podívejte se do vlastnosti [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status) cílového objektu BLOB. Konečný objekt BLOB bude potvrzen po dokončení kopírování.

Při přerušení operace kopírování je stav kopírování cílového objektu BLOB nastavený na [CopyStatus. Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus).

Metody [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) a [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) zruší probíhající operaci kopírování.

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

# <a name="python-v12"></a>[Python V12](#tab/python)

Pokud chcete získat stav operace kopírování, podívejte se do [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) slovníku vráceného metodou [get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-) . Konečný objekt BLOB bude potvrzen po dokončení kopírování.

Při přerušení operace kopírování je [stav](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) nastaven na "přerušeno".

Metoda [abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-) zruší probíhající operaci kopírování.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>Sady Azure SDK

Získat další informace o sadách Azure SDK:

 - [Sada Azure SDK pro .NET](https://github.com/azure/azure-sdk-for-net)
 - [Azure SDK pro Javu](https://github.com/azure/azure-sdk-for-java)
 - [Azure SDK pro Python](https://github.com/azure/azure-sdk-for-python)
 - [Azure SDK for JavaScript](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>Další kroky

Následující témata obsahují informace o kopírování objektů BLOB a přerušování probíhajících operací kopírování pomocí rozhraní Azure REST API.

- [Zkopírování objektu blob](/rest/api/storageservices/copy-blob)
- [Přerušit kopii objektu BLOB](/rest/api/storageservices/abort-copy-blob)
