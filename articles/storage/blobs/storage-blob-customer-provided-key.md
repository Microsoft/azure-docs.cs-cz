---
title: Zadejte klíč zadaný zákazníkem pro požadavek na úložiště objektů Blob s rozhraním .NET – Azure Storage
description: Zjistěte, jak zadat klíč zadaný zákazníkem pro požadavek na úložiště objektů Blob pomocí rozhraní .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806996"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Určení klíče poskytnutého zákazníkem pro požadavek na úložiště objektů Blob s rozhraním .NET

Klienti, kteří pořizují požadavky na úložiště objektů Blob Azure, mají možnost poskytnout šifrovací klíč na individuální požadavek. Zahrnutí šifrovacího klíče v požadavku poskytuje podrobnou kontrolu nad nastavením šifrování pro operace úložiště objektů Blob. Klíče poskytované zákazníkem (preview) lze uložit v trezoru klíčů Azure nebo v jiném úložišti klíčů.

Tento článek ukazuje, jak zadat klíč zadaný zákazníkem na základě požadavku s rozhraním .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Další informace o tom, jak se ověřovat pomocí klientské knihovny Azure Identity z Azure Storage, najdete v části **Ověření pomocí knihovny Azure Identity** v [tématu Autorizace přístupu k objektům BLOB a frontám s Azure Active Directory a spravovaným i pro prostředky Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Příklad: K nahrání objektu blob pomocí klíče poskytnutého zákazníkem

Následující příklad vytvoří klíč zadaný zákazníkem a použije tento klíč k nahrání objektu blob. Kód nahraje blok a pak potvrdí seznam blokování k zápisu objektu blob do Služby Azure Storage.

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

- [Šifrování Azure Storage pro data v klidovém stavu](../common/storage-service-encryption.md)
- [Autorizace přístupu k objektům BLOB a frontám pomocí Služby Azure Active Directory a spravovaných identit pro prostředky Azure](../common/storage-auth-aad-msi.md)
