---
title: Správa skupin prostředků – Azure CLI
description: Pomocí azure cli můžete spravovat své skupiny prostředků prostřednictvím Správce prostředků Azure. Ukazuje, jak vytvořit, vypsat a odstranit skupiny prostředků.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 7face572f545153ea92efbdb345bbaabda5dd126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248330"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Správa skupin prostředků Azure Resource Manager pomocí Azure CLI

Zjistěte, jak používat Azure CLI s [Azure Resource Manager](overview.md) ke správě skupin prostředků Azure. Správa prostředků Azure najdete v [tématu Správa prostředků Azure pomocí Azure CLI](manage-resources-cli.md).

Další články o správě skupin zdrojů:

- [Správa skupin prostředků Azure pomocí portálu Azure](manage-resources-portal.md)
- [Správa skupin prostředků Azure pomocí Azure PowerShellu](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Co je skupina prostředků

Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků. Obecně přidejte prostředky, které sdílejí stejný životní cyklus, do stejné skupiny prostředků, abyste je mohli snadno nasadit, aktualizovat a odstranit jako skupinu.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků tedy určujete, kde se tato metadata ukládají. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

Skupina prostředků ukládá metadata o prostředcích. Když zadáte umístění pro skupinu prostředků, určujete, kde jsou tato metadata uložena.

## <a name="create-resource-groups"></a>Vytvořit skupiny prostředků

Následující skript příkazového příkazu vytvoří skupinu prostředků a zobrazí skupinu prostředků.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Seznam skupin prostředků

V následujícím skriptu příkazového příkazu jsou uvedeny skupiny prostředků v rámci vašeho předplatného.

```azurecli-interactive
az group list
```

Chcete-li získat jednu skupinu prostředků:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Odstranění skupin prostředků

Následující skript příkazového příkazového příkazu odstraní skupinu prostředků:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Další informace o tom, jak Azure Resource Manager objednávky odstranění prostředků, najdete v [tématu Odstranění skupiny prostředků Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Nasazení prostředků do existující skupiny prostředků

Viz [Nasazení prostředků do existující skupiny prostředků](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Nasazení skupiny prostředků a prostředků

Skupinu prostředků můžete vytvořit a nasadit prostředky do skupiny pomocí šablony Správce prostředků. Další informace naleznete v [tématu Vytvoření skupiny prostředků a nasazení prostředků](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Opětovné nasazení při selhání nasazení

Tato funkce je také známá jako *vrácení zpět při chybě*. Další informace naleznete [v tématu Znovu nasadit při selhání nasazení](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Přechod na jinou skupinu prostředků nebo předplatné

Prostředky ve skupině můžete přesunout do jiné skupiny prostředků. Další informace naleznete v tématu [Přesunutí prostředků](manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Uzamčení skupin prostředků

Uzamčení zabrání ostatním uživatelům ve vaší organizaci v náhodném odstranění nebo úpravě kritických prostředků, jako je předplatné Azure, skupina prostředků nebo prostředek. 

Následující skript uzamkne skupinu prostředků, takže skupinu prostředků nelze odstranit.

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

## <a name="tag-resource-groups"></a>Označit skupiny prostředků

Značky můžete použít na skupiny prostředků a prostředky, které logicky uspořádají prostředky. Další informace najdete [v tématu Použití značek k uspořádání prostředků Azure](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Export skupin prostředků do šablon

Po úspěšném nastavení skupiny prostředků můžete chtít zobrazit šablonu Správce prostředků pro skupinu prostředků. Export šablony nabízí dvě výhody:

- Automatizujte budoucí nasazení řešení, protože šablona obsahuje veškerou úplnou infrastrukturu.
- Naučte se syntaxi šablony tak, že se podíváte na JavaScript Object Notation (JSON), který představuje vaše řešení.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Skript zobrazí šablonu na konzoli.  Zkopírujte JSON a uložte jej jako soubor.

Funkce šablony exportu nepodporuje export prostředků Azure Data Factory. Informace o tom, jak exportovat prostředky Data Factory, najdete v [tématu Kopírování nebo klonování datové továrny v Azure Data Factory](https://aka.ms/exportTemplateViaAdf).

Chcete-li exportovat prostředky vytvořené prostřednictvím klasického modelu nasazení, musíte [je migrovat do modelu nasazení Správce prostředků](https://aka.ms/migrateclassicresourcetoarm).

Další informace najdete [v tématu Export jednoho a více prostředků do šablony na webu Azure Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Správa přístupu ke skupinám prostředků

[Řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/overview.md) je způsob, jakým spravujete přístup k prostředkům v Azure. Další informace naleznete [v tématu Správa přístupu pomocí RBAC a Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Další kroky

- Pokud se chcete naučit Správce prostředků Azure, přečtěte si [přehled správce prostředků Azure](overview.md).
- Syntaxe syntaxe šablony Správce prostředků najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](../templates/template-syntax.md).
- Chcete-li se dozvědět, jak vyvíjet šablony, podívejte se na [podrobné výukové programy](/azure/azure-resource-manager/).
- Pokud chcete zobrazit schémata šablon Azure Resource Manager, přečtěte si [odkaz na šablonu](/azure/templates/).