---
title: Úvodní ukázky dotazů
description: Použití Azure Graph prostředků ke spuštění některých starter dotazuje, včetně počítání prostředky objednávání prostředků, nebo s konkrétní značkou.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: fd945b5fd9f26cc65c5b049406831228a3d5f327
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338712"
---
# <a name="starter-resource-graph-queries"></a>Úvodní dotazy na Resource Graph

Prvním krokem k porozumění dotazům s Azure Resource Graph jsou základní znalosti [dotazovacího jazyka](../concepts/query-language.md). Pokud ještě neznáte [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), doporučujeme přečíst si základní informace, abyste pochopili, jak vytvářet požadavky na prostředky, které hledáte.

Projdeme následující úvodní dotazy:

> [!div class="checklist"]
> - [Počet prostředků Azure](#count-resources)
> - [Výpis prostředků seřazených podle názvu](#list-resources)
> - [Zobrazení všech virtuálních počítačů, které jsou seřazené podle názvu v sestupném pořadí](#show-vms)
> - [Zobrazení prvních pěti virtuálních počítačů podle názvu a jejich typu operačního systému](#show-sorted)
> - [Počet virtuálních počítačů podle typu operačního systému](#count-os)
> - [Zobrazení prostředků, které obsahují úložiště](#show-storage)
> - [Seznam všech veřejných IP adres](#list-publicip)
> - [Počet prostředků, které mají IP adresy nakonfigurované podle předplatného](#count-resources-by-ip)
> - [Seznam prostředků s konkrétní hodnotou značky](#list-tag)
> - [Seznam všech účtů úložiště s konkrétní hodnotou značky](#list-specific-tag)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Podpora jazyků

Azure PowerShell (prostřednictvím modulu) a Azure CLI (prostřednictvím rozšíření) podporují Azure Resource Graph. Před spuštěním jakéhokoli z následujících dotazů zkontrolujte, že je vaše prostředí připravené. Zobrazte [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) a [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pro pokyny k instalaci a ověření vašeho výběru prostředí.

## <a name="count-resources"></a>Počet prostředků Azure

Tento dotaz vrátí počet prostředků Azure, které existují v předplatných, ke kterým máte přístup. Tento dotaz je také vhodný k ověření, že vaše vybrané prostředí má nainstalované a funkční odpovídající komponenty služby Azure Resource Graph.

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="list-resources"></a>Výpis prostředků seřazených podle názvu

Tento dotaz vrátí jakýkoli typ prostředku, ale pouze vlastnosti **name** (Název), **type** (Typ) a **location** (Umístění). Pomocí klauzule `order by` seřadí vlastnosti podle vlastnosti **name** (Název) ve vzestupném pořadí (`asc`).

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>Zobrazení všech virtuálních počítačů, které jsou seřazené podle názvu v sestupném pořadí

Když chceme vypsat pouze virtuální počítače (typ `Microsoft.Compute/virtualMachines`), můžeme ve výsledcích porovnat shodu vlastnosti **type** (Typ). Podobně jako v předchozím dotazu musí být změny `desc` `order by` být řazeny sestupně. `=~` ve shodě typu říká Azure Resource Graphu aby nerozlišoval malá a velká písmena.

```Query
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="show-sorted"></a>Zobrazení prvních pěti virtuálních počítačů podle názvu a jejich typu operačního systému

Tento dotaz bude používat `limit` pouze k načtení pěti odpovídajících záznamů, které jsou řazeny podle názvu. Typ prostředku Azure je `Microsoft.Compute/virtualMachines`. `project` říká Azure Resource Graph, které vlastnosti použít.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="count-os"></a>Počet virtuálních počítačů podle typu operačního systému

Vycházíme z předchozího dotazu a stále omezujeme prostředky Azure na typ `Microsoft.Compute/virtualMachines`, ale už neomezujeme počet vrácených záznamů.
Místo toho jsme použili `summarize` a `count()` k definování, jak seskupit a agregovat hodnoty podle vlastností, což je v tomto příkladu `properties.storageProfile.osDisk.osType`. Příklad toho, jak tento řetězec vypadá v úplném objektu, najdete v části [zjišťování prostředků – objevování virtuálních počítačů](../concepts/explore-resources.md#virtual-machine-discovery).

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Jiný způsob, jak napsat stejný dotaz je vlastnost `extend` a dočasné pojmenování pro použití v rámci dotazu, v tomto případě **os**. **OS** je potom využit `summarize` a `count()` stejně jako v předchozím příkladu.

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Uvědomte si, že zatímco `=~` umožňuje nerozlišovat malá a velká písmena, použití vlastností (jako například **properties.storageProfile.osDisk.osType**) v dotazu vyžaduje rozlišování malých a velkých písmen. Jestliže je vlastnost nesprávná velikost písmen, stále může hodnotu vrátit, ale seskupení nebo shrnutí nebude správné.

## <a name="show-storage"></a>Zobrazení prostředků, které obsahují úložiště

Místo explicitního definování typu k porovnání, tento dotaz z příkladu najde jakékoli prostředky Azure, které `contains` slovo **úložiště**.

```Query
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>Seznam všech veřejných IP adres

Podobně jako v předchozím dotazu se vyhledají všechny záznamy, jejichž typ obsahuje slovo **publicIPAddresses**.
Tento dotaz je rozšířen na tomto vzoru, aby se vyloučily výsledky, kde **properties.ipAddress** má hodnotu nula, aby se vrátily pouze vlastnosti **properties.ipAddress**a získaly se `limit` výsledky podle prvních 100. V závislosti na zvoleném prostředí možná budete muset odebrat uvozovky.

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

## <a name="count-resources-by-ip"></a>Počet prostředků, které mají IP adresy nakonfigurované podle předplatného

Pomocí předchozího ilustračního dotazu a přidáním `summarize` a `count()`, získáme seznam podle předplatného prostředků s nakonfigurovanými IP adresami.

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
```

## <a name="list-tag"></a>Seznam prostředků s konkrétní hodnotou značky

Rozsah výsledků můžeme omezit podle vlastností jiných než typ prostředku Azure, jako je například značka. V tomto příkladu vyfiltrujeme prostředky Azure s názvem značky **Environment** (Prostředí) s hodnotou **Internal** (Interní).

```Query
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Pokud chcete vrátit také značky prostředku a jejich hodnoty, přidejte ke klíčovému slovu `project` vlastnost **tags** (Značky).

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>Seznam všech účtů úložiště s konkrétní hodnotou značky

Zkombinujte funkci filtrování z předchozího příkladu a vyfiltrujte typ prostředku Azure podle vlastnosti **type** (Typ). Tento dotaz naše hledání omezuje také na konkrétní typy prostředků Azure s konkrétním názvem a hodnotou značky.

```Query
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> Tento příklad používá `==` pro shodu místo podmínky `=~`. `==` je shoda rozlišující velikost písmen.

## <a name="next-steps"></a>Další postup

- Další informace o [dotazovacím jazyku](../concepts/query-language.md)
- Naučte se [prozkoumat prostředky](../concepts/explore-resources.md)
- Zobrazit ukázky [Pokročilé dotazy](advanced.md)