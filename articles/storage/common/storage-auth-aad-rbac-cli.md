---
title: Přiřazení role Azure pro přístup k datům pomocí Azure CLI
titleSuffix: Azure Storage
description: Naučte se používat rozhraní příkazového řádku Azure k přiřazení oprávnění k objektu zabezpečení Azure Active Directory s řízením přístupu na základě role Azure (RBAC). Azure Storage podporuje integrované a vlastní role Azure pro ověřování prostřednictvím služby Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: c42061520b73966f2cd516716039d78c2b9cbeb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375982"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Přiřazení role Azure pro přístup k datům objektů BLOB a front pomocí Azure CLI

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definuje sadu předdefinovaných rolí Azure, které zahrnují společné sady oprávnění používané pro přístup k datům objektů BLOB nebo front.

Když je role Azure přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezený na úrovni předplatného, skupiny prostředků, účtu úložiště nebo jednotlivého kontejneru nebo fronty. Objekt zabezpečení Azure AD může být uživatelem, skupinou, instančním objektem služby nebo [spravovanou identitou pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Tento článek popisuje, jak pomocí Azure CLI vypsat předdefinované role Azure a přiřadit je uživatelům. Další informace o použití Azure CLI najdete v tématu [rozhraní příkazového řádku (CLI) pro azure Command-Line](/cli/azure).

## <a name="azure-roles-for-blobs-and-queues"></a>Role Azure pro objekty BLOB a fronty

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Určení oboru prostředků

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Výpis dostupných rolí Azure

Pokud chcete zobrazit seznam dostupných předdefinovaných rolí Azure pomocí Azure CLI, použijte příkaz [AZ role definition list](/cli/azure/role/definition#az-role-definition-list) :

```azurecli-interactive
az role definition list --out table
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

## <a name="assign-an-azure-role-to-a-security-principal"></a>Přiřazení role Azure k objektu zabezpečení

Pokud chcete k objektu zabezpečení přiřadit roli Azure, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) . Formát příkazu se může lišit v závislosti na rozsahu přiřazení. Následující příklady ukazují, jak přiřadit roli uživateli v různých oborech, ale můžete použít stejný příkaz k přiřazení role k libovolnému objektu zabezpečení.

> [!IMPORTANT]
> Když vytvoříte účet Azure Storage, nebudete automaticky přiřazovat oprávnění pro přístup k datům prostřednictvím služby Azure AD. Pro přístup k datům musíte explicitně přiřadit roli Azure RBAC. Můžete ji přiřadit na úrovni předplatného, skupiny prostředků, účtu úložiště nebo kontejneru nebo fronty.
>
> Pokud je účet úložiště zamčený s Azure Resource Manager zámek jen pro čtení, zabrání zámek rolím role RBAC Azure, které jsou vymezeny pro účet úložiště, nebo do kontejneru dat (kontejner objektů BLOB nebo fronta).

### <a name="container-scope"></a>Rozsah kontejneru

Chcete-li přiřadit obor role k kontejneru, zadejte řetězec obsahující obor kontejneru pro `--scope` parametr. Rozsah kontejneru je ve formátu:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

Následující příklad přiřadí roli **Přispěvatel dat objektu BLOB úložiště** k uživateli vymezenou na úrovni kontejneru. Nezapomeňte nahradit vzorové hodnoty a zástupné hodnoty v závorkách vlastními hodnotami:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Rozsah fronty

Chcete-li přiřadit obor role ke frontě, zadejte řetězec obsahující obor fronty pro `--scope` parametr. Rozsah fronty je ve formátu:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

V následujícím příkladu se uživateli přiřadí role **Přispěvatel dat fronty úložiště** , která je vymezena na úrovni fronty. Nezapomeňte nahradit vzorové hodnoty a zástupné hodnoty v závorkách vlastními hodnotami:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Rozsah účtu úložiště

Pokud chcete přiřadit obor role k účtu úložiště, zadejte rozsah prostředku účtu úložiště pro tento `--scope` parametr. Rozsah účtu úložiště je ve formátu:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Následující příklad ukazuje, jak přiřadit roli **čtečky dat objektů BLOB úložiště** k uživateli na úrovni účtu úložiště. Nezapomeňte nahradit vzorové hodnoty vlastními hodnotami: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Rozsah skupiny prostředků

Pokud chcete přiřadit obor role ke skupině prostředků, zadejte název nebo ID skupiny prostředků pro tento `--resource-group` parametr. Následující příklad přiřadí roli **čtečky dat fronty úložiště** k uživateli na úrovni skupiny prostředků. Nezapomeňte nahradit vzorové hodnoty a zástupné hodnoty v závorkách vlastními hodnotami:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Rozsah předplatného

Pokud chcete přiřadit obor role k předplatnému, zadejte obor pro předplatné daného `--scope` parametru. Rozsah předplatného je ve formátu:

```
/subscriptions/<subscription>
```

Následující příklad ukazuje, jak přiřadit roli **čtečky dat objektů BLOB úložiště** k uživateli na úrovni účtu úložiště. Nezapomeňte nahradit vzorové hodnoty vlastními hodnotami: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Další kroky

- [Přidání nebo odebrání přiřazení rolí Azure pomocí modulu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Použití modulu Azure PowerShell k přiřazení role Azure pro přístup k datům objektů BLOB a front](storage-auth-aad-rbac-powershell.md)
- [Přiřazení role Azure pro přístup k datům objektů blob a front pomocí webu Azure Portal](storage-auth-aad-rbac-portal.md)
