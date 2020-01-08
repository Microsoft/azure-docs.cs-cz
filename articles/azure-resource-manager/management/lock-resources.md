---
title: Uzamknout prostředky, aby nedocházelo ke změnám
description: Zabrání uživatelům aktualizovat nebo odstraňovat důležité prostředky Azure tím, že použije zámek pro všechny uživatele a role.
ms.topic: conceptual
ms.date: 05/14/2019
ms.openlocfilehash: b7c6c7980f12e7f9015f4504f461733100b14ea8
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644337"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Uzamknout prostředky, aby nedocházelo k neočekávaným změnám

Jako správce můžete potřebovat uzamknout předplatné, skupinu prostředků nebo prostředek, abyste ostatním uživatelům v organizaci zabránili v náhodném odstranění nebo úpravě důležitých prostředků. Zámek můžete nastavit na úroveň **CanNotDelete** nebo **ReadOnly**. Na portálu se zámky nazývají **Delete** a **jen pro čtení** .

* **CanNotDelete** znamená, že autorizovaní uživatelé můžou pořád číst a upravovat prostředek, ale nemůžou prostředek odstranit. 
* **ReadOnly** znamená, že autorizovaní uživatelé můžou číst prostředek, ale nemůžou prostředek odstranit ani aktualizovat. Použití tohoto zámku je podobné jako omezení všech autorizovaných uživatelů na oprávnění udělené rolí **Čtenář** . 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Jak se používají zámky

Když použijete zámek v nadřazeném oboru, všechny prostředky v tomto oboru zdědí stejný zámek. I prostředky, které později přidáte, zdědí zámek nadřazeného objektu. Nejvíce omezující zámek v dědičnosti má přednost.

Na rozdíl od řízení přístupu na základě role slouží zámky správy k nastavení daného omezení u všech uživatelů a rolí. Další informace o nastavení oprávnění pro uživatele a role najdete v tématu [Access Control na základě rolí v Azure](../../role-based-access-control/role-assignments-portal.md).

Zámky služby Resource Manager se vztahují jen na operace, které probíhají v rovině správy, tedy operace odesílané na `https://management.azure.com`. Tyto zámky nijak neomezují, jak prostředky vykonávají své vlastní funkce. Omezené jsou změny prostředků, ale ne jejich operace. Například zámek jen pro čtení na SQL Database zabrání v odstranění nebo změně databáze. Nebrání v vytváření, aktualizaci nebo odstraňování dat v databázi. Datové transakce jsou povoleny, protože tyto operace nejsou odesílány do `https://management.azure.com`.

Použití **ReadOnly** může vést k neočekávaným výsledkům, protože některé operace, které se nejeví pro úpravu prostředku, skutečně vyžadují akce, které jsou blokovány zámkem. Zámek **ReadOnly** lze použít u prostředku nebo skupiny prostředků obsahující prostředek. Mezi běžné příklady operací, které jsou blokovány zámkem **jen pro čtení** , patří:

* Zámek **jen pro čtení** v účtu úložiště znemožní všem uživatelům zobrazovat seznam klíčů. Operace výpisu klíčů se zpracovává prostřednictvím požadavku POST, protože vrácené klíče jsou k dispozici pro operace zápisu.

* Zámek **jen pro čtení** u prostředku App Service zabraňuje tomu, aby aplikace Visual Studio Průzkumník serveru zobrazování souborů pro daný prostředek, protože tato interakce vyžaduje přístup pro zápis.

* Zámek **jen pro čtení** u skupiny prostředků, která obsahuje virtuální počítač, zabrání všem uživatelům v spuštění nebo restartování virtuálního počítače. Tyto operace vyžadují požadavek POST.

## <a name="who-can-create-or-delete-locks"></a>Kdo může vytvářet nebo odstraňovat zámky
Chcete-li vytvořit nebo odstranit zámky pro správu, je nutné mít přístup k akcím `Microsoft.Authorization/*` nebo `Microsoft.Authorization/locks/*`. Z předdefinovaných rolí má tyto akce povolené pouze **vlastník** a **správce uživatelských přístupů**.

## <a name="managed-applications-and-locks"></a>Spravované aplikace a zámky

Některé služby Azure, například Azure Databricks, využívají [spravované aplikace](../managed-applications/overview.md) k implementaci služby. V takovém případě služba vytvoří dvě skupiny prostředků. Jedna skupina prostředků obsahuje přehled služby a není uzamčena. Další skupina prostředků obsahuje infrastrukturu pro službu a je uzamčená.

Pokud se pokusíte odstranit skupinu prostředků infrastruktury, zobrazí se chyba s oznámením, že skupina prostředků je uzamčená. Pokud se pokusíte odstranit zámek pro skupinu prostředků infrastruktury, zobrazí se chyba oznamující, že zámek nelze odstranit, protože je vlastněn systémovou aplikací.

Místo toho odstraňte službu, která také odstraní skupinu prostředků infrastruktury.

U spravovaných aplikací vyberte službu, kterou jste nasadili.

![Vybrat službu](./media/lock-resources/select-service.png)

Všimněte si, že služba obsahuje odkaz na **spravovanou skupinu prostředků**. Tato skupina prostředků obsahuje infrastrukturu a je uzamčená. Nedá se odstranit přímo.

![Zobrazit spravovanou skupinu](./media/lock-resources/show-managed-group.png)

Chcete-li odstranit vše pro službu včetně uzamčené skupiny prostředků infrastruktury, vyberte možnost **Odstranit** pro službu.

![Odstranit službu](./media/lock-resources/delete-service.png)

## <a name="portal"></a>Portál
[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Šablona

Při použití šablony Správce prostředků k nasazení zámku použijte v závislosti na rozsahu zámku jiné hodnoty pro název a typ.

Při použití zámku na **prostředek**použijte následující formáty:

* název – `{resourceName}/Microsoft.Authorization/{lockName}`
* typ – `{resourceProviderNamespace}/{resourceType}/providers/locks`

Při použití zámku u **skupiny prostředků** nebo **předplatného**použijte následující formáty:

* název – `{lockName}`
* typ – `Microsoft.Authorization/locks`

Následující příklad ukazuje šablonu, která vytvoří plán služby App Service, web a na webu zámek. Typ prostředku zámku je typ prostředku, který se má uzamknout a **/providers/Locks**. Název zámku se vytvoří zřetězením názvu prostředku s **/Microsoft.Authorization/** a názvem zámku.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Příklad nastavení zámku pro skupinu prostředků najdete v tématu [Vytvoření skupiny prostředků a její uzamčení](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Nasazené prostředky můžete uzamknout pomocí Azure PowerShell pomocí příkazu [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) .

Chcete-li uzamknout prostředek, zadejte název prostředku, jeho typ prostředku a název skupiny prostředků.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Chcete-li uzamknout skupinu prostředků, zadejte název skupiny prostředků.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

K získání informací o zámku použijte [příkaz Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Pokud chcete získat všechny zámky ve vašem předplatném, použijte:

```azurepowershell-interactive
Get-AzResourceLock
```

Pro získání všech zámků pro určitý prostředek použijte:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Pokud chcete získat všechny zámky pro skupinu prostředků, použijte:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

K odstranění zámku použijte:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Nasazené prostředky pomocí Azure CLI zamknete pomocí příkazu [AZ Lock Create](/cli/azure/lock#az-lock-create) .

Chcete-li uzamknout prostředek, zadejte název prostředku, jeho typ prostředku a název skupiny prostředků.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Chcete-li uzamknout skupinu prostředků, zadejte název skupiny prostředků.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Pokud chcete získat informace o zámku, použijte příkaz [AZ Lock list](/cli/azure/lock#az-lock-list). Pokud chcete získat všechny zámky ve vašem předplatném, použijte:

```azurecli
az lock list
```

Pro získání všech zámků pro určitý prostředek použijte:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Pokud chcete získat všechny zámky pro skupinu prostředků, použijte:

```azurecli
az lock list --resource-group exampleresourcegroup
```

K odstranění zámku použijte:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>Rozhraní REST API
Nasazené prostředky můžete uzamknout pomocí [REST API pro zámky pro správu](https://docs.microsoft.com/rest/api/resources/managementlocks). REST API umožňuje vytvářet a odstraňovat zámky a načítat informace o stávajících zámkích.

Chcete-li vytvořit zámek, spusťte příkaz:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Rozsahem může být předplatné, skupina prostředků nebo prostředek. Název zámku je bez ohledu na to, kam chcete volat zámek. V případě verze API-Version použijte **2016-09-01**.

V žádosti zahrňte objekt JSON, který určuje vlastnosti zámku.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Další kroky
* Další informace o logické organizaci prostředků najdete v tématu [použití značek k uspořádání prostředků](tag-resources.md) .
* Můžete použít omezení a konvence v rámci předplatného pomocí přizpůsobených zásad. Další informace najdete v tématu [Co je Azure Policy?](../../governance/policy/overview.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).

