---
title: Vytvoření skupin pro správu pro uspořádání prostředků Azure – zásady správného řízení Azure
description: Naučte se vytvářet skupiny pro správu Azure pro správu více prostředků pomocí portálu, Azure PowerShell a Azure CLI.
author: rthorn17
manager: rithorn
ms.service: governance
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 1b53d8a15e6ca1d4fc5a8ed6d58e526fadd12466
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949004"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Vytvoření skupin pro správu pro organizaci a správu prostředků

Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů v různých předplatných. Vytvořte tyto kontejnery, abyste vytvořili efektivní a efektivní hierarchii, která se dá použít s [Azure Policy](../policy/overview.md) a [řízení přístupu na základě rolí Azure](../../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](overview.md).

První skupina pro správu vytvořená v adresáři může trvat až 15 minut, než se dokončí. Při nastavování služby skupiny pro správu v Azure pro váš adresář existují procesy, které se spouštějí poprvé. Po dokončení procesu obdržíte oznámení.

## <a name="create-a-management-group"></a>Vytvoření skupiny pro správu

Skupinu pro správu můžete vytvořit pomocí portálu, PowerShellu nebo rozhraní příkazového řádku Azure CLI. V současné době nemůžete použít šablony Správce prostředků k vytvoření skupin pro správu.

### <a name="create-in-portal"></a>Vytvořit na portálu

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** > **Správa a zásady správného řízení**.

1. Vybrat **cost management + fakturace**

1. Na stránce Cost Management + fakturace – skupiny pro správu vyberte **skupiny pro správu**

1. Vyberte **+ Přidat skupinu pro správu**.

   ![Stránka pro práci se skupinami pro správu](./media/main.png)

1. Vyplňte pole ID skupiny pro správu.

   - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odesílání příkazů v této skupině pro správu. Tento identifikátor není po vytvoření upravitelný, protože se používá v rámci systému Azure k identifikaci této skupiny. [Kořenová skupina pro správu](overview.md#root-management-group-for-each-directory) se automaticky vytvoří s ID, které je Azure Active Directory ID. U všech ostatních skupin pro správu přiřaďte jedinečné ID.
   - Pole Zobrazovaný název je název, který se zobrazí v Azure Portal. Samostatné zobrazované jméno je volitelné pole při vytváření skupiny pro správu a může být kdykoli změněno.  

   ![Podokno možnosti pro vytvoření nové skupiny pro správu](./media/create_context_menu.png)  

1. Vyberte **Uložit**.

### <a name="create-in-powershell"></a>Vytvoření v PowerShellu

Pro prostředí PowerShell použijte rutinu [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) k vytvoření nové skupiny pro správu.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**Název_skupiny** je jedinečný identifikátor, který se vytváří. Toto ID používají jiné příkazy pro odkazování na tuto skupinu a nelze je později změnit.

Chcete-li, aby skupina pro správu v rámci Azure Portal zobrazovala jiný název, přidejte parametr **DisplayName** . Chcete-li například vytvořit skupinu pro správu s názvem skupiny contoso a zobrazovaným názvem "skupina contoso", použijte následující rutinu:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

V předchozích příkladech se nová skupina pro správu vytvoří pod kořenovou skupinou pro správu. Chcete-li určit jinou skupinu pro správu jako nadřazenou, použijte parametr **parentID** .

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Vytvoření v Azure CLI

V případě Azure CLI pomocí příkazu [AZ Account Management-Group Create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) vytvořte novou skupinu pro správu.

```azurecli-interactive
az account management-group create --name Contoso
```

**Název** je jedinečný identifikátor, který se vytváří. Toto ID používají jiné příkazy pro odkazování na tuto skupinu a nelze je později změnit.

Chcete-li, aby skupina pro správu v rámci Azure Portal zobrazovala jiný název, přidejte parametr **Display-Name** . Chcete-li například vytvořit skupinu pro správu s názvem skupiny contoso a zobrazovaným názvem "skupina contoso", použijte následující příkaz:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

V předchozích příkladech se nová skupina pro správu vytvoří pod kořenovou skupinou pro správu. Chcete-li určit jinou skupinu pro správu jako nadřazenou, použijte **nadřazený** parametr a zadejte název nadřazené skupiny.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Další kroky

Další informace o skupinách pro správu najdete v těchto tématech:

- [Vytvoření skupin pro správu pro uspořádání prostředků Azure](create.md)
- [Jak změnit, odstranit nebo spravovat skupiny pro správu](manage.md)
- [Kontrola skupin pro správu v modulu Azure PowerShellch prostředků](/powershell/module/az.resources#resources)
- [Kontrola skupin pro správu v REST API](/rest/api/resources/managementgroups)
- [Kontrola skupin pro správu v Azure CLI](/cli/azure/account/management-group)
