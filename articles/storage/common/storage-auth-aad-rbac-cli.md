---
title: Přiřazení role RBAC pro přístup k datům pomocí azure cli
titleSuffix: Azure Storage
description: Zjistěte, jak pomocí rozhraní API Azure přiřadit oprávnění k objektu zabezpečení služby Azure Active Directory pomocí řízení přístupu na základě rolí (RBAC). Azure Storage podporuje integrované a vlastní role RBAC pro ověřování prostřednictvím Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: abe35f3193e2d7ff9a949ca7cd330cb58da2b78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74891964"
---
# <a name="use-azure-cli-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Použití azure cli k přiřazení role RBAC pro přístup k datům objektů blob a fronty

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě rolí (RBAC).](../../role-based-access-control/overview.md) Azure Storage definuje sadu předdefinovaných rolí RBAC, které zahrnují společné sady oprávnění používaných pro přístup k datům objektů blob nebo fronty.

Když je role RBAC přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup může být vymezen na úroveň předplatného, skupiny prostředků, účtu úložiště nebo jednotlivého kontejneru nebo fronty. Zaregistrovaný objekt zabezpečení Azure AD může být uživatel, skupina, instanční objekt aplikační služby nebo [spravovaná identita pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Tento článek popisuje, jak pomocí rozhraní příkazového příkazového příkazu Azure vypsat předdefinované role RBAC a přiřadit je uživatelům. Další informace o používání rozhraní příkazového řádku Azure najdete v [tématu Rozhraní příkazového řádku Azure (CLI).](https://docs.microsoft.com/cli/azure)

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC pro objekty BLOB a fronty

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Určit obor prostředků

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Seznam dostupných rolí RBAC

Chcete-li seznam dostupných předdefinovaných rolí RBAC s rozhraním příkazového příkazu Azure CLI, použijte příkaz [az role definition list:](/cli/azure/role/definition#az-role-definition-list)

```azurecli-interactive
az role definition list --out table
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

Chcete-li přiřadit roli RBAC k objektu zabezpečení, použijte příkaz [vytvořit přiřazení role az.](/cli/azure/role/assignment#az-role-assignment-create) Formát příkazu se může lišit v závislosti na rozsahu přiřazení. Následující příklady ukazují, jak přiřadit roli uživateli v různých oborech, ale můžete použít stejný příkaz k přiřazení role libovolnému objektu zabezpečení.

### <a name="container-scope"></a>Obor kontejneru

Chcete-li přiřadit roli vymezenou kontejneru, zadejte řetězec obsahující `--scope` rozsah kontejneru pro parametr. Obor pro kontejner je ve formě:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

Následující příklad přiřadí roli **přispěvatele dat objektů blob úložiště** uživateli s vymezeným oborem na úroveň kontejneru. Nezapomeňte nahradit hodnoty vzorku a zástupné hodnoty v závorkách vlastními hodnotami:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Obor fronty

Chcete-li přiřadit roli vymezenou do fronty, zadejte řetězec obsahující `--scope` rozsah fronty pro parametr. Obor fronty je ve formě:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

Následující příklad přiřadí roli **přispěvatele dat fronty úložiště** uživateli s vymezeným oborem na úroveň fronty. Nezapomeňte nahradit hodnoty vzorku a zástupné hodnoty v závorkách vlastními hodnotami:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Obor účtu úložiště

Chcete-li přiřadit roli vymezenou účtu úložiště, zadejte obor `--scope` prostředku účtu úložiště pro parametr. Obor pro účet úložiště je ve formě:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Následující příklad ukazuje, jak přiřadit roli **čtečky dat objektů blob úložiště** uživateli na úrovni účtu úložiště. Nezapomeňte nahradit ukázkové hodnoty vlastními hodnotami: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Obor skupiny prostředků

Chcete-li přiřadit určitou roli vymezenou skupině prostředků, zadejte `--resource-group` název skupiny prostředků nebo ID parametru. Následující příklad přiřadí roli **čtečky dat fronty úložiště** uživateli na úrovni skupiny prostředků. Nezapomeňte nahradit hodnoty vzorku a zástupné hodnoty v závorkách vlastními hodnotami:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Obor předplatného

Chcete-li přiřadit roli vymezené k odběru, zadejte `--scope` obor pro odběr pro parametr. Obor pro odběr je ve formě:

```
/subscriptions/<subscription>
```

Následující příklad ukazuje, jak přiřadit roli **čtečky dat objektů blob úložiště** uživateli na úrovni účtu úložiště. Nezapomeňte nahradit ukázkové hodnoty vlastními hodnotami: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Další kroky

- [Správa přístupu k prostředkům Azure pomocí RBAC a Azure PowerShellu](../../role-based-access-control/role-assignments-powershell.md)
- [Udělit přístup k datům objektů blob Azure a fronty pomocí RBAC pomocí Azure PowerShellu](storage-auth-aad-rbac-powershell.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md)
