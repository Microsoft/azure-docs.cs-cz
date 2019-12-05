---
title: Zadejte klíč poskytnutý zákazníkem pro požadavek na úložiště objektů BLOB pomocí .NET-Azure Storage
description: Naučte se, jak zadat klíč poskytnutý zákazníkem pro požadavek na úložiště objektů BLOB pomocí .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806996"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Určení klíče poskytovaného zákazníkem pro požadavek na úložiště objektů BLOB s využitím .NET

Klienti, kteří provádějí požadavky na úložiště objektů BLOB v Azure, mají možnost zadat šifrovací klíč pro jednotlivé požadavky. Zahrnutí šifrovacího klíče na žádost poskytuje podrobnou kontrolu nad nastavením šifrování pro operace BLOB Storage. Klíče poskytované zákazníky (Preview) je možné uložit v Azure Key Vault nebo v jiném úložišti klíčů.

V tomto článku se dozvíte, jak zadat klíč poskytnutý zákazníkem v žádosti s .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Další informace o tom, jak ověřit pomocí klientské knihovny Azure identity z Azure Storage, najdete v části s názvem **ověřování pomocí knihovny identit Azure** v tématu [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Příklad: použití klíčového zákazníka k nahrání objektu BLOB

Následující příklad vytvoří klíč poskytnutý zákazníkem a použije tento klíč k nahrání objektu BLOB. Kód nahraje blok a pak potvrdí seznam blokování, aby napsal objekt blob do Azure Storage.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Další kroky

- [Azure Storage šifrování dat v klidovém umístění](../common/storage-service-encryption.md)
- [Autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](../common/storage-auth-aad-msi.md)
