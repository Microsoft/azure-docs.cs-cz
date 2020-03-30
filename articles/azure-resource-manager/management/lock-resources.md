---
title: Uzamčení prostředků, aby se zabránilo změnám
description: Zabraňte uživatelům v aktualizaci nebo odstranění důležitých prostředků Azure použitím zámku pro všechny uživatele a role.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 70fb189adb634b7ac24afe7cc8b94738117da5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274005"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Zamknutí prostředků, aby se zabránilo neočekávaným změnám

Jako správce můžete potřebovat uzamknout předplatné, skupinu prostředků nebo prostředek, abyste ostatním uživatelům v organizaci zabránili v náhodném odstranění nebo úpravě důležitých prostředků. Zámek můžete nastavit na úroveň **CanNotDelete** nebo **ReadOnly**. Na portálu zámky se nazývají **Odstranit** a **jen pro čtení** v uvedeném pořadí.

* **CanNotDelete** znamená, že oprávnění uživatelé mohou stále číst a upravovat prostředek, ale nemohou prostředek odstranit. 
* **Jen pro čtení** znamená, že oprávnění uživatelé mohou číst prostředek, ale nemohou prostředek odstranit ani aktualizovat. Použití tohoto zámku je podobné omezení všech oprávněných uživatelů na oprávnění udělená rolí **Čtečka.**

## <a name="how-locks-are-applied"></a>Jak se používají zámky

Při použití zámku v nadřazeném oboru, všechny prostředky v rámci tohoto oboru dědí stejný zámek. Dokonce i prostředky, které přidáte později dědí zámek od nadřazeného objektu. Nejvíce omezující zámek v dědičnosti má přednost.

Na rozdíl od řízení přístupu na základě role slouží zámky správy k nastavení daného omezení u všech uživatelů a rolí. Informace o nastavení oprávnění pro uživatele a role najdete v [tématu Řízení přístupu na základě rolí Azure](../../role-based-access-control/role-assignments-portal.md).

Zámky služby Resource Manager se vztahují jen na operace, které probíhají v rovině správy, tedy operace odesílané na `https://management.azure.com`. Tyto zámky nijak neomezují, jak prostředky vykonávají své vlastní funkce. Omezené jsou změny prostředků, ale ne jejich operace. Například zámek Jen pro čtení v databázi SQL zabrání odstranění nebo úpravě databáze. Nebrání ve vytváření, aktualizování nebo odstraňování dat v databázi. Datové transakce jsou povolené, protože tyto operace se neodesílají na `https://management.azure.com`.

Použití **ReadOnly** může vést k neočekávaným výsledkům, protože některé operace, které se nezdají změnit prostředek skutečně vyžadují akce, které jsou blokovány zámek. Zámek **Jen pro čtení** lze použít pro prostředek nebo na skupinu prostředků obsahující prostředek. Některé běžné příklady operací, které jsou blokovány zámek **Jen pro čtení** jsou:

* Zámek **Jen pro čtení** na účtu úložiště zabrání všem uživatelům v seznamu klíčů. Operace výpisu klíčů se zpracovává prostřednictvím požadavku POST, protože vrácené klíče jsou k dispozici pro operace zápisu.

* Zámek **ReadOnly** na prostředek služby App Service brání Průzkumníku serveru Visual Studio v zobrazení souborů pro prostředek, protože tato interakce vyžaduje přístup pro zápis.

* Zámek **Jen pro čtení** ve skupině prostředků, která obsahuje virtuální počítač, zabrání všem uživatelům ve spuštění nebo restartování virtuálního počítače. Tyto operace vyžadují požadavek POST.

## <a name="who-can-create-or-delete-locks"></a>Kdo může vytvářet nebo odstraňovat zámky

Chcete-li vytvořit nebo odstranit zámky `Microsoft.Authorization/*` správy, musíte mít přístup nebo `Microsoft.Authorization/locks/*` akce. Z předdefinovaných rolí má tyto akce povolené pouze **vlastník** a **správce uživatelských přístupů**.

## <a name="managed-applications-and-locks"></a>Spravované aplikace a zámky

Některé služby Azure, jako jsou Azure Databricks, používají [spravované aplikace](../managed-applications/overview.md) k implementaci služby. V takovém případě služba vytvoří dvě skupiny prostředků. Jedna skupina prostředků obsahuje přehled služby a není uzamčena. Druhá skupina prostředků obsahuje infrastrukturu pro službu a je uzamčena.

Pokud se pokusíte odstranit skupinu prostředků infrastruktury, zobrazí se chyba oznamující, že skupina prostředků je uzamčena. Pokud se pokusíte odstranit zámek pro skupinu prostředků infrastruktury, zobrazí se chyba oznamující, že zámek nelze odstranit, protože je vlastněn systémovou aplikací.

Místo toho odstraňte službu, která také odstraní skupinu prostředků infrastruktury.

U spravovaných aplikací vyberte službu, kterou jste nasadili.

![Vybrat službu](./media/lock-resources/select-service.png)

Všimněte si, že služba obsahuje odkaz pro **skupinu spravovaných prostředků**. Tato skupina prostředků obsahuje infrastrukturu a je uzamčena. Nelze jej přímo odstranit.

![Zobrazit spravovanou skupinu](./media/lock-resources/show-managed-group.png)

Chcete-li odstranit vše pro službu, včetně skupiny prostředků uzamčené infrastruktury, **vyberte** odstranit pro službu.

![Odstranit službu](./media/lock-resources/delete-service.png)

## <a name="azure-backups-and-locks"></a>Azure zálohování a zámky

Pokud uzamknete skupinu prostředků vytvořenou službou Azure Backup Service, zálohování se spustí. Služba podporuje maximálně 18 bodů obnovení. Pomocí zámku **CanNotDelete** nelze vyčistit body obnovení služby zálohování. Další informace najdete [v tématu Nejčastější dotazy zálohování virtuálních počítačích Azure](../../backup/backup-azure-vm-backup-faq.md).

## <a name="portal"></a>Portál

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Šablona

Při použití šablony Správce prostředků k nasazení zámku, použijte různé hodnoty pro název a typ v závislosti na oboru zámku.

Při použití zámku na **prostředek**použijte následující formáty:

* název -`{resourceName}/Microsoft.Authorization/{lockName}`
* typ -`{resourceProviderNamespace}/{resourceType}/providers/locks`

Při použití zámku na **skupinu prostředků** nebo **předplatné**použijte následující formáty:

* název -`{lockName}`
* typ -`Microsoft.Authorization/locks`

Následující příklad ukazuje šablonu, která vytvoří plán služby app service, web a zámek na webu. Typ prostředku zámku je typ prostředku prostředku k uzamčení a **/providers/locks**. Název zámku je vytvořen zřetězením názvu prostředku s **/Microsoft.Authorization/** a název zámku.

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

Příklad nastavení zámku ve skupině prostředků naleznete v [tématu Vytvoření skupiny prostředků a uzamčení](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Nasazené prostředky pomocí Azure PowerShellu uzamknete pomocí příkazu [New-AzResourceLock.](/powershell/module/az.resources/new-azresourcelock)

Chcete-li prostředek uzamknout, zadejte název prostředku, jeho typ prostředku a název skupiny prostředků.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Chcete-li skupinu prostředků uzamknout, zadejte název skupiny prostředků.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Chcete-li získat informace o zámku, použijte [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Chcete-li získat všechny zámky v předplatném, použijte:

```azurepowershell-interactive
Get-AzResourceLock
```

Chcete-li získat všechny zámky pro prostředek, použijte:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Chcete-li získat všechny zámky pro skupinu prostředků, použijte:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Chcete-li zámek odstranit, použijte:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Nasazené prostředky uzamknete pomocí příkazového příkazu KOnI Azure pomocí příkazu [az lock create.](/cli/azure/lock#az-lock-create)

Chcete-li prostředek uzamknout, zadejte název prostředku, jeho typ prostředku a název skupiny prostředků.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Chcete-li skupinu prostředků uzamknout, zadejte název skupiny prostředků.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Chcete-li získat informace o zámku, použijte [seznam zámků az](/cli/azure/lock#az-lock-list). Chcete-li získat všechny zámky v předplatném, použijte:

```azurecli
az lock list
```

Chcete-li získat všechny zámky pro prostředek, použijte:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Chcete-li získat všechny zámky pro skupinu prostředků, použijte:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Chcete-li zámek odstranit, použijte:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
Nasazené prostředky můžete uzamknout pomocí [rozhraní REST API pro uzamčení správy](https://docs.microsoft.com/rest/api/resources/managementlocks). Rozhraní REST API umožňuje vytvářet a odstraňovat zámky a načítat informace o existujících uzamčení.

Chcete-li vytvořit zámek, spusťte:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Obor může být předplatné, skupina prostředků nebo prostředek. Jméno zámku je to, čemu chceš říkat zámek. Pro verzi api použijte **2016-09-01**.

Do požadavku zahrňte objekt JSON, který určuje vlastnosti zámku.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Další kroky
* Informace o logickém uspořádání zdrojů najdete v tématu [Použití značek k uspořádání zdrojů.](tag-resources.md)
* Omezení a konvence můžete použít v rámci předplatného pomocí přizpůsobených zásad. Další informace najdete v tématu [Co je Azure Policy?](../../governance/policy/overview.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).

