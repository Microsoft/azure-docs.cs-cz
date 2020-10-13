---
title: Vytvoření uložené zásady přístupu pomocí .NET
titleSuffix: Azure Storage
description: Pomocí Azure Storage a .NET vytvořte zásady uloženého přístupu. Využijte další úrovně kontroly nad signaturami sdíleného přístupu na úrovni služby na serveru.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd6147fbe38710bcefd580e71be1d6f5d446a21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89010745"
---
# <a name="create-a-stored-access-policy-with-net"></a>Vytvoření uložené zásady přístupu pomocí .NET

Zásady uloženého přístupu poskytují další úroveň kontroly nad škálováním na straně serveru na úrovni služby (SAS) na straně serveru. Definováním uložených zásad přístupu můžete seskupit signatury sdíleného přístupu a poskytnout dodatečná omezení pro sdílené přístupové podpisy, které jsou vázány zásadou. Pomocí uložených zásad přístupu můžete změnit čas spuštění, čas vypršení platnosti nebo oprávnění pro SAS nebo ho odvolat po jeho vydání.
  
Uložené zásady přístupu podporují následující Azure Storage prostředky:  
  
- Kontejnery objektů blob  
- Sdílené složky  
- Fronty  
- Tabulky  
  
> [!NOTE]
> Uložené zásady přístupu na kontejneru mohou být přidruženy ke sdílenému přístupovému podpisu, který uděluje oprávnění samotnému kontejneru nebo objektům blob, které obsahuje. Podobně platí, že uložené zásady přístupu ve sdílené složce je možné přidružit ke sdílenému přístupovému podpisu, který uděluje oprávnění ke sdílené složce nebo k souborům, které obsahuje.  
>
> Uložené zásady přístupu se podporují jenom pro SAS služby. Uložené zásady přístupu se pro SAS účtu nebo delegování uživatelů nepodporují.  

Další informace o uložených zásadách přístupu najdete v tématu [definice uložených zásad přístupu](/rest/api/storageservices/define-stored-access-policy).

## <a name="create-a-stored-access-policy"></a>Vytvoření uložené zásady přístupu

Základní operace REST pro vytvoření zásad uloženého přístupu je [nastavená jako seznam ACL kontejneru](/rest/api/storageservices/set-container-acl). Aby bylo možné vytvořit uložené zásady přístupu přes sdílený klíč pomocí přístupových klíčů k účtu v připojovacím řetězci, musíte operaci autorizovat. Autorizace operace **Nastavení seznamu ACL kontejneru** s přihlašovacími údaji Azure AD není podporovaná. Další informace najdete v tématu [oprávnění pro volání operací s daty objektů BLOB a front](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Následující příklady kódu vytvoří uloženou zásadu přístupu na kontejneru. Zásady přístupu můžete použít k určení omezení pro SAS služby na kontejneru nebo v jeho objektech blob.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Chcete-li vytvořit uloženou zásadu přístupu na kontejneru s verzí 12 klientské knihovny .NET pro Azure Storage, zavolejte jednu z následujících metod:

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

Následující příklad vytvoří zásady uloženého přístupu platné pro jeden den a udělí oprávnění ke čtení a zápisu:

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Chcete-li vytvořit uloženou zásadu přístupu na kontejneru s verzí 12 klientské knihovny .NET pro Azure Storage, zavolejte jednu z následujících metod:

- [CloudBlobContainer. SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer. SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

Následující příklad vytvoří zásady uloženého přístupu platné pro jeden den a udělí oprávnění ke čtení, zápisu a výpisu:

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>Viz také

- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md)
- [Definování uložených zásad přístupu](/rest/api/storageservices/define-stored-access-policy)
- [Nakonfigurování připojovacích řetězců Azure Storage](storage-configure-connection-string.md)
