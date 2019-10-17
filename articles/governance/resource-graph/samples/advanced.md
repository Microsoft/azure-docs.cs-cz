---
title: Ukázky dotazů pro pokročilé
description: Pomocí Azure Resource graphu můžete spouštět některé pokročilé dotazy, včetně kapacity sady virtuálních počítačů škálovat, výpisu všech používaných značek a porovnání virtuálních počítačů s regulárními výrazy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 8b848eac338260340cb9160a72f33294e51101f2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387580"
---
# <a name="advanced-resource-graph-queries"></a>Pokročilé dotazy na Resource Graph

Prvním krokem k porozumění dotazům s Azure Resource Graph jsou základní znalosti [dotazovacího jazyka](../concepts/query-language.md). Pokud ještě neznáte [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), doporučujeme přečíst si základní informace, abyste pochopili, jak vytvářet požadavky na prostředky, které hledáte.

Projdeme následující rozšířené dotazy:

> [!div class="checklist"]
> - [Získat kapacitu a velikost sady škálování virtuálního počítače](#vmss-capacity)
> - [Odebrat sloupce z výsledků](#remove-column)
> - [Vypsat všechny názvy značek](#list-all-tags)
> - [Virtuální počítače odpovídající regulárnímu výrazu](#vm-regex)
> - [Seznam Cosmos DB s konkrétními umístěními pro zápis](#mvexpand-cosmosdb)
> - [Trezor klíčů s názvem předplatného](#join)
> - [Výpis databází SQL a jejich elastických fondů](#join-sql)
> - [Vypsat virtuální počítače s jejich síťovým rozhraním a veřejnou IP adresou](#join-vmpip)
> - [Vyhledání účtů úložiště s konkrétní značkou ve skupině prostředků](#join-findstoragetag)
> - [Kombinování výsledků ze dvou dotazů do jednoho výsledku](#unionresults)
> - [Zahrnutí názvů tenanta a předplatného do DisplayNames](#displaynames)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="language-support"></a>Podpora jazyků

Azure PowerShell (prostřednictvím modulu) a Azure CLI (prostřednictvím rozšíření) podporují Azure Resource Graph. Před spuštěním jakéhokoli z následujících dotazů zkontrolujte, že je vaše prostředí připravené. Zobrazte [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) a [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pro pokyny k instalaci a ověření vašeho výběru prostředí.

## <a name="vmss-capacity"></a>Získání kapacity a velikosti škálovací sady virtuálních počítačů

Tento dotaz hledá prostředky škálovací sady virtuálních počítačů a získá různé podrobnosti, včetně velikosti virtuálních počítačů a kapacity škálovací sady. Dotaz pomocí funkce `toint()` přetypuje kapacitu na číslo, aby bylo možné ji řadit. Nakonec se sloupce přejmenují na vlastní pojmenované vlastnosti.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="remove-column"></a>Odebrat sloupce z výsledků

Následující dotaz používá `summarize` k výpočtu prostředků podle předplatného, `join` pro jeho kombinaci s podrobnostmi o předplatném z tabulky _ResourceContainers_ , a pak `project-away` pro odebrání některých sloupců.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

## <a name="list-all-tags"></a>Vypsat všechny názvy značek

Tento dotaz spustí se značkou a vytvoří objekt JSON obsahující všechny jedinečné názvy značek a jejich odpovídající typy.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Virtuální počítače odpovídající regulárnímu výrazu

Tento dotaz vyhledá virtuální počítače, které odpovídají [regulárnímu výrazu](/dotnet/standard/base-types/regular-expression-language-quick-reference) (označovanému jako _regulární výraz_). **Výraz matchers \@** umožňuje definovat regulární výraz, který se má shodovat, což je `^Contoso(.*)[0-9]+$`.
Tato definice regulárního výrazu je vysvětlena jako:

- `^` – Porovnání musí začít na začátku řetězce.
- `Contoso` – Řetězec s rozlišováním velkých a malých písmen.
- `(.*)` – Shoda dílčího výrazu:
  - `.` – Odpovídá jakémukoli jednomu znaku (s výjimkou nového řádku).
  - `*` – Shoduje se s předchozím prvkem nulakrát nebo vícekrát.
- `[0-9]` – Shoda skupiny znaků pro čísla 0 až 9.
- `+` – Shoduje se s předchozím prvkem jednou nebo vícekrát.
- `$` – Shoda s předchozím prvkem se musí vyskytovat na konci řetězce.

Po porovnání podle názvu, dotaz promítne název a pořadí vzestupně podle názvu.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="mvexpand-cosmosdb"></a>Seznam Cosmos DB s konkrétními umístěními pro zápis

Následující dotaz omezuje Cosmos DB prostředků, používá `mv-expand` pro rozšíření kontejneru objektů a dat pro **Vlastnosti. writeLocations, nastavení**konkrétního projektu a omezení výsledků do **vlastností. writeLocations. Location** . odpovídá buď Východní USA, nebo Západní USA.

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

## <a name="join"></a>Trezor klíčů s názvem předplatného

Následující dotaz ukazuje komplexní použití `join`. Dotaz omezí propojenou tabulku na předplatná a s `project`, aby zahrnovala pouze původní pole _SubscriptionId_ a pole _Name_ bylo přejmenováno na _jméno_. Při přejmenování pole se zabrání `join` přidání jako _název1_ , protože pole již v _prostředcích_existuje. Původní tabulka je filtrována pomocí `where` a následující `project` zahrnuje sloupce z obou tabulek. Výsledkem dotazu je jeden Trezor klíčů, který zobrazuje typ, název trezoru klíčů a název předplatného, ve kterém je.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

## <a name="join-sql"></a>Výpis databází SQL a jejich elastických fondů

Následující dotaz používá **leftouter** `join` k spojování SQL Database prostředků a jejich souvisejících elastických fondů, pokud existují.

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

## <a name="join-vmpip"></a>Vypsat virtuální počítače s jejich síťovým rozhraním a veřejnou IP adresou

Tento dotaz používá dva **LeftOuter** příkazy `join` k spojování virtuálních počítačů, jejich souvisejících síťových rozhraní a všech veřejných IP adres souvisejících s těmito síťovými rozhraními.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mvexpand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mvexpand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

```azurecli-interactive
azure graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

## <a name="join-findstoragetag"></a>Vyhledání účtů úložiště s konkrétní značkou ve skupině prostředků

Následující dotaz používá **innerunique** `join` k připojení účtů úložiště se skupinami prostředků, které mají zadaný název značky a hodnotu značky.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=innerunique (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['key1'] == 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=innerunique ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

## <a name="unionresults"></a>Kombinování výsledků ze dvou dotazů do jednoho výsledku

Následující dotaz používá `union` k získání výsledků z tabulky _ResourceContainers_ a jejich přidání do výsledků z tabulky _Resources_ .

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

## <a name="displaynames"></a>Zahrnutí názvů tenanta a předplatného do DisplayNames

Tento dotaz pomocí nového parametru **include** s možností _DisplayNames_ přidá do výsledků **subscriptionDisplayName** a **tenantDisplayName** . Tento parametr je k dispozici pouze pro Azure CLI a Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

> [!NOTE]
> Pokud dotaz nepoužívá **aplikaci Project** k určení vrácených vlastností, jsou do výsledků automaticky zahrnuty **subscriptionDisplayName** a **tenantDisplayName** .
> Pokud dotaz používá **aplikaci Project**, musí být každé pole _DisplayName_ explicitně zahrnuto v **projektu** nebo nebude vráceno ve výsledcích, i když je použit parametr **include** .

## <a name="next-steps"></a>Další kroky

- Zobrazit ukázky [Starter dotazy](starter.md)
- Další informace o [dotazovacím jazyku](../concepts/query-language.md)
- Naučte se [prozkoumat prostředky](../concepts/explore-resources.md)