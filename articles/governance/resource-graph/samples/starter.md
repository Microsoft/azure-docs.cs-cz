---
title: Ukázky počátečních dotazů
description: Pomocí Azure Resource graphu můžete spouštět některé úvodní dotazy, včetně počítání prostředků, řazení prostředků nebo konkrétní značky.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 14bac2299505214b8b087946222c5560a9d90efd
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976869"
---
# <a name="starter-resource-graph-queries"></a>Dotazy na zdrojový graf Starter

Prvním krokem pro porozumění dotazům s Azure Resource Graph je základní porozumění [dotazovacímu jazyku](../concepts/query-language.md). Pokud vám [Azure Průzkumník dat](../../../data-explorer/data-explorer-overview.md)ještě neznáte, doporučujeme si projít základní informace, které vám pomůžou pochopit, jak vytvářet požadavky na prostředky, které hledáte.

Provedeme vás následujícími úvodními dotazy:

> [!div class="checklist"]
> - [Počet prostředků Azure](#count-resources)
> - [Vypsat prostředky seřazené podle názvu](#list-resources)
> - [Zobrazit všechny virtuální počítače seřazené podle názvu v sestupném pořadí](#show-vms)
> - [Zobrazit prvních pět virtuálních počítačů podle názvu a jejich typu operačního systému](#show-sorted)
> - [Počet virtuálních počítačů podle typu operačního systému](#count-os)
> - [Zobrazit prostředky, které obsahují úložiště](#show-storage)
> - [Vypsat všechny veřejné IP adresy](#list-publicip)
> - [Počet prostředků, které mají IP adresy nakonfigurované pomocí předplatného](#count-resources-by-ip)
> - [Vypíše prostředky s určitou hodnotou značky.](#list-tag)
> - [Vypíše všechny účty úložiště s určitou hodnotou značky.](#list-specific-tag)
> - [Zobrazit aliasy pro prostředek virtuálního počítače](#show-aliases)
> - [Zobrazit odlišné hodnoty pro konkrétní alias](#distinct-alias-values)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="language-support"></a>Podpora jazyků

Azure CLI (prostřednictvím rozšíření) a Azure PowerShell (prostřednictvím modulu) podporují Azure Resource Graph. Než spustíte některý z následujících dotazů, ověřte, že je vaše prostředí připravené. Postup instalace a ověření prostředí prostředí podle volby najdete v tématu [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) a [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) .

## <a name="a-namecount-resourcescount-azure-resources"></a>prostředky Azure @no__t 0Count

Tento dotaz vrátí počet prostředků Azure, které existují v předplatných, ke kterým máte přístup. Je také dobrým dotazem, jak ověřit, že vaše prostředí má volba, má nainstalované příslušné součásti Azure Resource graphu a v pracovním řádu.

```kusto
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a>@no__t 0List prostředky seřazené podle názvu

Tento dotaz vrátí jakýkoliv typ prostředku, ale pouze vlastnosti **název**, **typ**a **umístění** . Používá `order by` k řazení vlastností podle vlastnosti **Name** ve vzestupném pořadí (`asc`).

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

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a>@no__t – 0Show všechny virtuální počítače seřazené podle názvu v sestupném pořadí

K vypsání pouze virtuálních počítačů (které jsou typu `Microsoft.Compute/virtualMachines`) můžeme ve výsledcích porovnat **typ** vlastnosti. Podobně jako u předchozího dotazu `desc` změní hodnotu `order by` na sestupně. @No__t-0 v shodě typů oznamuje grafu prostředků, aby se nerozlišuje velká a malá písmena.

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

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a>@no__t – 0Show prvních pět virtuálních počítačů podle názvu a jejich typu operačního systému

Tento dotaz použije `top` k načtení pouze pěti vyhovujících záznamů seřazených podle názvu. Typ prostředku Azure je `Microsoft.Compute/virtualMachines`. `project` oznamuje grafu prostředků Azure, které vlastnosti se mají zahrnout.

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

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a>virtuální počítače s @no__t 0Count podle typu operačního systému

Při sestavování na předchozím dotazu pořád omezujeme prostředky Azure typu `Microsoft.Compute/virtualMachines`, ale už neomezuje počet vrácených záznamů.
Místo toho jsme použili `summarize` a `count()` k definování způsobu, jak seskupit a agregovat hodnoty podle vlastnosti, která v tomto příkladu je `properties.storageProfile.osDisk.osType`. Příklad toho, jak tento řetězec vypadá v plném objektu, najdete v tématu [prozkoumání prostředků – zjišťování virtuálních počítačů](../concepts/explore-resources.md#virtual-machine-discovery).

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

Jiný způsob, jak napsat stejný dotaz, je `extend` a vlastnost a dát mu dočasný název pro použití v rámci dotazu, v tomto případě v **operačním systému**. **operační systém** se pak používá `summarize` a `count()` jako v předchozím příkladu.

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
> Počítejte s tím, že zatímco `=~` umožňuje porovnávání bez rozlišení velkých a malých písmen, použití vlastností (například **Properties. storageProfile. osDisk. osType**) v dotazu vyžaduje správné zadání případu. Pokud je vlastnost nesprávného případu, může stále vracet hodnotu, ale seskupení nebo sumarizace by byly nesprávné.

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a>@no__t 0Show prostředky, které obsahují úložiště

Namísto explicitního definování typu, který se má shodovat, tento ukázkový dotaz vyhledá libovolný prostředek Azure, který @no__t – 0 wordové **úložiště**.

```kusto
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a>@no__t – 0List všechny veřejné IP adresy

Podobně jako u předchozího dotazu vyhledejte vše, co je typ se slovem **publicIPAddresses**.
Tento dotaz rozšiřuje tento model tak, aby zahrnoval jenom výsledky, ve kterých **Vlastnosti. ipaddress**
 @ no__t-2, aby vrátil jenom **Vlastnosti. IPAddress**a `limit` výsledky podle horní části.
100. V závislosti na zvolené prostředí možná budete muset tyto uvozovky zařídí.

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

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a>prostředky @no__t 0Count s IP adresami nakonfigurovanými předplatným

Když použijete předchozí příklad dotazu a přidáte `summarize` a `count()`, můžeme získat seznam pomocí předplatného prostředků s nakonfigurovanými IP adresami.

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

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a>@no__t 0List prostředky s určitou hodnotou značky

Výsledky můžeme omezit na jiné vlastnosti než typ prostředku Azure, jako je například značka. V tomto příkladu filtrujeme prostředky Azure s názvem značky **prostředí** , které má hodnotu **interní**.

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

Chcete-li také zadat, jaké značky má prostředek a jejich hodnoty, přidejte **značky** vlastností do klíčového slova `project`.

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

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a>@no__t – 0List všechny účty úložiště s určitou hodnotou značky

Zkombinujte funkce filtru v předchozím příkladu a filtrujte typ prostředku Azure podle vlastnosti **typu** . Tento dotaz také omezí naše hledání na konkrétní typy prostředků Azure s určitým názvem a hodnotou značky.

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
> Tento příklad používá `==` pro porovnání namísto podmíněného `=~`. `==` se shoduje rozlišovat velká a malá písmena.

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a>aliasy <a name="show-aliases"/>Show pro prostředek virtuálního počítače

[Azure Policy aliasy](../../policy/concepts/definition-structure.md#aliases) používají Azure Policy ke správě dodržování předpisů prostředků. Graf prostředků Azure může vracet _aliasy_ typu prostředku. Tyto hodnoty jsou užitečné při porovnávání aktuální hodnoty aliasů při vytváření vlastní definice zásad. Pole _aliasy_ není ve výchozím nastavení k dispozici ve výsledcích dotazu. K explicitnímu přidání do výsledků použijte `project aliases`.

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

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a>@no__t – 0Show jedinečné hodnoty pro konkrétní alias

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

## <a name="next-steps"></a>Další kroky

- Další informace o [dotazovacím jazyku](../concepts/query-language.md)
- Naučte se [prozkoumat prostředky](../concepts/explore-resources.md) .
- Zobrazit ukázky [pokročilých dotazů](advanced.md)
