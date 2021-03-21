---
title: Zadejte klíč poskytnutý zákazníkem pro požadavek na úložiště objektů BLOB pomocí .NET-Azure Storage
description: Naučte se, jak zadat klíč poskytnutý zákazníkem pro požadavek na úložiště objektů BLOB pomocí .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693503"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Určení klíče poskytovaného zákazníkem pro požadavek na úložiště objektů BLOB s využitím .NET

Klienti, kteří provádějí požadavky na úložiště objektů BLOB v Azure, mají možnost zadat šifrovací klíč AES-256 pro jednotlivé požadavky. Zahrnutí šifrovacího klíče na žádost poskytuje podrobnou kontrolu nad nastavením šifrování pro operace BLOB Storage. Klíče poskytované zákazníkem mohou být uloženy v Azure Key Vault nebo v jiném úložišti klíčů.

V tomto článku se dozvíte, jak zadat klíč poskytnutý zákazníkem v žádosti s .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Další informace o tom, jak ověřit pomocí klientské knihovny Azure identity, najdete v tématu o [klientské knihovně Azure identity pro .NET](/dotnet/api/overview/azure/identity-readme).

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>Použití klíčového zákazníka k zápisu do objektu BLOB

Následující příklad poskytuje klíč AES-256 při nahrávání objektu BLOB s klientskou knihovnou V12 pro úložiště objektů BLOB. V příkladu se pomocí objektu [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) autorizuje požadavek na zápis ve službě Azure AD, ale můžete taky autorizovat žádost s přihlašovacími údaji ke sdíleným klíčům.

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

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

- [Zadání šifrovacího klíče pro požadavek na úložiště objektů BLOB](encryption-customer-provided-keys.md)
- [Šifrování služby Azure Storage pro neaktivní uložená data](../common/storage-service-encryption.md)
