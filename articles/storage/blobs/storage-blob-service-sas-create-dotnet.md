---
title: Vytvoření SAS služby pro kontejner nebo objekt BLOB pomocí .NET-Azure Storage
description: Naučte se vytvořit sdílený přístupový podpis služby (SAS) pro kontejner nebo objekt BLOB pomocí klientské knihovny .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/09/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: ca419165d4dea2a04f51e7ab6f60d6e23f418933
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035630"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>Vytvoření SAS služby pro kontejner nebo objekt BLOB s využitím .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

V tomto článku se dozvíte, jak pomocí klíče účtu úložiště vytvořit SAS služby pro kontejner nebo objekt BLOB pomocí [klientské knihovny Azure Storage pro .NET](/dotnet/api/overview/azure/storage/client).

## <a name="create-a-service-sas-for-a-blob-container"></a>Vytvoření SAS služby pro kontejner objektů BLOB

Chcete-li vytvořit SAS služby pro kontejner, zavolejte metodu [CloudBlobContainer. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) .

Následující příklad kódu vytvoří SAS na kontejneru. Pokud je zadaný název existující zásady přístupu, tato zásada je přidružená k SAS. Pokud nejsou k dispozici žádné uložené zásady přístupu, pak kód vytvoří ad hoc SAS na kontejneru.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

## <a name="create-a-service-sas-for-a-blob"></a>Vytvoření SAS služby pro objekt BLOB

Chcete-li vytvořit SAS služby pro objekt blob, zavolejte metodu [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) .

Následující příklad kódu vytvoří SAS pro objekt BLOB. Pokud je zadaný název existující zásady přístupu, tato zásada je přidružená k SAS. Pokud nejsou k dispozici žádné uložené zásady přístupu, vytvoří kód v objektu BLOB ad hoc SAS.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Další kroky

- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md)
- [Vytvoření SAS služby](/rest/api/storageservices/create-service-sas)
