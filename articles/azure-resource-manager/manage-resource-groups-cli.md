---
title: Správa skupin v Azure Resource Manageru pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Pomocí Azure CLI ke správě skupin Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 9245d4c01816d26e262b1c8bd35015e2a6a9558c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550542"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Správa skupin prostředků Azure Resource Manageru pomocí rozhraní příkazového řádku Azure

Další informace o použití Azure CLI využijte [Azure Resource Manageru](resource-group-overview.md) ke správě skupin prostředků Azure. Správa prostředků Azure, najdete v části [Správa prostředků Azure pomocí Azure CLI](./manage-resources-cli.md).

Další články o správě skupin prostředků:

- [Správa skupin prostředků Azure pomocí webu Azure portal](./manage-resources-portal.md)
- [Správa skupin prostředků Azure pomocí Azure Powershellu](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Co je skupina prostředků

Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků. Obecně platí přidejte prostředky, které sdílejí stejný životní cyklus do stejné skupiny prostředků, takže můžete snadno nasadit, aktualizovat a odstranit jako skupina.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků tedy určujete, kde se tato metadata ukládají. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků, určujete, kde se tato metadata ukládají.

## <a name="create-resource-groups"></a>Vytvoření skupiny prostředků

Následující skript rozhraní příkazového řádku vytvoří skupinu prostředků a poté zobrazí skupina prostředků.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Seznam skupin prostředků

Následující skript rozhraní příkazového řádku vypíše skupinu prostředků v rámci vašeho předplatného.

```azurecli-interactive
az group list
```

Pokud chcete získat jednu skupinu prostředků:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Odstranění skupiny prostředků

Následující skript rozhraní příkazového řádku odstraní skupinu prostředků:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Další informace o tom, jak Azure Resource Manageru orders odstranění prostředků najdete v tématu [odstranění skupiny prostředků Azure Resource Manageru](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Nasazení prostředků do existující skupiny prostředků

Zobrazit [nasadit prostředky do existující skupiny prostředků](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Nasazení skupiny prostředků a prostředků

Můžete vytvořit skupinu prostředků a nasazení prostředků do skupiny s použitím šablony Resource Manageru. Další informace najdete v tématu [vytvořte skupinu prostředků a nasazení prostředků](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Opětovné nasazení při nasazení se nezdaří

Tato funkce se také označuje jako *vrácení zpět při chybě*. Další informace najdete v tématu [opětovné nasazení při selhání nasazení](./resource-group-template-deploy-cli.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Přesunout do jiné skupiny prostředků nebo předplatného

Prostředky ve skupině můžete přesunout do jiné skupiny prostředků. Další informace najdete v tématu [přesunutí prostředků](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Skupiny prostředků zámku

Zamknutí zabrání ostatním uživatelům ve vaší organizaci omylem odstranit nebo upravit důležité prostředky, jako je předplatné Azure, skupinu prostředků nebo prostředek. 

Následující skript Zamkne skupinu prostředků, takže nelze odstranit skupinu prostředků.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Následující skript načte všech zámků pro skupinu prostředků:

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

Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Značka skupiny prostředků

Můžete provést značky u prostředků a skupin prostředků logicky tak uspořádat vaše prostředky. Informace najdete v tématu [použití značek k uspořádání prostředků Azure](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Export skupiny prostředků do šablon

Po úspěšném nastavení vaší skupiny prostředků, můžete zobrazit šablony Resource Manageru pro skupinu prostředků. Export šablony nabízí dvě výhody:

- Budoucí nasazení řešení automatizace, protože šablona obsahuje kompletní infrastrukturu.
- Přečtěte si syntaxi šablony pohledem na zápisu JSON (JavaScript Object), který představuje vaše řešení.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Tento skript zobrazí v konzole pro šablonu.  Zkopírujte JSON a uložte jako soubor.

Další informace najdete v tématu [Export skupiny prostředků](./manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="manage-access-to-resource-groups"></a>Správa přístupu ke skupinám prostředků

[Řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) je způsob správy přístupu k prostředkům v Azure. Další informace najdete v tématu [správě přístupu pomocí RBAC a rozhraní příkazového řádku Azure](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Další postup

- Další Azure Resource Manageru najdete v tématu [přehled Azure Resource Manageru](./resource-group-overview.md).
- Seznamte se se syntaxí šablony Resource Manageru, najdete v článku [Princip struktury a syntaxe šablon Azure Resource Manageru](./resource-group-authoring-templates.md).
- Zjistěte, jak vyvíjet šablony, najdete v článku [podrobné kurzy](/azure/azure-resource-manager/).
- Schémata šablon Azure Resource Manageru najdete v tématu [referenčními informacemi k šablonám](/azure/templates/).