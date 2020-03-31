---
title: Vytvoření skupin pro správu pro uspořádání prostředků – Zásady správného řízení Azure
description: Zjistěte, jak vytvořit skupiny pro správu Azure pro správu více prostředků pomocí portálu, Azure PowerShellu a Azure CLI.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: d9bb2e82404c0188094298f40da3346ee132eec3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436535"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Vytvoření skupin pro správu pro organizaci a správu prostředků

Skupiny pro správu jsou kontejnery, které vám pomohou spravovat přístup, zásady a dodržování předpisů napříč více předplatnými. Vytvořte tyto kontejnery k vytvoření efektivní a efektivní hierarchie, kterou lze použít s [Azure Policy](../policy/overview.md) a Azure Role Based [Access Controls](../../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete [v tématu Uspořádání prostředků pomocí skupin pro správu Azure](overview.md).

Dokončení první skupiny pro správu vytvořené v adresáři může trvat až 15 minut. Existují procesy, které se spustí poprvé nastavit službu skupin pro správu v rámci Azure pro váš adresář. Po dokončení procesu obdržíte oznámení. Další informace naleznete [v tématu počáteční nastavení skupin pro správu](./overview.md#initial-setup-of-management-groups). 

## <a name="create-a-management-group"></a>Vytvoření skupiny pro správu

Každý uživatel Azure AD v tenantovi můžete vytvořit skupinu pro správu bez oprávnění pro zápis skupiny pro správu přiřazené tomuto uživateli.  Tato nová skupina pro správu bude podřízenou skupinou Root Management Group a tvůrce bude mít přiřazení role Vlastník. Služba skupiny pro správu umožňuje tuto schopnost tak, aby přiřazení rolí nejsou potřeba na kořenové úrovni. Žádní uživatelé nemají přístup ke skupině root management group při jeho vytvoření.  Abychom se vyhnuli překážkám při hledání globálních správců Azure AD, aby mohli začít používat skupiny pro správu, povolujeme vytváření počátečních skupin pro správu na kořenové úrovni.      

Skupinu pro správu můžete vytvořit pomocí portálu, PowerShellu nebo Azure CLI. V současné době nelze k vytváření skupin pro správu používat šablony Správce prostředků.

### <a name="create-in-portal"></a>Vytvořit na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **Správa všech služeb** > **+ zásady správného řízení**.

1. Vybrat **řízení nákladů + fakturaci**

1. Na stránce Řízení nákladů + Fakturace – skupiny pro správu vyberte **Skupiny pro správu.**

1. Vyberte **+ Přidat skupinu pro správu**.

   ![Stránka pro práci se skupinami pro správu](./media/main.png)

1. Vyplňte pole ID skupiny pro správu.

   - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odesílání příkazů v této skupině pro správu. Tento identifikátor není upravitelný po vytvoření, protože se používá v celém systému Azure k identifikaci této skupiny. [Kořenová skupina pro správu](overview.md#root-management-group-for-each-directory) se automaticky vytvoří s ID, které je ID služby Azure Active Directory. Pro všechny ostatní skupiny pro správu přiřaďte jedinečné ID.
   - Pole se zobrazovatým názvem je název, který se zobrazí na webu Azure Portal. Samostatný zobrazovaný název je volitelné pole při vytváření skupiny pro správu a lze jej kdykoli změnit.  

   ![Podokno Možností pro vytvoření nové skupiny pro správu](./media/create_context_menu.png)  

1. Vyberte **Uložit**.

### <a name="create-in-powershell"></a>Vytvořit v PowerShellu

V prostředí PowerShell použijte rutinu [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) k vytvoření nové skupiny pro správu.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**Název_skupiny** je jedinečný identifikátor, který se vytváří. Toto ID se používá jinými příkazy k odkazování na tuto skupinu a nelze jej později změnit.

Pokud chcete, aby skupina pro správu zobrazovala jiný název na webu Azure Portal, přidejte parametr **DisplayName.** Chcete-li například vytvořit skupinu pro správu s názvem Skupiny Contoso a zobrazovaným názvem "Contoso Group", použijte následující rutinu:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

V předchozích příkladech je nová skupina pro správu vytvořena pod kořenovou skupinou pro správu. Chcete-li jako nadřazenou skupinu pro správu určit jinou skupinu pro správu, použijte parametr **ParentId.**

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Vytvořit v Azure CLI

Pro Azure CLI použijte příkaz [vytvořit skupinu účtů az](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) k vytvoření nové skupiny pro správu.

```azurecli-interactive
az account management-group create --name Contoso
```

**Název** je jedinečný identifikátor, který se vytváří. Toto ID se používá jinými příkazy k odkazování na tuto skupinu a nelze jej později změnit.

Pokud chcete, aby skupina pro správu zobrazovala jiný název na webu Azure Portal, přidejte parametr **display-name.** Chcete-li například vytvořit skupinu pro správu s názvem Skupiny Contoso a zobrazovaným názvem "Contoso Group", použijte následující příkaz:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

V předchozích příkladech je nová skupina pro správu vytvořena pod kořenovou skupinou pro správu. Chcete-li jako nadřazenou skupinu pro správu určit jinou skupinu pro správu, použijte **nadřazený** parametr a zadejte název nadřazené skupiny.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Další kroky

Další informace o řešeních pro správu najdete v následujících tématech:

- [Vytváření skupin pro správu pro organizaci prostředků Azure](create.md)
- [Jak měnit, odstraňovat nebo spravovat skupiny pro správu](manage.md)
- [Kontrola skupin pro správu v modulu Prostředky Azure PowerShellu](/powershell/module/az.resources#resources)
- [Kontrola skupin pro správu v rozhraní REST API](/rest/api/resources/managementgroups)
- [Kontrola skupin pro správu v Azure CLI](/cli/azure/account/management-group)
