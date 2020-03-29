---
title: Přiřazení role RBAC pro přístup k datům pomocí prostředí PowerShell
titleSuffix: Azure Storage
description: Naučte se, jak pomocí PowerShellu přiřadit oprávnění k objektu zabezpečení služby Azure Active Directory pomocí řízení přístupu na základě rolí (RBAC). Azure Storage podporuje integrované a vlastní role RBAC pro ověřování prostřednictvím Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1413035c879198cf333aeeb5d8fe993162939172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460583"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Použití prostředí PowerShell k přiřazení role RBAC pro přístup k datům objektů blob a fronty

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě rolí (RBAC).](../../role-based-access-control/overview.md) Azure Storage definuje sadu předdefinovaných rolí RBAC, které zahrnují společné sady oprávnění používaných pro přístup ke kontejnerům nebo frontám.

Když je role RBAC přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup může být vymezen na úroveň předplatného, skupiny prostředků, účtu úložiště nebo jednotlivého kontejneru nebo fronty. Zaregistrovaný objekt zabezpečení Azure AD může být uživatel, skupina, instanční objekt aplikační služby nebo [spravovaná identita pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Tento článek popisuje, jak pomocí Prostředí Azure PowerShell uvádět předdefinované role RBAC a přiřazovat je uživatelům. Další informace o používání Azure PowerShellu najdete v [tématu Přehled Azure PowerShellu](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC pro objekty BLOB a fronty

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Určit obor prostředků

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Seznam dostupných rolí RBAC

Chcete-li v ypsit dostupné předdefinované role RBAC s Prostředím Azure PowerShell, použijte příkaz [Get-AzRoleDefinition:](/powershell/module/az.resources/get-azroledefinition)

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Zobrazí se uvedené předdefinované datové role Azure Storage spolu s dalšími předdefinovanými rolemi pro Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Přiřazení role RBAC k objektu zabezpečení

Chcete-li přiřadit roli RBAC k objektu zabezpečení, použijte příkaz [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment) Formát příkazu se může lišit v závislosti na rozsahu přiřazení. Chcete-li spustit příkaz, musíte mít roli vlastníka nebo přispěvatele přiřazenou v odpovídajícím oboru. Následující příklady ukazují, jak přiřadit roli uživateli v různých oborech, ale můžete použít stejný příkaz k přiřazení role libovolnému objektu zabezpečení.

### <a name="container-scope"></a>Obor kontejneru

Chcete-li přiřadit roli vymezenou kontejneru, zadejte řetězec obsahující `--scope` rozsah kontejneru pro parametr. Obor pro kontejner je ve formě:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Následující příklad přiřadí roli **přispěvatele dat objektů blob úložiště** uživateli, která je vymezena kontejnerem s názvem *ukázkový kontejner*. Nezapomeňte nahradit hodnoty vzorku a zástupné hodnoty v závorkách vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Obor fronty

Chcete-li přiřadit roli vymezenou do fronty, zadejte řetězec obsahující `--scope` rozsah fronty pro parametr. Obor fronty je ve formě:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Následující příklad přiřadí roli **přispěvatele dat fronty úložiště** uživateli s vymezeným oborem fronty s názvem *vzorová fronta*. Nezapomeňte nahradit hodnoty vzorku a zástupné hodnoty v závorkách vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Obor účtu úložiště

Chcete-li přiřadit roli vymezenou účtu úložiště, zadejte obor `--scope` prostředku účtu úložiště pro parametr. Obor pro účet úložiště je ve formě:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Následující příklad ukazuje, jak obor role **čtečky dat objektů blob úložiště** pro uživatele na úrovni účtu úložiště. Nezapomeňte nahradit ukázkové hodnoty vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Obor skupiny prostředků

Chcete-li přiřadit určitou roli vymezenou skupině prostředků, zadejte `--resource-group` název skupiny prostředků nebo ID parametru. Následující příklad přiřadí roli **čtečky dat fronty úložiště** uživateli na úrovni skupiny prostředků. Nezapomeňte nahradit hodnoty vzorku a zástupné hodnoty v závorkách vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Obor předplatného

Chcete-li přiřadit roli vymezené k odběru, zadejte `--scope` obor pro odběr pro parametr. Obor pro odběr je ve formě:

```
/subscriptions/<subscription>
```

Následující příklad ukazuje, jak přiřadit roli **čtečky dat objektů blob úložiště** uživateli na úrovni účtu úložiště. Nezapomeňte nahradit ukázkové hodnoty vlastními hodnotami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Další kroky

- [Správa přístupu k prostředkům Azure pomocí RBAC a Azure PowerShellu](../../role-based-access-control/role-assignments-powershell.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC pomocí Azure CLI](storage-auth-aad-rbac-cli.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md)
