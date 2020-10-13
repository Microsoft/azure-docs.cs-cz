---
title: Povolení a správa verzí objektů BLOB
titleSuffix: Azure Storage
description: Naučte se povolit správu verzí objektů BLOB ve Azure Portal nebo pomocí Azure Resource Manager šablony.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 1df7afb5a029ff7770a64d6bf698a462c8ab9735
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230666"
---
# <a name="enable-and-manage-blob-versioning"></a>Povolení a správa verzí objektů BLOB

Chcete-li automaticky zachovat předchozí verze objektu, můžete povolit správu verzí služby Blob Storage.  Pokud je povolená Správa verzí objektů blob, můžete obnovit předchozí verzi objektu blob, aby se data obnovila v případě, že se omylem změnila nebo odstranila.

Tento článek popisuje, jak povolit nebo zakázat správu verzí objektů BLOB pro účet úložiště pomocí šablony Azure Portal nebo Azure Resource Manager. Další informace o tom, jak se správou verzí objektů blob, najdete v tématu [Správa verzí objektů BLOB](versioning-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Povolení správy verzí objektů blob

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Povolení správy verzí objektů BLOB v Azure Portal:

1. Na portálu přejděte na svůj účet úložiště.
1. V části **BLOB Service**vyberte **Ochrana dat**.
1. V části **Správa verzí** vyberte **povoleno**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Snímek obrazovky ukazující, jak povolit správu verzí objektů BLOB v Azure Portal":::

# <a name="template"></a>[Šablona](#tab/template)

Chcete-li povolit správu verzí objektů BLOB pomocí šablony, vytvořte šablonu s vlastností **IsVersioningEnabled** na **hodnotu true**. Následující postup popisuje, jak vytvořit šablonu v Azure Portal.

1. V Azure Portal klikněte na možnost **vytvořit prostředek**.
1. V **části Hledat na Marketplace**zadejte **šablonu Deployment**a potom stiskněte **ENTER**.
1. Zvolte **template Deployment**, zvolte **vytvořit**a potom **v editoru zvolte sestavit vlastní šablonu**.
1. V editoru šablon vložte následující kód JSON. `<accountName>`Zástupný symbol nahraďte názvem vašeho účtu úložiště.
1. Uložte šablonu.
1. Zadejte skupinu prostředků účtu a pak kliknutím na tlačítko **koupit** nasaďte šablonu a povolte správu verzí objektů BLOB.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Další informace o nasazení prostředků pomocí šablon v Azure Portal najdete v tématu [nasazení prostředků pomocí Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Úprava objektu BLOB tak, aby aktivoval novou verzi

Následující příklad kódu ukazuje, jak aktivovat vytvoření nové verze pomocí klientské knihovny Azure Storage pro .NET, verze [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) nebo novější. Před spuštěním tohoto příkladu se ujistěte, že jste povolili správu verzí pro váš účet úložiště.

Tento příklad vytvoří objekt blob bloku a pak aktualizuje metadata objektu BLOB. Aktualizace metadat objektu BLOB aktivuje vytvoření nové verze. Příklad načte počáteční verzi a aktuální verzi a zobrazí, že metadata obsahují pouze aktuální verze.

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>Další kroky

- [Správa verzí objektů BLOB](versioning-overview.md)
- [Obnovitelné odstranění objektů blob služby Azure Storage](soft-delete-overview.md)
