---
title: Definování zásad uloženého přístupu pomocí .NET – Azure Storage
description: Naučte se definovat uložené zásady přístupu pomocí klientské knihovny .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "68990738"
---
# <a name="define-a-stored-access-policy-with-net"></a>Definování uložených zásad přístupu pomocí .NET

Zásady uloženého přístupu poskytují další úroveň kontroly nad škálováním na straně serveru na úrovni služby (SAS) na straně serveru. Definováním uložených zásad přístupu můžete seskupit signatury sdíleného přístupu a poskytnout dodatečná omezení pro sdílené přístupové podpisy, které jsou vázány zásadou. Pomocí uložených zásad přístupu můžete změnit čas spuštění, čas vypršení platnosti nebo oprávnění pro SAS nebo ho odvolat po jeho vydání.
  
 Uložené zásady přístupu podporují následující prostředky úložiště:  
  
- Kontejnery objektů BLOB  
- Sdílené složky  
- Fronty  
- Tabulky  
  
> [!NOTE]
> Uložené zásady přístupu na kontejneru mohou být přidruženy ke sdílenému přístupovému podpisu, který uděluje oprávnění samotnému kontejneru nebo objektům blob, které obsahuje. Podobně platí, že uložené zásady přístupu ve sdílené složce je možné přidružit ke sdílenému přístupovému podpisu, který uděluje oprávnění ke sdílené složce nebo k souborům, které obsahuje.  
>
> Uložené zásady přístupu se podporují jenom pro SAS služby. Uložené zásady přístupu se pro SAS účtu nebo delegování uživatelů nepodporují.  

## <a name="create-a-stored-access-policy"></a>Vytvoření uložené zásady přístupu

Následující kód vytvoří uloženou zásadu přístupu na kontejneru. Zásady přístupu můžete použít k určení omezení pro SAS služby na kontejneru nebo v jeho objektech blob.

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
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>Viz také

- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md)
- [Definování uložených zásad přístupu](/rest/api/storageservices/define-stored-access-policy)

