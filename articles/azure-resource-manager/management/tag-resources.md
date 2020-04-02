---
title: Označení prostředků, skupin prostředků a předplatných pro logickou organizaci
description: Ukazuje, jak použít značky pro uspořádání prostředků Azure pro fakturaci a správu.
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 76f9f61b3fe7002508bbd884f427efcfee698579
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548483"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Použití značek k uspořádání prostředků Azure a hierarchie správy

Značky použijete na prostředky Azure, skupiny prostředků a předplatná, abyste je logicky uspořádali do taxonomie. Každá značka se skládá z názvu a dvojice hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

Doporučení, jak implementovat strategii označování, naleznete v [tématu Resource naming and tagging decision guide](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Požadovaný přístup

Chcete-li použít značky na prostředek, musíte mít přístup pro zápis k typu prostředku **Microsoft.Resources/tags.** Role [Přispěvatel značek](../../role-based-access-control/built-in-roles.md#tag-contributor) umožňuje použít značky na entitu bez přístupu k samotné entitě. V současné době role přispěvatele značky nemůže použít značky pro prostředky nebo skupiny prostředků prostřednictvím portálu. Můžete použít značky na odběry prostřednictvím portálu. Podporuje všechny operace značek prostřednictvím powershellu a rozhraní REST API.  

Role [přispěvatele](../../role-based-access-control/built-in-roles.md#contributor) také uděluje požadovaný přístup k použití značek pro libovolnou entitu. Chcete-li použít značky pouze na jeden typ prostředku, použijte roli přispěvatele pro daný prostředek. Chcete-li například použít značky pro virtuální počítače, použijte [přispěvatele virtuálního počítače](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Použití značek

Azure PowerShell nabízí dva příkazy pro použití značek – [New-AzTag](/powershell/module/az.resources/new-aztag) a [Update-AzTag](/powershell/module/az.resources/update-aztag). Musíte mít modul Az.Resources 1.12.0 nebo novější. Verzi můžete zkontrolovat `Get-Module Az.Resources`pomocí aplikace . Můžete nainstalovat tento modul nebo [nainstalovat Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 nebo novější.

**New-AzTag** nahradí všechny značky na prostředek, skupinu prostředků nebo předplatné. Při volání příkazu předavte ID prostředku entity, kterou chcete označit.

Následující příklad použije sadu značek pro účet úložiště:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Po dokončení příkazu si všimněte, že prostředek má dvě značky.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Pokud příkaz spustíte znovu, ale tentokrát s různými značkami, všimněte si, že starší značky jsou odstraněny.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Chcete-li přidat značky k prostředku, který již značky obsahuje, použijte **aplikaci Update-AzTag**. Nastavte parametr **-Operation** na **sloučit**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Všimněte si, že dvě nové značky byly přidány do dvou existujících značek.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Každý název značky může mít pouze jednu hodnotu. Pokud zadáte novou hodnotu pro značku, stará hodnota se nahradí i v případě, že použijete operaci sloučení. Následující příklad změní značku Stav z normální na zelenou.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Když nastavíte parametr **-Operation** na **Nahradit**, existující značky se nahradí novou sadou tagů.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

V prostředku zůstanou pouze nové značky.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Stejné příkazy také pracovat se skupinami prostředků nebo odběry. Předáte identifikátor pro skupinu prostředků nebo odběr, který chcete označit.

Chcete-li do skupiny prostředků přidat novou sadu značek, použijte:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Chcete-li aktualizovat značky pro skupinu prostředků, použijte:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Chcete-li do předplatného přidat novou sadu značek, použijte:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Chcete-li aktualizovat značky pro předplatné, použijte:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Ve skupině prostředků může být více než jeden prostředek se stejným názvem. V takovém případě můžete nastavit každý prostředek pomocí následujících příkazů:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Výpis značek

Chcete-li získat značky pro prostředek, skupinu prostředků nebo předplatné, použijte příkaz [Get-AzTag](/powershell/module/az.resources/get-aztag) a předavte ID prostředku pro entitu.

Chcete-li zobrazit značky zdroje, použijte:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Chcete-li zobrazit značky pro skupinu prostředků, použijte:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Chcete-li zobrazit značky pro předplatné, použijte:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Seznam podle značky

Chcete-li získat prostředky, které mají konkrétní název značky a hodnotu, použijte:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Chcete-li získat prostředky, které mají konkrétní název značky s libovolnou hodnotou značky, použijte:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Chcete-li získat skupiny prostředků, které mají konkrétní název značky a hodnotu, použijte:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Odebrání značek

Chcete-li odebrat určité značky, použijte **update-AzTag** a nastavte **-Operation** to **Delete**. Předajte značky, které chcete odstranit.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

Zadané značky budou odebrány.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Chcete-li odebrat všechny značky, použijte příkaz [Remove-AzTag.](/powershell/module/az.resources/remove-aztag)

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>Použití značek

Při přidávání značek do skupiny prostředků nebo prostředku můžete buď přepsat existující značky, nebo připojit nové značky k existujícím značkám.

Chcete-li značky na prostředku přepsat, použijte:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Chcete-li připojit značku k existujícím značkám u prostředku, použijte:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Chcete-li přepsat existující značky ve skupině prostředků, použijte:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Chcete-li připojit značku k existujícím značkám ve skupině prostředků, použijte:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

V současné době Azure CLI nepodporuje použití značek na předplatná.

### <a name="list-tags"></a>Výpis značek

Chcete-li zobrazit existující značky pro prostředek, použijte:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Pokud chcete zobrazit existující značky pro skupinu prostředků, použijte:

```azurecli-interactive
az group show -n examplegroup --query tags
```

Výstup tohoto skriptu bude v následujícím formátu:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

### <a name="list-by-tag"></a>Seznam podle značky

Chcete-li získat všechny prostředky, které mají `az resource list`určitou značku a hodnotu, použijte :

```azurecli-interactive
az resource list --tag Dept=Finance
```

Chcete-li získat skupiny prostředků, `az group list`které mají určitou značku, použijte :

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Manipulační prostory

Pokud názvy značek nebo hodnoty obsahují mezery, musíte provést několik dalších kroků. Následující příklad použije všechny značky ze skupiny prostředků na její prostředky, když značky mohou obsahovat mezery.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>Šablony

Pomocí šablony Správce prostředků můžete během nasazení označit prostředky, skupiny prostředků a předplatná.

### <a name="apply-values"></a>Použít hodnoty

Následující příklad nasazuje účet úložiště se třemi značkami. Dvě značky`Dept` ( `Environment`a ) jsou nastaveny na literálové hodnoty. Jedna značka`LastDeployed`( ) je nastavena na parametr, který je výchozí na aktuální datum.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>Aplikování objektu

Můžete definovat parametr objektu, ve kterém je uloženo několik značek, a použít tento objekt na element značky. Tento přístup poskytuje větší flexibilitu než v předchozím příkladu, protože objekt může mít různé vlastnosti. Každá vlastnost v objektu se stane samostatnou značkou pro prostředek. V následujícím příkladu je na element značky použitý parametr `tagValues`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>Použití řetězce JSON

Pokud chcete uložit mnoho hodnot v jedné značce, použijte řetězec JSON, který představuje hodnoty. Celý řetězec JSON je uložen jako jedna značka, která nesmí překročit 256 znaků. V následujícím příkladu je jedna značka `CostCenter`, která obsahuje několik hodnot z řetězce JSON:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Použití značek ze skupiny prostředků

Chcete-li použít značky ze skupiny prostředků na prostředek, použijte funkci [resourceGroup.](../templates/template-functions-resource.md#resourcegroup) Při získávání hodnoty značky `tags[tag-name]` použijte syntaxi místo `tags.tag-name` syntaxe, protože některé znaky nejsou v zápisu tečky správně analyzovány.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Použití značek u skupin prostředků nebo předplatných

Značky můžete přidat do skupiny prostředků nebo předplatného nasazením typu prostředku **Microsoft.Resources/tags.** Značky se použijí na cílovou skupinu prostředků nebo předplatné pro nasazení. Při každém nasazení šablony nahradíte všechny značky, které byly dříve použity.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Chcete-li použít značky na skupinu prostředků, použijte buď PowerShell nebo Azure CLI. Nasaďte do skupiny prostředků, kterou chcete označit.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Pokud chcete použít značky na předplatné, použijte buď PowerShell nebo Azure CLI. Nasaďte se do předplatného, které chcete označit.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Následující šablona přidá značky z objektu do skupiny prostředků nebo předplatného.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portál

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Pokud chcete pracovat se značkami prostřednictvím rozhraní Azure REST API, použijte:

* [Tagy - vytvořit nebo aktualizovat v oboru](/rest/api/resources/tags/createorupdateatscope) (PUT operace)
* [Značky – aktualizace v oboru](/rest/api/resources/tags/updateatscope) (operace PATCH)
* [Tagy - Get at Scope](/rest/api/resources/tags/getatscope) (GET operace)
* [Tagy - Odstranit v oboru](/rest/api/resources/tags/deleteatscope) (operace DELETE)

## <a name="inherit-tags"></a>Dědit značky

Značky použité pro skupinu prostředků nebo předplatné nejsou zděděny prostředky. Pokud chcete na prostředky použít značky z předplatného nebo skupiny prostředků, přečtěte si hlavní [tématikou Azure Policies – tags](tag-policies.md).

## <a name="tags-and-billing"></a>Značky a fakturace

Můžete je použít třeba k seskupení údajů o fakturaci. Pokud například používáte odlišný virtuální počítač pro každou organizační složku, můžete pomocí značek seskupit údaje o využití podle nákladových středisek. Značky lze použít také ke kategorizaci nákladů podle prostředí modulu spuštění, jako je například fakturované využití virtuálních počítačů běžících v produkčním prostředí.

Informace o značkách můžete načíst prostřednictvím souborů API Azure Resource Usage a RateCard nebo prostřednictvím souboru hodnot csv oddělených podle čárek.You can retrieve information about tags through [the Azure Resource Usage and RateCard API or](../../billing/billing-usage-rate-card-overview.md) the usage comma-separated values (CSV) file. Soubor využití stáhnete z [Azure Account Center](https://account.azure.com/Subscriptions) nebo na webu Azure Portal. Další informace najdete v [tématu Stažení nebo zobrazení fakturační faktury Azure a dat o denním využití](../../billing/billing-download-azure-invoice-daily-usage-date.md). Při stahování souboru využití z Centra účtů Azure vyberte **verzi 2**. U služeb, které podporují značky s fakturací, se značky zobrazí ve sloupci **Značky.**

Operace rozhraní REST API najdete v [tématu Odkaz na rozhraní API pro fakturaci azure](/rest/api/billing/).

## <a name="limitations"></a>Omezení

Na značky se vztahují následující omezení:

* Značky nepodporují všechny typy prostředků. Pokud chcete zjistit, jestli můžete použít značku pro typ prostředku, přečtěte si informace [o podpoře prostředků Azure](tag-support.md).
* Skupiny pro správu aktuálně nepodporují značky.
* Každý prostředek, skupina prostředků a předplatné může mít maximálně 50 párů název/hodnota značky. Pokud potřebujete použít více značek, než je maximální povolené číslo, použijte pro hodnotu značky řetězec JSON. Řetězec JSON může obsahovat mnoho hodnot, které se použijí pro jeden název značky. Skupina prostředků nebo odběr může obsahovat mnoho prostředků, které mají každý 50 dvojice název značky a hodnoty.
* Název značky je omezen na 512 znaků a hodnota značky je omezena na 256 znaků. Pro účty úložiště je název značky omezen na 128 znaků a hodnota značky je omezena na 256 znaků.
* Generalizované virtuální ms nepodporují značky.
* Značky nelze použít na klasické prostředky, jako jsou cloudové služby.
* Názvy značek nesmí obsahovat `<` `>`tyto `%` `&`znaky: , , , `\`, `?`, ,`/`

   > [!NOTE]
   > V současné době zóny Azure DNS a služby Traffic Manger také neumožňují použití mezer ve značce.

## <a name="next-steps"></a>Další kroky

* Značky nepodporují všechny typy prostředků. Pokud chcete zjistit, jestli můžete použít značku pro typ prostředku, přečtěte si informace [o podpoře prostředků Azure](tag-support.md).
* Doporučení, jak implementovat strategii označování, naleznete v [tématu Resource naming and tagging decision guide](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
