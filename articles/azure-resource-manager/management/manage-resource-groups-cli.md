---
title: Správa skupin prostředků – Azure CLI
description: Pomocí Azure CLI můžete spravovat skupiny prostředků prostřednictvím Azure Resource Manager. Ukazuje, jak vytvořit, vypsat a odstranit skupiny prostředků.
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4a9a4ed4ebba7f6f2470bb9e7000a899ebc26323
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185806"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Správa skupin prostředků Azure Resource Manager pomocí Azure CLI

Naučte se používat Azure CLI s [Azure Resource Manager](overview.md) ke správě skupin prostředků Azure. Informace o správě prostředků Azure najdete v tématu [Správa prostředků Azure pomocí Azure CLI](manage-resources-cli.md).

Další články o správě skupin prostředků:

- [Správa skupin prostředků Azure pomocí Azure Portal](manage-resources-portal.md)
- [Správa skupin prostředků Azure pomocí Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Co je skupina prostředků

Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků. Obecně můžete přidat prostředky, které sdílejí stejný životní cyklus do stejné skupiny prostředků, abyste je mohli snadno nasadit, aktualizovat a odstranit jako skupinu.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků tedy určujete, kde se tato metadata ukládají. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

Skupina prostředků ukládá metadata o prostředcích. Když zadáte umístění pro skupinu prostředků, určíte, kde jsou tato metadata uložená.

## <a name="create-resource-groups"></a>Vytvoření skupin prostředků

Následující příkaz rozhraní příkazového řádku vytvoří skupinu prostředků.

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>Seznam skupin prostředků

Následující skript rozhraní příkazového řádku vypíše skupiny prostředků v rámci vašeho předplatného.

```azurecli-interactive
az group list
```

Jak získat jednu skupinu prostředků:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Odstranění skupin prostředků

Následující skript rozhraní příkazového řádku odstraní skupinu prostředků:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Další informace o tom, jak Azure Resource Manager objednává odstranění prostředků, najdete v tématu [Azure Resource Manager odstranění skupiny prostředků](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Nasazení prostředků do existující skupiny prostředků

Viz [nasazení prostředků do existující skupiny prostředků](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Nasazení skupiny prostředků a prostředků

Můžete vytvořit skupinu prostředků a nasadit prostředky do skupiny pomocí šablony Správce prostředků. Další informace najdete v tématu [Vytvoření skupiny prostředků a nasazení prostředků](../templates/deploy-to-subscription.md#resource-groups).

## <a name="redeploy-when-deployment-fails"></a>Znovu nasadit v případě neúspěchu nasazení

Tato funkce se také označuje jako *vrácení zpět s chybou*. Další informace najdete v tématu [opětovné nasazení v případě neúspěchu nasazení](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Přesunout do jiné skupiny prostředků nebo předplatného

Prostředky ve skupině můžete přesunout do jiné skupiny prostředků. Další informace najdete v tématu [Přesunutí prostředků](manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Uzamčení skupin prostředků

Uzamykání brání jiným uživatelům ve vaší organizaci v neúmyslném odstranění nebo úpravě důležitých prostředků, jako je například předplatné Azure, skupina prostředků nebo prostředek. 

Následující skript zamkne skupinu prostředků, takže skupinu prostředků nejde odstranit.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Následující skript získá všechny zámky pro skupinu prostředků:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

Následující skript odstraní zámek:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](lock-resources.md).

## <a name="tag-resource-groups"></a>Označení skupin prostředků

Můžete použít značky pro skupiny prostředků a prostředky k logické organizaci vašich assetů. Informace najdete v tématu [použití značek k uspořádání prostředků Azure](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Export skupin prostředků do šablon

Po úspěšném nastavení skupiny prostředků možná budete chtít zobrazit šablonu Správce prostředků pro skupinu prostředků. Export šablony nabízí dvě výhody:

- Automatizujte budoucí nasazení řešení, protože šablona obsahuje veškerou kompletní infrastrukturu.
- Podívejte se na JavaScript Object Notation (JSON), která představuje vaše řešení, a Naučte se syntaxí šablony.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Skript zobrazí šablonu v konzole nástroje.  Zkopírujte kód JSON a uložte ho jako soubor.

Funkce Exportovat šablonu nepodporuje export Azure Data Factorych prostředků. Další informace o tom, jak můžete exportovat Data Factory prostředky, najdete [v tématu kopírování nebo klonování datové továrny v Azure Data Factory](../../data-factory/copy-clone-data-factory.md).

Chcete-li exportovat prostředky vytvořené prostřednictvím modelu nasazení Classic, je nutné [je migrovat do modelu nasazení Správce prostředků](../../virtual-machines/migration-classic-resource-manager-overview.md).

Další informace najdete v tématu [Export jednoho a více prostředků do šablony v Azure Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Správa přístupu ke skupinám prostředků

[Řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md) je způsob, jakým můžete spravovat přístup k prostředkům v Azure. Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Další kroky

- Informace o Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](overview.md).
- Další informace o syntaxi šablon Správce prostředků naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](../templates/template-syntax.md).
- Další informace o vývoji šablon najdete v [podrobných kurzech](../index.yml).
- Chcete-li zobrazit schémata šablon Azure Resource Manager, přečtěte si téma [reference šablony](/azure/templates/).