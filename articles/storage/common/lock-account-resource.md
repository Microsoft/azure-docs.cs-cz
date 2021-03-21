---
title: Použít Azure Resource Manager zámek k účtu úložiště
titleSuffix: Azure Storage
description: Naučte se, jak použít Azure Resource Manager zámek k účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9d80c0b8d4d913322c47d1ad278d6dbc033d2409
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620010"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>Použít Azure Resource Manager zámek k účtu úložiště

Microsoft doporučuje uzamknout všechny vaše účty úložiště pomocí Azure Resource Manager zámku, aby se předešlo náhodnému nebo škodlivému odstranění účtu úložiště. Existují dva typy Azure Resource Managerch zámků prostředků:

- Zámek **CannotDelete** zabraňuje uživatelům odstranit účet úložiště, ale umožňuje čtení a úpravu jeho konfigurace.
- Zámek **jen pro čtení** zabraňuje uživatelům odstranit účet úložiště nebo měnit jeho konfiguraci, ale umožňuje čtení konfigurace.

Další informace o uzamčení Azure Resource Manager najdete v tématu [uzamčení prostředků, aby se zabránilo změnám](../../azure-resource-manager/management/lock-resources.md).

> [!IMPORTANT]
> Uzamykání účtu úložiště nechrání data v rámci tohoto účtu při jejich aktualizaci nebo odstranění.

## <a name="configure-an-azure-resource-manager-lock"></a>Konfigurace zámku Azure Resource Manager

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ke konfiguraci zámku účtu úložiště pomocí Azure Portal použijte následující postup:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části **Nastavení** vyberte **zámky**.
1. Vyberte **Přidat**.
1. Zadejte název zámku prostředku a zadejte typ zámku. V případě potřeby přidejte poznámku o zámku.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="Snímek obrazovky, který ukazuje, jak uzamknout účet úložiště pomocí zámku CannotDelete":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete nakonfigurovat zámek účtu úložiště pomocí PowerShellu, nejdřív se ujistěte, že máte nainstalovanou [modul AZ PowerShell](https://www.powershellgallery.com/packages/Az). Dále zavolejte příkaz [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) a určete typ zámku, který chcete vytvořit, jak je znázorněno v následujícím příkladu:

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete nakonfigurovat zámek na účtu úložiště pomocí Azure CLI, zavolejte příkaz [AZ Lock Create](/cli/azure/lock#az_lock_create) a určete typ zámku, který chcete vytvořit, jak je znázorněno v následujícím příkladu:

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>Ověřování operací s daty, když je aktivní zámek jen pro čtení

Když se na účet úložiště použije zámek **jen pro čtení** , pro tento účet úložiště se zablokuje operace [vypsat klíče](/rest/api/storagerp/storageaccounts/listkeys) . Operace se **seznamem klíčů** je operace post protokolu HTTPS a všechny operace post nejsou zabráněno, pokud je pro tento účet nakonfigurovaný zámek **ReadOnly** . Operace **klíče seznamu** vrátí přístupové klíče účtu, které pak můžete použít ke čtení a zápisu do libovolných dat v účtu úložiště.

Pokud má klient v době, kdy se na účet úložiště používá přístupový klíč účtu, přístup k datům, může i nadále používat klíče pro přístup k datům. Klienti, kteří k klíčům nemají přístup, ale budou muset použít přihlašovací údaje Azure Active Directory (Azure AD) k přístupu k datům objektů BLOB nebo front v účtu úložiště.

Uživatelé Azure Portal můžou být ovlivněni při použití zámku **jen pro čtení** , pokud předtím použili data objektů BLOB nebo front na portálu pomocí přístupových klíčů k účtu. Po použití zámku budou uživatelé portálu muset použít přihlašovací údaje Azure AD pro přístup k datům objektů BLOB nebo Queue na portálu. K tomu je potřeba, aby k nim měl uživatel aspoň dvě role RBAC: role Azure Resource Manager Reader minimálně a jednu z Azure Storage rolí přístupu k datům. Další informace najdete v jednom z následujících článků:

- [Vyberte, jak autorizovat přístup k datům objektu BLOB v Azure Portal](../blobs/authorize-data-operations-portal.md)
- [Vyberte, jak autorizovat přístup k datům ve frontě v Azure Portal](../queues/authorize-data-operations-portal.md)

Data v souborech Azure nebo Table service můžou být nepřístupná klientům, kteří k nim dříve přistupovali pomocí klíčů účtu. Osvědčeným postupem je, že pokud musíte použít zámek **jen pro čtení** k účtu úložiště, přesuňte své služby soubory a Table Service Azure do účtu úložiště, který není uzamčený pomocí zámku **jen pro čtení** .

## <a name="next-steps"></a>Další kroky

[Uzamknout prostředky, aby nedocházelo ke změnám](../../azure-resource-manager/management/lock-resources.md)
