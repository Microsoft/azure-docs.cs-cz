---
title: Vytvoření a Správa snímku objektu BLOB v .NET
titleSuffix: Azure Storage
description: Naučte se používat klientskou knihovnu .NET k vytvoření snímku objektu BLOB jen pro čtení pro zálohování dat objektů BLOB v daném časovém okamžiku.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d72c30fb7c45c92f24ec5132859a45c6ae953
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95543370"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Vytvoření a Správa snímku objektu BLOB v .NET

Snímek je verze objektu BLOB jen pro čtení, která je pořízena v určitém časovém okamžiku. Tento článek popisuje, jak vytvořit a spravovat snímky objektů BLOB pomocí [klientské knihovny Azure Storage pro .NET](/dotnet/api/overview/azure/storage).

Další informace o snímcích objektů BLOB v Azure Storage najdete v tématu [snímky objektů BLOB](snapshots-overview.md).

## <a name="create-a-snapshot"></a>Vytvoření snímku

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Chcete-li vytvořit snímek objektu blob bloku pomocí verze 12. x Azure Storage klientské knihovny pro .NET, použijte jednu z následujících metod:

- [CreateSnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

Následující příklad kódu ukazuje, jak vytvořit snímek s verzí 12. x. Přidejte odkaz na knihovnu [Azure. identity](https://www.nuget.org/packages/azure.identity) a použijte přihlašovací údaje Azure AD k autorizaci požadavků službě.

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Chcete-li vytvořit snímek objektu blob bloku pomocí verze 11. x Azure Storage klientské knihovny pro rozhraní .NET, použijte jednu z následujících metod:

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

Následující příklad kódu ukazuje, jak vytvořit snímek s verzí 11. x. Tento příklad určuje další metadata pro snímek při jeho vytvoření.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
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

## <a name="delete-snapshots"></a>Odstranit snímky

Pokud chcete odstranit objekt blob, musíte nejdřív odstranit všechny snímky tohoto objektu BLOB. Snímek můžete odstranit jednotlivě nebo můžete určit, že se při odstranění zdrojového objektu BLOB odstraní všechny snímky. Pokud se pokusíte odstranit objekt blob, který má stále snímky, dojde k chybě.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Pokud chcete odstranit objekt BLOB a jeho snímky pomocí verze 12. x Azure Storage klientské knihovny pro .NET, použijte jednu z následujících metod a zahrňte do výčtu [DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) :

- [Odstranit](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

Následující příklad kódu ukazuje, jak odstranit objekt BLOB a jeho snímky v rozhraní .NET, kde `blobClient` je objekt typu [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)):

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Pokud chcete odstranit objekt BLOB a jeho snímky pomocí verze 11. x Azure Storage klientské knihovny pro .NET, použijte jednu z následujících metod odstranění objektů BLOB a přidejte výčet [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) :

- [Odstranit](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

Následující příklad kódu ukazuje, jak odstranit objekt BLOB a jeho snímky v rozhraní .NET, kde `blockBlob` je objekt typu [CloudBlockBlob] [dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>Další kroky

- [Snímky objektů BLOB](snapshots-overview.md)
- [Verze objektů BLOB](versioning-overview.md)
- [Obnovitelné odstranění pro objekty blob](./soft-delete-blob-overview.md)