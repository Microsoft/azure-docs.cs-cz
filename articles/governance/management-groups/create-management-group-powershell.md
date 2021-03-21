---
title: 'Rychlý Start: Vytvoření skupiny pro správu pomocí Azure PowerShell'
description: V tomto rychlém startu použijete Azure PowerShell k vytvoření skupiny pro správu, která slouží k uspořádání prostředků do hierarchie prostředků.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 216cfeb6e1389793afcfd27d8785a5f912db2c97
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592513"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>Rychlý Start: Vytvoření skupiny pro správu pomocí Azure PowerShell

Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů v různých předplatných. Vytvořte tyto kontejnery, abyste vytvořili efektivní a efektivní hierarchii, která se dá použít s [Azure Policy](../policy/overview.md) a [řízení přístupu na základě rolí Azure](../../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](overview.md).

První skupina pro správu vytvořená v adresáři může trvat až 15 minut, než se dokončí. Při nastavování služby skupiny pro správu v Azure pro váš adresář existují procesy, které se spouštějí poprvé. Po dokončení procesu obdržíte oznámení. Další informace najdete v tématu [počáteční nastavení skupin pro správu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Předpoklady

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Než začnete, ujistěte se, že je nainstalovaná nejnovější verze Azure PowerShell. Podrobné informace najdete v tématu [instalace Azure PowerShell modulu](/powershell/azure/install-az-ps) .

- Libovolný uživatel Azure AD v tenantovi může vytvořit skupinu pro správu bez oprávnění k zápisu skupiny pro správu, která jsou přiřazená tomuto uživateli, pokud není povolena [ochrana hierarchie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Tato nová skupina pro správu se stal podřízenou skupinou kořenové skupiny pro správu nebo [výchozí skupině pro správu](./how-to/protect-resource-hierarchy.md#setting---default-management-group) a autorovi je přiděleno přiřazení role "vlastník". Služba skupiny pro správu umožňuje tuto možnost, takže přiřazení rolí není nutné na kořenové úrovni. Žádní uživatelé nemají přístup ke kořenové skupině pro správu při jejím vytvoření. Aby nedocházelo k tomu, že by globální Správci služby Azure AD mohli začít používat skupiny pro správu, umožníme vytváření počátečních skupin pro správu na kořenové úrovni.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Vytvořit v Azure PowerShell

Pro prostředí PowerShell použijte rutinu [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) k vytvoření nové skupiny pro správu. V tomto příkladu je skupina pro správu **název_skupiny** _Contoso_.

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat skupinu pro správu vytvořenou výše, použijte rutinu [Remove-AzManagementGroup](/powershell/module/az.resources/remove-azmanagementgroup) :

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili skupinu pro správu, která bude organizovat svou hierarchii prostředků. Skupina pro správu může obsahovat předplatná nebo jiné skupiny pro správu.

Pokud chcete získat další informace o skupinách pro správu a o tom, jak spravovat hierarchii prostředků, pokračujte tady:

> [!div class="nextstepaction"]
> [Správa prostředků pomocí skupin pro správu](./manage.md)