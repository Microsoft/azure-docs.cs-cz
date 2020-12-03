---
title: Označení prostředků, skupin prostředků a předplatných pro logickou organizaci
description: Ukazuje, jak použít značky k uspořádání prostředků Azure k fakturaci a správě.
ms.topic: conceptual
ms.date: 12/03/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e47d3acf15ce5e4f5cb70444419b76beb21ae98b
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558143"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Použití značek k uspořádání prostředků Azure a hierarchie správy

Značky můžete použít pro prostředky Azure, skupiny prostředků a odběry k logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

Doporučení, jak implementovat strategii označování, najdete v článku [Průvodce vytvářením názvů prostředků a označování označením](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

> [!IMPORTANT]
> V názvech značek se operace nerozlišují malá a velká písmena. Značka s názvem značky, bez ohledu na velikost písmen, je aktualizována nebo načtena. Poskytovatel prostředků ale může zachovat velikost písmen, které zadáte pro název značky. V sestavách nákladů se zobrazí velká a malá písmena.
> 
> Hodnoty značek rozlišují velká a malá písmena.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Požadovaný přístup

Existují dva způsoby, jak získat požadovaný přístup k prostředkům značky.

- Můžete mít přístup pro zápis k typu prostředku **Microsoft. Resources/Tags** . Tento přístup vám umožní označit libovolný prostředek, a to i v případě, že nemáte přístup k samotnému prostředku. Tento přístup udělí role [Přispěvatel značek](../../role-based-access-control/built-in-roles.md#tag-contributor) . V současné době role Přispěvatel značek nemůže na základě portálu použít značky na prostředky nebo skupiny prostředků. Může použít značky pro odběry prostřednictvím portálu. Podporuje všechny operace značek prostřednictvím PowerShellu a REST API.  

- Můžete mít přístup pro zápis k samotnému prostředku. Role [přispěvatele](../../role-based-access-control/built-in-roles.md#contributor) uděluje požadovaný přístup pro použití značek pro libovolnou entitu. Pokud chcete použít Tagy jenom pro jeden typ prostředku, použijte pro tento prostředek roli přispěvatele. Pokud například chcete použít značky pro virtuální počítače, použijte [přispěvatele virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Použít značky

Azure PowerShell nabízí dva příkazy pro použití značek- [New-AzTag](/powershell/module/az.resources/new-aztag) a [Update-AzTag](/powershell/module/az.resources/update-aztag). Musíte mít modul AZ. Resources 1.12.0 nebo novější. Verzi můžete ověřit pomocí `Get-Module Az.Resources` . Tento modul můžete nainstalovat nebo [nainstalovat Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 nebo novějším.

**Příkaz New-AzTag** nahradí všechny značky prostředku, skupiny prostředků nebo předplatného. Při volání příkazu předejte ID prostředku entity, kterou chcete označit.

V následujícím příkladu se aplikuje sada značek na účet úložiště:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Po dokončení příkazu si všimněte, že prostředek obsahuje dvě značky.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Pokud znovu spustíte příkaz, ale tentokrát s různými značkami, Všimněte si, že se předchozí značky odebraly.

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

Chcete-li přidat značky k prostředku, který již obsahuje značky, použijte **příkaz Update-AzTag**. Nastavte parametr **-Operation** na **Merge**.

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

Každý název značky může mít pouze jednu hodnotu. Pokud zadáte novou hodnotu pro značku, stará hodnota je nahrazena i v případě, že použijete operaci sloučení. Následující příklad změní značku stavu z normálního na zelenou.

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

Když nastavíte parametr **-Operation** na hodnotu **nahradit**, existující značky budou nahrazeny novou sadou značek.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

V prostředku zůstanou jenom nové značky.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Stejné příkazy také fungují se skupinami prostředků nebo předplatnými. Předáte identifikátor skupiny prostředků nebo předplatného, které chcete označit.

Pokud chcete do skupiny prostředků přidat novou sadu značek, použijte:

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

Pokud chcete přidat novou sadu značek k předplatnému, použijte:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Pokud chcete aktualizovat značky pro předplatné, použijte:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Ve skupině prostředků může být víc než jeden prostředek se stejným názvem. V takovém případě můžete nastavit jednotlivé prostředky pomocí následujících příkazů:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Výpis značek

Pokud chcete získat značky prostředku, skupiny prostředků nebo předplatného, použijte příkaz [Get-AzTag](/powershell/module/az.resources/get-aztag) a předejte mu ID prostředku pro danou entitu.

Chcete-li zobrazit značky pro prostředek, použijte:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Pokud chcete zobrazit značky pro skupinu prostředků, použijte:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Pokud chcete zobrazit značky pro předplatné, použijte:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Seznam podle značky

Chcete-li získat prostředky, které mají konkrétní název a hodnotu značky, použijte:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Chcete-li získat prostředky, které mají konkrétní název značky s libovolnou hodnotou značky, použijte:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Chcete-li získat skupiny prostředků, které mají konkrétní název a hodnotu značky, použijte:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Odebrat značky

Chcete-li odebrat konkrétní značky, použijte příkaz **Update-AzTag** a set **-Operation-** to **Delete**. Předejte značky, které chcete odstranit.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

Zadané značky jsou odebrány.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Chcete-li odebrat všechny značky, použijte příkaz [Remove-AzTag](/powershell/module/az.resources/remove-aztag) .

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>Použít značky

Azure CLI nabízí dva příkazy pro použití značek – [AZ tag Create](/cli/azure/tag#az_tag_create) a [AZ tag Update](/cli/azure/tag#az_tag_update). Musíte mít Azure CLI 2.10.0 nebo novější. Verzi můžete ověřit pomocí `az version` . Informace o aktualizaci nebo instalaci najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

Příkaz **AZ tag Create** nahradí všechny značky u prostředku, skupiny prostředků nebo předplatného. Při volání příkazu předejte ID prostředku entity, kterou chcete označit.

V následujícím příkladu se aplikuje sada značek na účet úložiště:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag create --resource-id $resource --tags Dept=Finance Status=Normal
```

Po dokončení příkazu si všimněte, že prostředek obsahuje dvě značky.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Status": "Normal"
  }
},
```

Pokud znovu spustíte příkaz, ale tentokrát s různými značkami, Všimněte si, že se předchozí značky odebraly.

```azurecli-interactive
az tag create --resource-id $resource --tags Team=Compliance Environment=Production
```

```output
"properties": {
  "tags": {
    "Environment": "Production",
    "Team": "Compliance"
  }
},
```

Chcete-li přidat značky k prostředku, který již obsahuje značky, použijte `az tag update` . Nastavte `--operation` parametr na `Merge` .

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Dept=Finance Status=Normal
```

Všimněte si, že dvě nové značky byly přidány do dvou existujících značek.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Normal",
    "Team": "Compliance"
  }
},
```

Každý název značky může mít pouze jednu hodnotu. Pokud zadáte novou hodnotu pro značku, stará hodnota je nahrazena i v případě, že použijete operaci sloučení. Následující příklad změní značku stavu z normálního na zelenou.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Status=Green
```

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Green",
    "Team": "Compliance"
  }
},
```

Když nastavíte `--operation` parametr na `Replace` , existující značky budou nahrazeny novou sadou značek.

```azurecli-interactive
az tag update --resource-id $resource --operation Replace --tags Project=ECommerce CostCenter=00123 Team=Web
```

V prostředku zůstanou jenom nové značky.

```output
"properties": {
  "tags": {
    "CostCenter": "00123",
    "Project": "ECommerce",
    "Team": "Web"
  }
},
```

Stejné příkazy také fungují se skupinami prostředků nebo předplatnými. Předáte identifikátor skupiny prostředků nebo předplatného, které chcete označit.

Pokud chcete do skupiny prostředků přidat novou sadu značek, použijte:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag create --resource-id $group --tags Dept=Finance Status=Normal
```

Chcete-li aktualizovat značky pro skupinu prostředků, použijte:

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags CostCenter=00123 Environment=Production
```

Pokud chcete přidat novou sadu značek k předplatnému, použijte:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag create --resource-id /subscriptions/$sub --tags CostCenter=00123 Environment=Dev
```

Pokud chcete aktualizovat značky pro předplatné, použijte:

```azurecli-interactive
az tag update --resource-id /subscriptions/$sub --operation Merge --tags Team="Web Apps"
```

### <a name="list-tags"></a>Výpis značek

Chcete-li získat značky prostředku, skupiny prostředků nebo předplatného, použijte příkaz [AZ tag list](/cli/azure/tag#az_tag_list) a předejte mu ID prostředku pro entitu.

Chcete-li zobrazit značky pro prostředek, použijte:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag list --resource-id $resource
```

Pokud chcete zobrazit značky pro skupinu prostředků, použijte:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag list --resource-id $group
```

Pokud chcete zobrazit značky pro předplatné, použijte:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag list --resource-id /subscriptions/$sub
```

### <a name="list-by-tag"></a>Seznam podle značky

Chcete-li získat prostředky, které mají konkrétní název a hodnotu značky, použijte:

```azurecli-interactive
az resource list --tag CostCenter=00123 --query [].name
```

Chcete-li získat prostředky, které mají konkrétní název značky s libovolnou hodnotou značky, použijte:

```azurecli-interactive
az resource list --tag Team --query [].name
```

Chcete-li získat skupiny prostředků, které mají konkrétní název a hodnotu značky, použijte:

```azurecli-interactive
az group list --tag Dept=Finance
```

### <a name="remove-tags"></a>Odebrat značky

Chcete-li odebrat konkrétní značky, použijte `az tag update` a nastavte `--operation` na `Delete` . Předejte značky, které chcete odstranit.

```azurecli-interactive
az tag update --resource-id $resource --operation Delete --tags Project=ECommerce Team=Web
```

Zadané značky jsou odebrány.

```output
"properties": {
  "tags": {
    "CostCenter": "00123"
  }
},
```

Chcete-li odebrat všechny značky, použijte příkaz [AZ tag Delete](/cli/azure/tag#az_tag_delete) .

```azurecli-interactive
az tag delete --resource-id $resource
```

### <a name="handling-spaces"></a>Prostory pro zpracování

Pokud názvy nebo hodnoty značek obsahují mezery, uzavřete je do dvojitých uvozovek.

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags "Cost Center"=Finance-1222 Location="West US"
```

## <a name="templates"></a>Šablony

Můžete označovat prostředky, skupiny prostředků a odběry během nasazování pomocí šablony Správce prostředků.

### <a name="apply-values"></a>Použít hodnoty

Následující příklad nasadí účet úložiště se třemi značkami. Dvě značky ( `Dept` a `Environment` ) jsou nastaveny na hodnoty literálu. Jedna značka ( `LastDeployed` ) je nastavena na parametr, který je výchozím nastavením aktuální datum.

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

### <a name="apply-an-object"></a>Použití objektu

Můžete definovat parametr objektu, ve kterém je uloženo několik značek, a použít tento objekt na element značky. Tento přístup poskytuje větší flexibilitu než předchozí příklad, protože objekt může mít různé vlastnosti. Každá vlastnost v objektu se stane samostatnou značkou pro prostředek. V následujícím příkladu je na element značky použitý parametr `tagValues`.

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

### <a name="apply-a-json-string"></a>Použít řetězec JSON

Pokud chcete uložit mnoho hodnot v jedné značce, použijte řetězec JSON, který představuje hodnoty. Celý řetězec JSON je uložen jako jedna značka, která nemůže být delší než 256 znaků. V následujícím příkladu je jedna značka `CostCenter`, která obsahuje několik hodnot z řetězce JSON:  

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

### <a name="apply-tags-from-resource-group"></a>Použít značky ze skupiny prostředků

Chcete-li použít značky ze skupiny prostředků na prostředek, použijte funkci [Resource ()](../templates/template-functions-resource.md#resourcegroup) . Při získávání hodnoty značky použijte `tags[tag-name]` syntaxi namísto `tags.tag-name` syntaxe, protože některé znaky nejsou v zápisu tečky správně analyzovány.

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Použití značek pro skupiny prostředků nebo odběry

Do skupiny prostředků nebo předplatného můžete přidat značky tak, že nasadíte typ prostředku **Microsoft. Resources/Tags** . Značky se aplikují na cílovou skupinu prostředků nebo předplatné pro nasazení. Pokaždé, když nasadíte šablonu, nahradíte všechny značky, které byly dříve aplikovány.

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

Pokud chcete značky použít pro skupinu prostředků, použijte PowerShell nebo Azure CLI. Nasaďte do skupiny prostředků, kterou chcete označit.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Pokud chcete značky použít u předplatného, použijte PowerShell nebo Azure CLI. Nasaďte do předplatného, které chcete označit.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Další informace o nasazeních předplatných najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](../templates/deploy-to-subscription.md).

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

Pokud chcete pracovat se značkami prostřednictvím REST API Azure, použijte:

* [Značky – vytvořit nebo aktualizovat v oboru](/rest/api/resources/tags/createorupdateatscope) (operace PUT)
* [Značky – aktualizace v oboru](/rest/api/resources/tags/updateatscope) (operace opravy)
* [Značky – získat v oboru](/rest/api/resources/tags/getatscope) (operace Get)
* [Značky – odstranění v oboru](/rest/api/resources/tags/deleteatscope) (operace odstranění)

## <a name="inherit-tags"></a>Zdědit značky

Značky použité pro skupinu prostředků nebo odběr nejsou děděny prostředky. Pokud chcete použít značky z předplatného nebo skupiny prostředků k prostředkům, přečtěte si téma [zásady Azure – značky](tag-policies.md).

## <a name="tags-and-billing"></a>Značky a fakturace

Můžete je použít třeba k seskupení údajů o fakturaci. Pokud například používáte odlišný virtuální počítač pro každou organizační složku, můžete pomocí značek seskupit údaje o využití podle nákladových středisek. Značky lze použít také ke kategorizaci nákladů podle prostředí modulu spuštění, jako je například fakturované využití virtuálních počítačů běžících v produkčním prostředí.

Můžete načíst informace o značkách prostřednictvím [rozhraní API pro využití prostředků Azure a sazebních karet](../../cost-management-billing/manage/usage-rate-card-overview.md) nebo souboru hodnot oddělených čárkami (CSV). Soubor využití si stáhnete z Azure Portal. Další informace najdete v tématech [stažení nebo zobrazení fakturačních faktur Azure a dat o denním využití](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md). Při stahování souboru využití z Centrum účtů Azure vyberte možnost **verze 2**. Pro služby, které podporují značky s fakturací, se značky zobrazí ve sloupci **značky** .

Informace o REST API operacích najdete v [referenčních informacích o fakturačních REST API Azure](/rest/api/billing/).

## <a name="limitations"></a>Omezení

Na značky se vztahují následující omezení:

* Ne všechny typy prostředků podporují značky. Pokud chcete zjistit, jestli můžete pro typ prostředku použít značku, přečtěte si téma [Podpora značek pro prostředky Azure](tag-support.md).
* Každý prostředek, skupina prostředků a předplatné můžou mít maximálně 50 párů název/hodnota značky. Pokud potřebujete použít více značek, než je maximální povolený počet, použijte jako hodnotu značky řetězec JSON. Řetězec JSON může obsahovat mnoho hodnot, které se použijí pro jeden název značky. Skupina prostředků nebo předplatné můžou obsahovat spoustu prostředků, které mají každý z nich 50 páry název/hodnota značky.
* Název značky je omezen na 512 znaků a hodnota značky je omezena na 256 znaků. Pro účty úložiště je název značky omezen na 128 znaků a hodnota značky je omezena na 256 znaků.
* Značky nelze použít u klasických prostředků, jako je například Cloud Services.
* Názvy značek nesmí obsahovat tyto znaky: `<` , `>` , `%` , `&` , `\` , `?` , `/`

   > [!NOTE]
   > V současné době Azure DNS zóny a služby Traffic Manager také nedovolují použití mezer ve značce.
   >
   > Přední dveře Azure nepodporují použití `#` v názvu značky.
   >
   > Azure Automation a Azure CDN podporují pouze 15 značek u prostředků.

## <a name="next-steps"></a>Další kroky

* Ne všechny typy prostředků podporují značky. Pokud chcete zjistit, jestli můžete pro typ prostředku použít značku, přečtěte si téma [Podpora značek pro prostředky Azure](tag-support.md).
* Doporučení, jak implementovat strategii označování, najdete v článku [Průvodce vytvářením názvů prostředků a označování označením](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
