---
title: Použití Azure Powershellu ke správě Azure AD přístupová práva k datům objektu blob a fronty pomocí RBAC – Azure Storage
description: Pomocí Azure Powershellu k přiřazení přístupu ke kontejnerům a fronty s řízením přístupu na základě rolí (RBAC). Azure Storage podporuje předdefinované a vlastní role RBAC pro ověřování pomocí Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e850b915cd01b6bacd70d6df7752eeb83f7101d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153846"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC pomocí Powershellu

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md). Azure Storage definuje sadu předdefinované role RBAC, které zahrnuje společné sady oprávnění pro přístup k kontejnery nebo fronty. 

Když roli RBAC je přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup se dají vymezit na úrovni předplatného, skupinu prostředků, účet úložiště nebo kontejner nebo fronty. Objekt zabezpečení Azure AD může být uživatel, skupina, instančního objektu aplikace nebo [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Tento článek popisuje, jak pomocí prostředí Azure PowerShell seznamu předdefinované role RBAC a přiřadit je uživatelům. Další informace o použití Azure Powershellu najdete v tématu [Přehled prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC pro objekty BLOB a fronty

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Určení oboru prostředků 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Seznam dostupných rolí RBAC

K zobrazení seznamu dostupných předdefinované role RBAC pomocí Azure Powershellu, použijte [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) příkaz:

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Zobrazí se vám předdefinované role dat služby Azure Storage uvedený spolu s další předdefinované role pro Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-user"></a>Přiřaďte roli RBAC pro uživatele

Pokud chcete uživateli přiřadit roli RBAC, použijte [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) příkazu. Formát příkazu může lišit v závislosti na rozsah přiřazení. Následující příklady znázorňují způsob přiřazení role uživateli v různých oborech.

### <a name="container-scope"></a>Kontejner oboru

Přiřazení role obor do kontejneru, zadejte řetězec obsahující obor kontejneru `--scope` parametru. Obor pro kontejner je ve formátu:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Následující příklad přiřadí **Přispěvatel dat objektu Blob úložiště** role pro uživatele, omezená na kontejner s názvem *ukázkový kontejner*. Nezapomeňte ukázkové hodnoty a zástupné hodnoty v závorkách nahraďte vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Oboru fronty

Přiřazení role obor pro frontu, zadejte řetězec obsahující obor do fronty `--scope` parametru. Obor pro frontu je ve formátu:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Následující příklad přiřadí **Přispěvatel dat fronty služby Storage** role pro uživatele, obor pro frontu s názvem *Ukázka fronty*. Nezapomeňte ukázkové hodnoty a zástupné hodnoty v závorkách nahraďte vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Obor účtů úložiště

Přiřazení role omezená na účet úložiště, zadejte obor prostředek účtu úložiště pro `--scope` parametru. Obor pro účet úložiště je ve formátu:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Následující příklad ukazuje postup obor **čtecí modul dat pro úložiště objektů Blob** role pro uživatele na úrovni účtu úložiště. Nezapomeňte ukázkové hodnoty nahradit vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Obor skupiny prostředků

Přiřazení role vymezeny na skupinu prostředků, zadejte název skupiny prostředků nebo ID `--resource-group` parametru. Následující příklad přiřadí **Čtenář dat fronty služby Storage** role pro uživatele na úrovni skupiny prostředků. Nezapomeňte ukázkové hodnoty a zástupné hodnoty v závorkách nahraďte vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Oboru předplatného

Přiřazení role omezená na předplatné, zadejte rozsah pro odběr `--scope` parametru. Obor pro odběr je ve formátu:

```
/subscriptions/<subscription>
```

Následující příklad ukazuje, jak přiřadit **čtecí modul dat pro úložiště objektů Blob** role pro uživatele na úrovni účtu úložiště. Nezapomeňte ukázkové hodnoty nahradit vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Další postup

- [Správa přístupu k prostředkům Azure pomocí RBAC a Azure Powershellu](../../role-based-access-control/role-assignments-powershell.md)
- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC pomocí rozhraní příkazového řádku Azure](storage-auth-aad-rbac-cli.md)
- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md)
