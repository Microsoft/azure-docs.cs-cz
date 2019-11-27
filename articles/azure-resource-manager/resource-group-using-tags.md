---
title: Označení prostředků pro logickou organizaci
description: Ukazuje, jak použít značky k uspořádání prostředků Azure k fakturaci a správě.
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

Pro použití značek u prostředků musí mít uživatel k tomuto typu prostředku přístup pro zápis. Chcete-li použít značky pro všechny typy prostředků, použijte roli [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) . Pokud chcete použít Tagy jenom pro jeden typ prostředku, použijte pro tento prostředek roli přispěvatele. Pokud například chcete použít značky pro virtuální počítače, použijte [přispěvatele virtuálních počítačů](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>Zásady

Pomocí [Azure Policy](../governance/policy/overview.md) můžete vymáhat pravidla označování a konvence. Vytvořením zásady se vyhnete scénáři nasazení prostředků do vašeho předplatného, které nedodržují očekávané značky vaší organizace. Místo ručního použití značek nebo hledání prostředků, které nedodržují předpisy, můžete vytvořit zásadu, která při nasazení automaticky použije potřebné značky. Značky se teď dají použít u existujících prostředků s novým efektem [změny](../governance/policy/concepts/effects.md#modify) a [úlohou nápravy](../governance/policy/how-to/remediate-resources.md). V následující části jsou uvedeny příklady zásad pro značky.

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

Chcete-li získat *prostředky, které mají konkrétní název značky*, použijte:

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

Pokud chcete použít všechny značky ze skupiny prostředků na prostředky a *nezachovat existující značky prostředků*, použijte tento skript:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Pokud chcete použít všechny značky ze skupiny prostředků na prostředky a *zachovat existující značky u neduplicitních prostředků*, použijte tento skript:

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

Nebo pokud chcete zobrazit existující značky pro prostředek se *zadaným názvem, typem a skupinou prostředků*, použijte:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Při procházení kolekcí prostředků je vhodné zobrazit prostředek podle ID prostředku. Kompletní příklad je uveden dále v tomto článku. Pokud chcete zobrazit existující značky pro *prostředek s konkrétním ID prostředku*, použijte:

```azurecli
az resource show --id <resource-id> --query tags
```

Chcete-li získat skupiny prostředků s konkrétní značkou, použijte `az group list`:

```azurecli
az group list --tag Dept=IT
```

Chcete-li získat všechny prostředky, které mají určitou značku a hodnotu, použijte `az resource list`:

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

Chcete-li přidat značky k prostředku, který již obsahuje značky, načtěte existující značky, přeformátujte tuto hodnotu a znovu použijte existující a nové značky:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags -o json)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Pokud chcete použít všechny značky ze skupiny prostředků na prostředky a *nezachovat existující značky prostředků*, použijte tento skript:

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

Pokud chcete použít všechny značky ze skupiny prostředků na prostředky a *zachovat existující značky prostředků*, použijte tento skript:

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

Chcete-li během nasazování označit prostředek, přidejte `tags` element do prostředku, který nasazujete. Zadejte název a hodnotu značky.

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

Chcete-li nastavit značku na hodnotu DateTime, použijte [funkci UtcNow](resource-group-template-functions-string.md#utcnow).

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

Chcete-li použít značky ze skupiny prostředků na prostředek, použijte funkci [Resource](resource-group-template-functions-resource.md#resourcegroup) . Při získávání hodnoty značky použijte místo syntaxe `tags.tag-name` syntaxi `tags.[tag-name]`, protože některé znaky se v zápisu tečky neanalyzují správně.

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

## <a name="rest-api"></a>REST API

Azure Portal a PowerShell používají [Správce prostředků REST API](https://docs.microsoft.com/rest/api/resources/) na pozadí. Pokud potřebujete integrovat označování do jiného prostředí, můžete získat značky pomocí metody **Get** pro ID prostředku a aktualizovat sadu značek pomocí volání **opravy** .

## <a name="tags-and-billing"></a>Značky a fakturace

K seskupení fakturačních dat můžete použít značky. Pokud například spouštíte více virtuálních počítačů pro různé organizace, použijte značky k seskupení využití podle nákladového centra. Pomocí značek můžete také kategorizovat náklady podle běhového prostředí, jako je například využití fakturace pro virtuální počítače běžící v produkčním prostředí.

Můžete načítat informace o značkách prostřednictvím [rozhraní API pro využití prostředků Azure a ratecard](../billing/billing-usage-rate-card-overview.md) nebo souborů hodnot oddělených čárkami (CSV). Soubor využití si můžete stáhnout z [centrum účtů Azure](https://account.azure.com/Subscriptions) nebo Azure Portal. Další informace najdete v tématech [stažení nebo zobrazení fakturačních faktur Azure a dat o denním využití](../billing/billing-download-azure-invoice-daily-usage-date.md). Při stahování souboru využití z Centrum účtů Azure vyberte možnost **verze 2**. Pro služby, které podporují značky s fakturací, se značky zobrazí ve sloupci **značky** .

Informace o REST API operacích najdete v [referenčních informacích o fakturačních REST API Azure](/rest/api/billing/).

## <a name="next-steps"></a>Další kroky

* Ne všechny typy prostředků podporují značky. Pokud chcete zjistit, jestli můžete pro typ prostředku použít značku, přečtěte si téma [Podpora značek pro prostředky Azure](tag-support.md).
* Úvod k používání portálu najdete v tématu [použití Azure Portal ke správě prostředků Azure](manage-resource-groups-portal.md).  
