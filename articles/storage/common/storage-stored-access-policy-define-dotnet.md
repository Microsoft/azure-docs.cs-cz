---
title: Definování zásad uloženého přístupu pomocí rozhraní .NET – Azure Storage
description: Zjistěte, jak definovat uložené zásady přístupu pomocí knihovny klienta .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68990738"
---
# <a name="define-a-stored-access-policy-with-net"></a>Definování zásad uloženého přístupu pomocí rozhraní .NET

Uložené zásady přístupu poskytuje další úroveň kontroly nad podpisy sdíleného přístupu (SAS) na úrovni služby na straně serveru. Definování zásad y uloženého přístupu slouží k seskupení sdílených přístupových podpisů a k poskytnutí dalších omezení pro podpisy sdíleného přístupu, které jsou zásadami vázány. Uložené zásady přístupu můžete použít ke změně času zahájení, času vypršení platnosti nebo oprávnění pro SAS nebo k jeho odvolání po jeho vydání.
  
 Následující prostředky úložiště podporují uložené zásady přístupu:  
  
- Kontejnery s objekty blob  
- Sdílené složky  
- Fronty  
- Tabulky  
  
> [!NOTE]
> Uložené zásady přístupu v kontejneru lze přidružit ke sdílenému přístupu podpis udělující oprávnění ke samotnému kontejneru nebo objekty BLOB, které obsahuje. Podobně uložené zásady přístupu ke sdílené složce mohou být přidruženy ke sdílenému přístupovému podpisu udělujícímu oprávnění samotné sdílené složce nebo souborům, které obsahuje.  
>
> Uložené zásady přístupu jsou podporovány pouze pro službu SAS. Uložené zásady přístupu nejsou podporovány pro účet SAS nebo uživatelské delegování SAS.  

## <a name="create-a-stored-access-policy"></a>Vytvoření uložených zásad přístupu

Následující kód vytvoří uložené zásady přístupu v kontejneru. Pomocí zásad přístupu můžete určit omezení pro službu SAS v kontejneru nebo jeho objekty BLOB.

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

- [Udělit omezený přístup k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md)
- [Definování uložených zásad přístupu](/rest/api/storageservices/define-stored-access-policy)

