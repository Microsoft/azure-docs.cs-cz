---
title: Tag resources for logical organization
description: Shows how to apply tags to organize Azure resources for billing and managing.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f3fca2030d33ba5a52d43924ff542801d435e4de
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484276"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Používání značek k uspořádání prostředků Azure

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

To apply tags to resources, the user must have write access to that resource type. To apply tags to all resource types, use the [Contributor](../role-based-access-control/built-in-roles.md#contributor) role. To apply tags to only one resource type, use the contributor role for that resource. For example, to apply tags to virtual machines, use the [Virtual Machine Contributor](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>Zásady

You can use [Azure Policy](../governance/policy/overview.md) to enforce tagging rules and conventions. By creating a policy, you avoid the scenario of resources being deployed to your subscription that don't comply with the expected tags for your organization. Instead of manually applying tags or searching for resources that aren't compliant, you can create a policy that automatically applies the needed tags during deployment. Tags can also now be applied to existing resources with the new [Modify](../governance/policy/concepts/effects.md#modify) effect and a [remediation task](../governance/policy/how-to/remediate-resources.md). The following section shows example policies for tags.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete zobrazit existující značky pro *skupinu prostředků*, použijte:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Výstup tohoto skriptu bude v následujícím formátu:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Pokud chcete zobrazit existující značky pro *prostředek s konkrétním ID prostředku*, použijte:

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Nebo, pokud chcete zobrazit existující značky pro *prostředek se zadaným názvem a skupinou prostředků*, použijte:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Pokud chcete získat *skupiny prostředků s konkrétní značkou*, použijte:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

Pokud chcete získat *prostředky s konkrétní značkou*, použijte:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

To get *resources that have a specific tag name*, use:

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

Pokaždé, když použijete značky na prostředek nebo skupinu prostředků, přepíšete pro daný prostředek nebo skupinu prostředků existující značky. Proto je nutné použít jiný přístup na základě toho, jestli prostředek nebo skupina prostředků má existující značky.

Pokud chcete přidat značky ke *skupině prostředků bez existujících značek*, použijte:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Pokud chcete přidat značky ke *skupině prostředků s existujícími značkami*, načtěte existující značky, přidejte novou značku a znovu tyto značky použijte:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Pokud chcete přidat značky k *prostředku bez existujících značek*, použijte:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Pokud chcete přidat značky k *prostředku s existujícími značkami*, použijte:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

To apply all tags from a resource group to its resources, and *not keep existing tags on the resources*, use the following script:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

To apply all tags from a resource group to its resources, and *keep existing tags on resources that aren't duplicates*, use the following script:

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Pokud chcete odebrat všechny značky, předejte prázdnou zatřiďovací tabulku:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

Pokud chcete zobrazit existující značky pro *skupinu prostředků*, použijte:

```azurecli
az group show -n examplegroup --query tags
```

Výstup tohoto skriptu bude v následujícím formátu:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Or, to see the existing tags for a *resource that has a specified name, type, and resource group*, use:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

When looping through a collection of resources, you might want to show the resource by resource ID. A complete example is shown later in this article. Pokud chcete zobrazit existující značky pro *prostředek s konkrétním ID prostředku*, použijte:

```azurecli
az resource show --id <resource-id> --query tags
```

To get resource groups that have a specific tag, use `az group list`:

```azurecli
az group list --tag Dept=IT
```

To get all the resources that have a particular tag and value, use `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Pokaždé, když použijete značky na prostředek nebo skupinu prostředků, přepíšete pro daný prostředek nebo skupinu prostředků existující značky. Proto je nutné použít jiný přístup na základě toho, jestli prostředek nebo skupina prostředků má existující značky.

Pokud chcete přidat značky ke *skupině prostředků bez existujících značek*, použijte:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Pokud chcete přidat značky k *prostředku bez existujících značek*, použijte:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

To add tags to a resource that already has tags, retrieve the existing tags, reformat that value, and reapply the existing and new tags:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags -o json)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

To apply all tags from a resource group to its resources, and *not keep existing tags on the resources*, use the following script:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags -o json)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

To apply all tags from a resource group to its resources, and *keep existing tags on resources*, use the following script:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags -o json)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags -o json)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Šablony

To tag a resource during deployment, add the `tags` element to the resource you're deploying. Zadejte název a hodnotu značky.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Použití literálové hodnoty na název značky

Následující příklad ukazuje účet úložiště se dvěma značkami (`Dept` a `Environment`) nastavenými na literálové hodnoty:

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
                "Dept": "Finance",
                "Environment": "Production"
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

To set a tag to a datetime value, use the [utcNow function](resource-group-template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Použití objektu na element značky

Můžete definovat parametr objektu, ve kterém je uloženo několik značek, a použít tento objekt na element značky. Každá vlastnost v objektu se stane samostatnou značkou pro prostředek. V následujícím příkladu je na element značky použitý parametr `tagValues`.

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

### <a name="apply-a-json-string-to-the-tag-name"></a>Použití řetězce JSON na název značky

Pokud chcete uložit mnoho hodnot v jedné značce, použijte řetězec JSON, který představuje hodnoty. The entire JSON string is stored as one tag that can't exceed 256 characters. V následujícím příkladu je jedna značka `CostCenter`, která obsahuje několik hodnot z řetězce JSON:  

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

### <a name="apply-tags-from-resource-group"></a>Apply tags from resource group

To apply tags from a resource group to a resource, use the [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) function. When getting the tag value, use the `tags.[tag-name]` syntax instead of the `tags.tag-name` syntax, because some characters aren't parsed correctly in the dot notation.

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

## <a name="portal"></a>Portál

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>Rozhraní REST API

The Azure portal and PowerShell both use the [Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/) behind the scenes. If you need to integrate tagging into another environment, you can get tags by using **GET** on the resource ID and update the set of tags by using a **PATCH** call.

## <a name="tags-and-billing"></a>Tags and billing

You can use tags to group your billing data. For example, if you're running multiple VMs for different organizations, use the tags to group usage by cost center. You can also use tags to categorize costs by runtime environment, such as the billing usage for VMs running in the production environment.

You can retrieve information about tags through the [Azure Resource Usage and RateCard APIs](../billing/billing-usage-rate-card-overview.md) or the usage comma-separated values (CSV) file. You download the usage file from the [Azure Account Center](https://account.azure.com/Subscriptions) or Azure portal. For more information, see [Download or view your Azure billing invoice and daily usage data](../billing/billing-download-azure-invoice-daily-usage-date.md). When downloading the usage file from the Azure Account Center, select **Version 2**. For services that support tags with billing, the tags appear in the **Tags** column.

For REST API operations, see [Azure Billing REST API Reference](/rest/api/billing/).

## <a name="next-steps"></a>Další kroky

* Not all resource types support tags. To determine if you can apply a tag to a resource type, see [Tag support for Azure resources](tag-support.md).
* For an introduction to using the portal, see [Using the Azure portal to manage your Azure resources](manage-resource-groups-portal.md).  
