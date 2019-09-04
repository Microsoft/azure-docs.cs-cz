---
title: Ukázky počátečních dotazů
description: Pomocí Azure Resource graphu můžete spouštět některé úvodní dotazy, včetně počítání prostředků, řazení prostředků nebo konkrétní značky.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 688c591cbe94c69c73779843011cb24c3d2fd4cf
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241083"
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
> - [Zobrazit aliasy pro prostředek virtuálního počítače](#show-aliases)
> - [Zobrazit odlišné hodnoty pro konkrétní alias](#distinct-alias-values)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="language-support"></a>Podpora jazyků

Azure PowerShell (prostřednictvím modulu) a Azure CLI (prostřednictvím rozšíření) podporují Azure Resource Graph. Před spuštěním jakéhokoli z následujících dotazů zkontrolujte, že je vaše prostředí připravené. Zobrazte [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) a [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pro pokyny k instalaci a ověření vašeho výběru prostředí.

## <a name="a-namecount-resourcescount-azure-resources"></a><a name="count-resources"/>Počet prostředků Azure

Tento dotaz vrátí počet prostředků Azure, které existují v předplatných, ke kterým máte přístup. Tento dotaz je také vhodný k ověření, že vaše vybrané prostředí má nainstalované a funkční odpovídající komponenty služby Azure Resource Graph.

```kusto
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a><a name="list-resources"/>Vypsat prostředky seřazené podle názvu

Tento dotaz vrátí jakýkoli typ prostředku, ale pouze vlastnosti **name** (Název), **type** (Typ) a **location** (Umístění). Pomocí klauzule `order by` seřadí vlastnosti podle vlastnosti **name** (Název) ve vzestupném pořadí (`asc`).

```kusto
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"/>Zobrazit všechny virtuální počítače seřazené podle názvu v sestupném pořadí

Když chceme vypsat pouze virtuální počítače (typ `Microsoft.Compute/virtualMachines`), můžeme ve výsledcích porovnat shodu vlastnosti **type** (Typ). Podobně jako v předchozím dotazu musí být změny `desc` `order by` být řazeny sestupně. `=~` ve shodě typu říká Azure Resource Graphu aby nerozlišoval malá a velká písmena.

```kusto
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

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"/>Zobrazit prvních pět virtuálních počítačů podle názvu a jejich typu operačního systému

Tento dotaz bude používat `top` pouze k načtení pěti odpovídajících záznamů, které jsou řazeny podle názvu. Typ prostředku Azure je `Microsoft.Compute/virtualMachines`. `project` říká Azure Resource Graph, které vlastnosti použít.

```kusto
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

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a><a name="count-os"/>Počet virtuálních počítačů podle typu operačního systému

Vycházíme z předchozího dotazu a stále omezujeme prostředky Azure na typ `Microsoft.Compute/virtualMachines`, ale už neomezujeme počet vrácených záznamů.
Místo toho jsme použili `summarize` a `count()` k definování, jak seskupit a agregovat hodnoty podle vlastností, což je v tomto příkladu `properties.storageProfile.osDisk.osType`. Příklad toho, jak tento řetězec vypadá v úplném objektu, najdete v části [zjišťování prostředků – objevování virtuálních počítačů](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
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

```kusto
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

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a><a name="show-storage"/>Zobrazit prostředky, které obsahují úložiště

Místo explicitního definování typu k porovnání, tento dotaz z příkladu najde jakékoli prostředky Azure, které `contains` slovo **úložiště**.

```kusto
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a><a name="list-publicip"/>Vypsat všechny veřejné IP adresy

Podobně jako v předchozím dotazu se vyhledají všechny záznamy, jejichž typ obsahuje slovo **publicIPAddresses**.
Tento dotaz rozbalí tento model tak, aby obsahoval jenom výsledky, ve kterých **Vlastnosti. IPAddress**
`isnotempty`vrátí jenom **Vlastnosti. IPAddress**, a `limit` výsledky podle horní části.
100. V závislosti na zvoleném prostředí možná budete muset odebrat uvozovky.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"/>Počet prostředků, které mají IP adresy nakonfigurované pomocí předplatného

Pomocí předchozího ilustračního dotazu a přidáním `summarize` a `count()`, získáme seznam podle předplatného prostředků s nakonfigurovanými IP adresami.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a><a name="list-tag"/>Vypíše prostředky s určitou hodnotou značky.

Rozsah výsledků můžeme omezit podle vlastností jiných než typ prostředku Azure, jako je například značka. V tomto příkladu vyfiltrujeme prostředky Azure s názvem značky **Environment** (Prostředí) s hodnotou **Internal** (Interní).

```kusto
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

```kusto
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"/>Vypíše všechny účty úložiště s určitou hodnotou značky.

Zkombinujte funkci filtrování z předchozího příkladu a vyfiltrujte typ prostředku Azure podle vlastnosti **type** (Typ). Tento dotaz naše hledání omezuje také na konkrétní typy prostředků Azure s konkrétním názvem a hodnotou značky.

```kusto
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

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"/>Zobrazit aliasy pro prostředek virtuálního počítače

[Azure Policy aliasy](../../policy/concepts/definition-structure.md#aliases) používají Azure Policy ke správě dodržování předpisů prostředků. Graf prostředků Azure může vracet _aliasy_ typu prostředku. Tyto hodnoty jsou užitečné při porovnávání aktuální hodnoty aliasů při vytváření vlastní definice zásad. Pole _aliasy_ není ve výchozím nastavení k dispozici ve výsledcích dotazu. Slouží `project aliases` k explicitnímu přidání do výsledků.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"/>Zobrazit odlišné hodnoty pro konkrétní alias

Zobrazení hodnoty aliasů u jednoho prostředku je užitečné, ale nezobrazuje skutečnou hodnotu použití Azure Resource graphu k dotazování napříč předplatnými. Tento příklad vyhledá všechny hodnoty konkrétního aliasu a vrátí jedinečné hodnoty.

```kusto
where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)"
```

```azurecli-interactive
az graph query -q "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

## <a name="next-steps"></a>Další postup

- Další informace o [dotazovacím jazyku](../concepts/query-language.md)
- Naučte se [prozkoumat prostředky](../concepts/explore-resources.md)
- Zobrazit ukázky [Pokročilé dotazy](advanced.md)
