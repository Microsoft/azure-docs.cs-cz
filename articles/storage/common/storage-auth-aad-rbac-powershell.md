---
title: Přiřazení role RBAC pro přístup k datům pomocí PowerShellu
titleSuffix: Azure Storage
description: Naučte se používat PowerShell k přiřazení oprávnění k objektu zabezpečení Azure Active Directory s řízením přístupu na základě role (RBAC). Azure Storage podporuje integrované a vlastní role RBAC pro ověřování prostřednictvím služby Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e95d25e4f26abc48f2a734cf8456739e6ef55b84
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/16/2020
ms.locfileid: "84807697"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Přiřazení role RBAC pro přístup k datům BLOB a Queue pomocí PowerShellu

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md). Azure Storage definuje sadu předdefinovaných rolí RBAC, které zahrnují společné sady oprávnění používané pro přístup k kontejnerům nebo frontám.

Když je role RBAC přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezený na úrovni předplatného, skupiny prostředků, účtu úložiště nebo jednotlivého kontejneru nebo fronty. Objekt zabezpečení Azure AD může být uživatelem, skupinou, instančním objektem služby nebo [spravovanou identitou pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Tento článek popisuje, jak použít Azure PowerShell k vypsání předdefinovaných rolí RBAC a jejich přiřazení uživatelům. Další informace o použití Azure PowerShell najdete v tématu [přehled Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC pro objekty BLOB a fronty

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Určení oboru prostředků

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Výpis dostupných rolí RBAC

K vypsání dostupných integrovaných rolí RBAC pomocí Azure PowerShell použijte příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) :

```powershell
Get-AzRoleDefinition | FT Name, Description
```

V seznamu jsou uvedené předdefinované role Azure Storage dat spolu s dalšími integrovanými rolemi pro Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Přiřazení role RBAC objektu zabezpečení

K přiřazení role RBAC objektu zabezpečení použijte příkaz [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . Formát příkazu se může lišit v závislosti na rozsahu přiřazení. Aby bylo možné spustit příkaz, je nutné mít přiřazenou roli vlastníka nebo přispěvatele v příslušném oboru. Následující příklady ukazují, jak přiřadit roli uživateli v různých oborech, ale můžete použít stejný příkaz k přiřazení role k libovolnému objektu zabezpečení.

### <a name="container-scope"></a>Rozsah kontejneru

Chcete-li přiřadit obor role k kontejneru, zadejte řetězec obsahující obor kontejneru pro `--scope` parametr. Rozsah kontejneru je ve formátu:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Následující příklad přiřadí roli **Přispěvatel dat objektu BLOB úložiště** k uživateli s vymezeným kontejnerem s názvem *Sample-Container*. Nezapomeňte nahradit vzorové hodnoty a zástupné hodnoty v závorkách vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Rozsah fronty

Chcete-li přiřadit obor role ke frontě, zadejte řetězec obsahující obor fronty pro `--scope` parametr. Rozsah fronty je ve formátu:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Následující příklad přiřadí uživateli roli **Přispěvatel dat fronty úložiště** s vymezenou frontou s názvem *Sample-Queue*. Nezapomeňte nahradit vzorové hodnoty a zástupné hodnoty v závorkách vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Rozsah účtu úložiště

Pokud chcete přiřadit obor role k účtu úložiště, zadejte rozsah prostředku účtu úložiště pro tento `--scope` parametr. Rozsah účtu úložiště je ve formátu:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Následující příklad ukazuje, jak nastavit obor role **čtečky dat objektů BLOB úložiště** na uživatele na úrovni účtu úložiště. Nezapomeňte nahradit vzorové hodnoty vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Rozsah skupiny prostředků

Pokud chcete přiřadit obor role ke skupině prostředků, zadejte název nebo ID skupiny prostředků pro tento `--resource-group` parametr. Následující příklad přiřadí roli **čtečky dat fronty úložiště** k uživateli na úrovni skupiny prostředků. Nezapomeňte nahradit vzorové hodnoty a zástupné hodnoty v závorkách vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Rozsah předplatného

Pokud chcete přiřadit obor role k předplatnému, zadejte obor pro předplatné daného `--scope` parametru. Rozsah předplatného je ve formátu:

```
/subscriptions/<subscription>
```

Následující příklad ukazuje, jak přiřadit roli **čtečky dat objektů BLOB úložiště** k uživateli na úrovni účtu úložiště. Nezapomeňte nahradit vzorové hodnoty vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Další kroky

- [Správa přístupu k prostředkům Azure pomocí RBAC a Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC pomocí Azure CLI](storage-auth-aad-rbac-cli.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md)
