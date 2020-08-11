---
title: Vytvoření skupin pro správu pro uspořádání prostředků – zásady správného řízení Azure
description: Naučte se vytvářet skupiny pro správu Azure pro správu více prostředků pomocí portálu, Azure PowerShell a Azure CLI.
ms.date: 08/10/2020
ms.topic: conceptual
ms.openlocfilehash: 0739eafdf4cf41195b189a8496c331ab470bbec0
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056631"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Vytvoření skupin pro správu pro organizaci a správu prostředků

Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů v různých předplatných. Vytvořte tyto kontejnery, abyste vytvořili efektivní a efektivní hierarchii, která se dá použít s [Azure Policy](../policy/overview.md) a [řízení přístupu na základě rolí Azure](../../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](overview.md).

První skupina pro správu vytvořená v adresáři může trvat až 15 minut, než se dokončí. Při nastavování služby skupiny pro správu v Azure pro váš adresář existují procesy, které se spouštějí poprvé. Po dokončení procesu obdržíte oznámení. Další informace najdete v tématu [počáteční nastavení skupin pro správu](./overview.md#initial-setup-of-management-groups).

## <a name="create-a-management-group"></a>Vytvoření skupiny pro správu

Libovolný uživatel služby Azure AD v tenantovi může vytvořit skupinu pro správu bez oprávnění k zápisu skupiny pro správu přiřazeného tomuto uživateli. Tato nová skupina pro správu bude podřízenou skupinou kořenové skupiny pro správu a tvůrci bude přiřazeno přiřazení role "vlastník". Služba skupiny pro správu umožňuje tuto možnost, takže přiřazení rolí není nutné na kořenové úrovni. Žádní uživatelé nemají přístup ke kořenové skupině pro správu při jejím vytvoření. Aby nedocházelo k tomu, že by globální Správci služby Azure AD mohli začít používat skupiny pro správu, umožníme vytváření počátečních skupin pro správu v kořenu.  
obsah.

Skupinu pro správu můžete vytvořit pomocí portálu, [Azure Resource Manager šablony](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group), PowerShellu nebo rozhraní příkazového řádku Azure CLI.

### <a name="create-in-portal"></a>Vytvořit na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby**  >  **Správa a zásady správného řízení**.

1. Vyberte **skupiny pro správu**.

1. Vyberte **+ Přidat skupinu pro správu**.

   :::image type="content" source="./media/main.png" alt-text="Stránka pro práci se skupinami pro správu" border="false":::

1. Vyplňte pole ID skupiny pro správu.

   - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odesílání příkazů v této skupině pro správu. Tento identifikátor není po vytvoření upravitelný, protože se používá v rámci systému Azure k identifikaci této skupiny. [Kořenová skupina pro správu](overview.md#root-management-group-for-each-directory) se automaticky vytvoří s ID, které je Azure Active Directory ID. U všech ostatních skupin pro správu přiřaďte jedinečné ID.
   - Pole Zobrazovaný název je název, který se zobrazí v Azure Portal. Samostatné zobrazované jméno je volitelné pole při vytváření skupiny pro správu a dá se změnit kdykoli  
     interval.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Podokno možnosti pro vytvoření nové skupiny pro správu" border="false":::

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

Další informace o řešeních pro správu najdete v následujících tématech:

- [Vytváření skupin pro správu pro organizaci prostředků Azure](./create.md)
- [Jak měnit, odstraňovat nebo spravovat skupiny pro správu](./manage.md)
- [Kontrola skupin pro správu v modulu Prostředky Azure PowerShellu](/powershell/module/az.resources#resources)
- [Kontrola skupin pro správu v rozhraní REST API](/rest/api/resources/managementgroups)
- [Kontrola skupin pro správu v Azure CLI](/cli/azure/account/management-group)
