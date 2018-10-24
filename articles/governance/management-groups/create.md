---
title: Vytvoření skupin pro správu k uspořádání prostředků Azure
description: Zjistěte, jak vytvořit skupiny pro správu Azure ke správě více prostředků.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rithorn
ms.openlocfilehash: 6a45ffa13ead40b72fd1a0a3c2696a6e6829a4d5
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956404"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Vytvoření skupin pro správu pro organizaci poskytující prostředky a správu

Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů napříč několika předplatnými. Vytvoření těchto kontejnerů k sestavení účinný a efektivní hierarchie, který lze použít s [Azure Policy](../policy/overview.md) a [řízení přístupu na základě Role Azure](../../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků se skupinami pro správu Azure](overview.md).

První skupiny pro správu, vytvoří se v adresáři může trvat až 15 minut. Existují procesy, které se při prvním přístupu k nastavení služby správy skupin v rámci Azure pro svůj adresář. Po dokončení procesu, dostanete oznámení.

## <a name="create-a-management-group"></a>Vytvořit skupinu pro správu

Skupina pro správu můžete vytvořit pomocí portálu, Powershellu nebo rozhraní příkazového řádku Azure. V současné době nelze pomocí šablon Resource Manageru k vytvoření skupin pro správu.

### <a name="create-in-portal"></a>Vytvořit na portálu

1. Přihlaste se na [Azure Portal](http://portal.azure.com).

1. Vyberte **všechny služby** > **skupin pro správu**.

1. Na hlavní stránce vyberte **skupiny pro správu nové**.

   ![Hlavní skupiny](./media/main.png)

1. Vyplňte pole ID skupiny správy.

   - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odeslání příkazů na tuto skupinu pro správu. Tento identifikátor není po vytvoření upravit, protože se používá v rámci systému Azure k identifikaci této skupiny.
   - Pole zobrazovaného názvu je název, který se zobrazí na portálu Azure portal. Samostatné zobrazované jméno je volitelné pole při vytváření rozhraní pro správu skupin a můžete kdykoli změnit.  

   ![Vytvořit](./media/create_context_menu.png)  

1. Vyberte **Uložit**.

### <a name="create-in-powershell"></a>Vytvořit v prostředí PowerShell

V rámci prostředí PowerShell použijte rutinu New-AzureRmManagementGroup:

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso'
```

**GroupName** vytváří jedinečný identifikátor. Toto ID používá další příkazy odkazují na tuto skupinu a není možné později změnit.

Pokud jste chtěli zobrazit na webu Azure portal jiný název skupiny pro správu, měli byste přidat **DisplayName** parametr s řetězcem. Například Kdybyste chtěli vytvořit skupinu pro správu s GroupName Contoso a zobrazovaný název "Skupina společnosti Contoso", můžete využít následující rutinu:

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId 'ContosoTenant'
```

Použití **ParentId** vytvořit parametr má tuto skupinu pro správu v rámci různých pro správu.

### <a name="create-in-azure-cli"></a>Vytvořit v Azure CLI

V Azure CLI, použijte az účet-skupiny pro správu, vytvořit příkaz.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

## <a name="next-steps"></a>Další postup

Další informace o řešeních pro správu najdete v následujících tématech:

- [Uspořádání prostředků se skupinami pro správu Azure](overview.md)
- [Jak měnit, odstraňovat nebo spravovat skupiny pro správu](manage.md)
- [Instalace modulu Azure PowerShellu](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Specifikace rozhraní REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Nainstalujte rozšíření Azure CLI](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)
