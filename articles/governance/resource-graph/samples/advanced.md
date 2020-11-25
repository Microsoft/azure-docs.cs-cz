---
title: Ukázky dotazů pro pokročilé
description: Pomocí Azure Resource graphu můžete spouštět některé rozšířené dotazy, včetně práce se sloupci, použitými značkami a porovnáním prostředků s regulárními výrazy.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: dff4b06cc5cf4385820c7f6251efaae792d9c22d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005397"
---
# <a name="advanced-resource-graph-query-samples"></a>Ukázky dotazů v rozšířeném grafu prostředků

Prvním krokem k porozumění dotazům s Azure Resource Graph jsou základní znalosti [dotazovacího jazyka](../concepts/query-language.md). Pokud ještě neznáte [Azure Data Explorer](/azure/data-explorer/data-explorer-overview), doporučujeme přečíst si základní informace, abyste pochopili, jak vytvářet požadavky na prostředky, které hledáte.

Projdeme následující rozšířené dotazy:

- [Zobrazit verzi rozhraní API pro každý typ prostředku](#apiversion)
- [Získat kapacitu a velikost sady škálování virtuálního počítače](#vmss-capacity)
- [Odebrat sloupce z výsledků](#remove-column)
- [Vypsat všechny názvy značek](#list-all-tags)
- [Virtuální počítače odpovídající regulárnímu výrazu](#vm-regex)
- [Seznam Cosmos DB s konkrétními umístěními pro zápis](#mvexpand-cosmosdb)
- [Trezory klíčů s názvem předplatného](#join)
- [Výpis databází SQL a jejich elastických fondů](#join-sql)
- [Vypsat virtuální počítače s jejich síťovým rozhraním a veřejnou IP adresou](#join-vmpip)
- [Vypíše všechna rozšíření nainstalovaná na virtuálním počítači.](#join-vmextension)
- [Vyhledání účtů úložiště s konkrétní značkou ve skupině prostředků](#join-findstoragetag)
- [Kombinování výsledků ze dvou dotazů do jednoho výsledku](#unionresults)
- [Zahrnutí názvů tenanta a předplatného do DisplayNames](#displaynames)
- [Sumarizace virtuálního počítače pomocí rozšířené vlastnosti stav napájení](#vm-powerstate)
- [Počet neodpovídajících přiřazení konfigurace hostů](#count-gcnoncompliant)
- [Dotaz na podrobnosti sestav přiřazení konfigurace hostů](#query-gcreports)
- [Najde všechny důvody, proč počítač nesplňuje požadavky na přiřazení konfigurace hostů.](#query-gcmachinedetails)

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free), ještě než začnete.

## <a name="language-support"></a>Podpora jazyků

Azure PowerShell (prostřednictvím modulu) a Azure CLI (prostřednictvím rozšíření) podporují Azure Resource Graph. Před spuštěním jakéhokoli z následujících dotazů zkontrolujte, že je vaše prostředí připravené. Zobrazte [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) a [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pro pokyny k instalaci a ověření vašeho výběru prostředí.

## <a name="show-resource-types-and-api-versions"></a><a name="apiversion"></a>Zobrazit typy prostředků a verze rozhraní API

Graf prostředků primárně používá nejnovější verzi rozhraní API poskytovatele prostředků, která není ve verzi Preview, do `GET` vlastností prostředků během aktualizace. V některých případech byla použitá verze rozhraní API přepsána tak, aby poskytovala více aktuálních nebo široce používaných vlastností ve výsledcích. Následující dotaz podrobně popisuje verzi rozhraní API, která se používá pro shromažďování vlastností každého typu prostředku:

```kusto
Resources
| distinct type, apiVersion
| where isnotnull(apiVersion)
| order by type asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20distinct%20type%2C%20apiVersion%0D%0A%7C%20where%20isnotnull%28apiVersion%29%0D%0A%7C%20order%20by%20type%20asc" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20distinct%20type%2C%20apiVersion%0D%0A%7C%20where%20isnotnull%28apiVersion%29%0D%0A%7C%20order%20by%20type%20asc" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20distinct%20type%2C%20apiVersion%0D%0A%7C%20where%20isnotnull%28apiVersion%29%0D%0A%7C%20order%20by%20type%20asc" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="get-virtual-machine-scale-set-capacity-and-size"></a><a name="vmss-capacity"></a>Získání kapacity a velikosti škálovací sady virtuálních počítačů

Tento dotaz hledá prostředky škálovací sady virtuálních počítačů a získá různé podrobnosti, včetně velikosti virtuálních počítačů a kapacity škálovací sady. Dotaz pomocí funkce `toint()` přetypuje kapacitu na číslo, aby bylo možné ji řadit. Nakonec se sloupce přejmenují na vlastní pojmenované vlastnosti.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%20%27microsoft.compute%2Fvirtualmachinescalesets%27%0D%0A%7C%20where%20name%20contains%20%27contoso%27%0D%0A%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint%28sku.capacity%29%2C%20Tier%20%3D%20sku.name%0D%0A%7C%20order%20by%20Capacity%20desc" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%20%27microsoft.compute%2Fvirtualmachinescalesets%27%0D%0A%7C%20where%20name%20contains%20%27contoso%27%0D%0A%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint%28sku.capacity%29%2C%20Tier%20%3D%20sku.name%0D%0A%7C%20order%20by%20Capacity%20desc" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%20%27microsoft.compute%2Fvirtualmachinescalesets%27%0D%0A%7C%20where%20name%20contains%20%27contoso%27%0D%0A%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint%28sku.capacity%29%2C%20Tier%20%3D%20sku.name%0D%0A%7C%20order%20by%20Capacity%20desc" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="remove-columns-from-results"></a><a name="remove-column"></a>Odebrat sloupce z výsledků

Následující dotaz slouží `summarize` k počítání prostředků podle předplatného, `join` aby ho bylo možné kombinovat s podrobnostmi o předplatných z tabulky _ResourceContainers_ a pak `project-away` odebrat některé sloupce.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20resourceCount%3Dcount%28%29%20by%20subscriptionId%0D%0A%7C%20join%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20resourceCount%3Dcount%28%29%20by%20subscriptionId%0D%0A%7C%20join%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20resourceCount%3Dcount%28%29%20by%20subscriptionId%0D%0A%7C%20join%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="list-all-tag-names"></a><a name="list-all-tags"></a>Vypsat všechny názvy značek

Tento dotaz spustí se značkou a vytvoří objekt JSON obsahující všechny jedinečné názvy značek a jejich odpovídající typy.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20tags%0D%0A%7C%20summarize%20buildschema%28tags%29" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20tags%0D%0A%7C%20summarize%20buildschema%28tags%29" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20tags%0D%0A%7C%20summarize%20buildschema%28tags%29" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="virtual-machines-matched-by-regex"></a><a name="vm-regex"></a>Virtuální počítače odpovídající regulárnímu výrazu

Tento dotaz vyhledá virtuální počítače, které odpovídají [regulárnímu výrazu](/dotnet/standard/base-types/regular-expression-language-quick-reference) (označovanému jako _regulární výraz_). **Výraz \@ Matchers** umožňuje definovat regulární výraz, který se má shodovat, což je `^Contoso(.*)[0-9]+$` .
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5EContoso%28.%2A%29%5B0-9%5D%2B%24%27%0D%0A%7C%20project%20name%0D%0A%7C%20order%20by%20name%20asc" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5EContoso%28.%2A%29%5B0-9%5D%2B%24%27%0D%0A%7C%20project%20name%0D%0A%7C%20order%20by%20name%20asc" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5EContoso%28.%2A%29%5B0-9%5D%2B%24%27%0D%0A%7C%20project%20name%0D%0A%7C%20order%20by%20name%20asc" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="list-cosmos-db-with-specific-write-locations"></a><a name="mvexpand-cosmosdb"></a>Seznam Cosmos DB s konkrétními umístěními pro zápis

Následující dotaz omezuje Cosmos DB prostředků, používá `mv-expand` Nástroj k rozšíření kontejneru objektů a dat pro **Vlastnosti. writeLocations**, pak pro konkrétní projekt a omezení výsledků do **vlastností. writeLocations. location** hodnoty odpovídající buď východní USA nebo západní USA.

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.documentdb%2Fdatabaseaccounts%27%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20%28properties.writeLocations%29%0D%0A%7C%20mv-expand%20writeLocations%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring%28writeLocations.locationName%29%0D%0A%7C%20where%20writeLocation%20in%20%28%27East%20US%27%2C%20%27West%20US%27%29%0D%0A%7C%20summarize%20by%20id%2C%20name" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.documentdb%2Fdatabaseaccounts%27%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20%28properties.writeLocations%29%0D%0A%7C%20mv-expand%20writeLocations%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring%28writeLocations.locationName%29%0D%0A%7C%20where%20writeLocation%20in%20%28%27East%20US%27%2C%20%27West%20US%27%29%0D%0A%7C%20summarize%20by%20id%2C%20name" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.documentdb%2Fdatabaseaccounts%27%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20%28properties.writeLocations%29%0D%0A%7C%20mv-expand%20writeLocations%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring%28writeLocations.locationName%29%0D%0A%7C%20where%20writeLocation%20in%20%28%27East%20US%27%2C%20%27West%20US%27%29%0D%0A%7C%20summarize%20by%20id%2C%20name" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="key-vaults-with-subscription-name"></a><a name="join"></a>Trezory klíčů s názvem předplatného

Následující dotaz ukazuje komplexní použití `join` s **druhem** jako _LeftOuter_. Dotaz omezí propojenou tabulku na prostředky předplatného a s tím, že `project` zahrne pouze původní pole _SubscriptionId_ a pole _název_ bylo přejmenováno na _subname_. Přejmenování pole zabraňuje `join` jeho přidání jako _název1_ , protože pole již v _prostředcích_ existuje. Původní tabulka je filtrována pomocí `where` a následující `project` obsahuje sloupce z obou tabulek. Výsledkem dotazu jsou všechny trezory klíčů, které zobrazují typ, název trezoru klíčů a název předplatného, které je v.

```kusto
Resources
| join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20join%20kind%3Dleftouter%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20project%20type%2C%20name%2C%20SubName" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20join%20kind%3Dleftouter%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20project%20type%2C%20name%2C%20SubName" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20join%20kind%3Dleftouter%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20project%20type%2C%20name%2C%20SubName" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="list-sql-databases-and-their-elastic-pools"></a><a name="join-sql"></a>Výpis databází SQL a jejich elastických fondů

Následující dotaz používá **LeftOuter** `join` k spojování SQL Database prostředků a jejich souvisejících elastických fondů, pokud jsou nějaké.

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Fdatabases%27%0D%0A%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower%28tostring%28properties.elasticPoolId%29%29%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Felasticpools%27%0D%0A%20%20%20%20%7C%20project%20elasticPoolId%20%3D%20tolower%28id%29%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state%29%0D%0Aon%20elasticPoolId%0D%0A%7C%20project-away%20elasticPoolId1" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Fdatabases%27%0D%0A%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower%28tostring%28properties.elasticPoolId%29%29%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Felasticpools%27%0D%0A%20%20%20%20%7C%20project%20elasticPoolId%20%3D%20tolower%28id%29%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state%29%0D%0Aon%20elasticPoolId%0D%0A%7C%20project-away%20elasticPoolId1" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Fdatabases%27%0D%0A%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower%28tostring%28properties.elasticPoolId%29%29%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Felasticpools%27%0D%0A%20%20%20%20%7C%20project%20elasticPoolId%20%3D%20tolower%28id%29%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state%29%0D%0Aon%20elasticPoolId%0D%0A%7C%20project-away%20elasticPoolId1" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="list-virtual-machines-with-their-network-interface-and-public-ip"></a><a name="join-vmpip"></a>Vypsat virtuální počítače s jejich síťovým rozhraním a veřejnou IP adresou

Tento dotaz používá dva příkazy **LeftOuter** `join` k spojování virtuálních počítačů vytvořených pomocí modelu nasazení Správce prostředků, jejich souvisejících síťových rozhraní a všech veřejných IP adres souvisejících s těmito síťovými rozhraními.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mv-expand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mv-expand ipconfig=properties.ipConfigurations 
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%0D%0A%7C%20extend%20nics%3Darray_length%28properties.networkProfile.networkInterfaces%29%20%0D%0A%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%0D%0A%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20%27true%27%20or%20isempty%28nic%29%20%0D%0A%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring%28properties.hardwareProfile.vmSize%29%2C%20nicId%20%3D%20tostring%28nic.id%29%20%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fnetworkinterfaces%27%0D%0A%20%20%20%20%7C%20extend%20ipConfigsCount%3Darray_length%28properties.ipConfigurations%29%20%0D%0A%20%20%20%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%0D%0A%20%20%20%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20%27true%27%0D%0A%20%20%20%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring%28ipconfig.properties.publicIPAddress.id%29%29%0D%0Aon%20nicId%0D%0A%7C%20project-away%20nicId1%0D%0A%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fpublicipaddresses%27%0D%0A%20%20%20%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress%29%0D%0Aon%20publicIpId%0D%0A%7C%20project-away%20publicIpId1" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%0D%0A%7C%20extend%20nics%3Darray_length%28properties.networkProfile.networkInterfaces%29%20%0D%0A%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%0D%0A%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20%27true%27%20or%20isempty%28nic%29%20%0D%0A%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring%28properties.hardwareProfile.vmSize%29%2C%20nicId%20%3D%20tostring%28nic.id%29%20%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fnetworkinterfaces%27%0D%0A%20%20%20%20%7C%20extend%20ipConfigsCount%3Darray_length%28properties.ipConfigurations%29%20%0D%0A%20%20%20%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%0D%0A%20%20%20%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20%27true%27%0D%0A%20%20%20%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring%28ipconfig.properties.publicIPAddress.id%29%29%0D%0Aon%20nicId%0D%0A%7C%20project-away%20nicId1%0D%0A%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fpublicipaddresses%27%0D%0A%20%20%20%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress%29%0D%0Aon%20publicIpId%0D%0A%7C%20project-away%20publicIpId1" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%0D%0A%7C%20extend%20nics%3Darray_length%28properties.networkProfile.networkInterfaces%29%20%0D%0A%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%0D%0A%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20%27true%27%20or%20isempty%28nic%29%20%0D%0A%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring%28properties.hardwareProfile.vmSize%29%2C%20nicId%20%3D%20tostring%28nic.id%29%20%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fnetworkinterfaces%27%0D%0A%20%20%20%20%7C%20extend%20ipConfigsCount%3Darray_length%28properties.ipConfigurations%29%20%0D%0A%20%20%20%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%0D%0A%20%20%20%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20%27true%27%0D%0A%20%20%20%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring%28ipconfig.properties.publicIPAddress.id%29%29%0D%0Aon%20nicId%0D%0A%7C%20project-away%20nicId1%0D%0A%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fpublicipaddresses%27%0D%0A%20%20%20%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress%29%0D%0Aon%20publicIpId%0D%0A%7C%20project-away%20publicIpId1" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="list-all-extensions-installed-on-a-virtual-machine"></a><a name="join-vmextension"></a>Vypíše všechna rozšíření nainstalovaná na virtuálním počítači.

Nejprve tento dotaz používá `extend` typ prostředku virtuální počítače k získání ID na velká písmena ( `toupper()` ) ID, získání názvu a typu operačního systému a získání velikosti virtuálního počítače.
Získání ID prostředku v horním případě je dobrým způsobem, jak připravit připojení k jiné vlastnosti. Pak dotaz používá `join` s **druhem** jako _LeftOuter_ k získání rozšíření virtuálních počítačů porovnáním horního použitau `substring` ID rozšíření. Část ID před "/Extensions/ \<ExtensionName\> " má stejný formát jako ID virtuálních počítačů, takže tuto vlastnost používáme pro `join` . `summarize` se pak používá s `make_list` názvem rozšíření virtuálního počítače pro kombinování názvu každého rozšíření, kde _ID_, _OSName_, _OSType_ a _VMSize_ jsou stejné jako jedna vlastnost Array. Nakonec jsme `order by` snížili použita _OSName_ na **ASC**. Ve výchozím nastavení `order by` je sestupně.

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| extend
    JoinID = toupper(id),
    OSName = tostring(properties.osProfile.computerName),
    OSType = tostring(properties.storageProfile.osDisk.osType),
    VMSize = tostring(properties.hardwareProfile.vmSize)
| join kind=leftouter(
    Resources
    | where type == 'microsoft.compute/virtualmachines/extensions'
    | extend 
        VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),
        ExtensionName = name
) on $left.JoinID == $right.VMId
| summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize
| order by tolower(OSName) asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend VMId = toupper(substring(id, 0, indexof(id, '/extensions'))), ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend VMId = toupper(substring(id, 0, indexof(id, '/extensions'))), ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0A%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines'%0A%7C%20extend%0A%20%20%20%20JoinID%20%3D%20toupper(id)%2C%0A%20%20%20%20OSName%20%3D%20tostring(properties.osProfile.computerName)%2C%0A%20%20%20%20OSType%20%3D%20tostring(properties.storageProfile.osDisk.osType)%2C%0A%20%20%20%20VMSize%20%3D%20tostring(properties.hardwareProfile.vmSize)%0A%7C%20join%20kind%3Dleftouter(%0A%20%20%20%20Resources%0A%20%20%20%20%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines%2Fextensions'%0A%20%20%20%20%7C%20extend%20%0A%20%20%20%20%20%20%20%20VMId%20%3D%20toupper(substring(id%2C%200%2C%20indexof(id%2C%20'%2Fextensions')))%2C%0A%20%20%20%20%20%20%20%20ExtensionName%20%3D%20name%0A)%20on%20%24left.JoinID%20%3D%3D%20%24right.VMId%0A%7C%20summarize%20Extensions%20%3D%20make_list(ExtensionName)%20by%20id%2C%20OSName%2C%20OSType%2C%20VMSize%0A%7C%20order%20by%20tolower(OSName)%20asc" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0A%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines'%0A%7C%20extend%0A%20%20%20%20JoinID%20%3D%20toupper(id)%2C%0A%20%20%20%20OSName%20%3D%20tostring(properties.osProfile.computerName)%2C%0A%20%20%20%20OSType%20%3D%20tostring(properties.storageProfile.osDisk.osType)%2C%0A%20%20%20%20VMSize%20%3D%20tostring(properties.hardwareProfile.vmSize)%0A%7C%20join%20kind%3Dleftouter(%0A%20%20%20%20Resources%0A%20%20%20%20%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines%2Fextensions'%0A%20%20%20%20%7C%20extend%20%0A%20%20%20%20%20%20%20%20VMId%20%3D%20toupper(substring(id%2C%200%2C%20indexof(id%2C%20'%2Fextensions')))%2C%0A%20%20%20%20%20%20%20%20ExtensionName%20%3D%20name%0A)%20on%20%24left.JoinID%20%3D%3D%20%24right.VMId%0A%7C%20summarize%20Extensions%20%3D%20make_list(ExtensionName)%20by%20id%2C%20OSName%2C%20OSType%2C%20VMSize%0A%7C%20order%20by%20tolower(OSName)%20asc" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0A%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines'%0A%7C%20extend%0A%20%20%20%20JoinID%20%3D%20toupper(id)%2C%0A%20%20%20%20OSName%20%3D%20tostring(properties.osProfile.computerName)%2C%0A%20%20%20%20OSType%20%3D%20tostring(properties.storageProfile.osDisk.osType)%2C%0A%20%20%20%20VMSize%20%3D%20tostring(properties.hardwareProfile.vmSize)%0A%7C%20join%20kind%3Dleftouter(%0A%20%20%20%20Resources%0A%20%20%20%20%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines%2Fextensions'%0A%20%20%20%20%7C%20extend%20%0A%20%20%20%20%20%20%20%20VMId%20%3D%20toupper(substring(id%2C%200%2C%20indexof(id%2C%20'%2Fextensions')))%2C%0A%20%20%20%20%20%20%20%20ExtensionName%20%3D%20name%0A)%20on%20%24left.JoinID%20%3D%3D%20%24right.VMId%0A%7C%20summarize%20Extensions%20%3D%20make_list(ExtensionName)%20by%20id%2C%20OSName%2C%20OSType%2C%20VMSize%0A%7C%20order%20by%20tolower(OSName)%20asc" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="find-storage-accounts-with-a-specific-tag-on-the-resource-group"></a><a name="join-findstoragetag"></a>Vyhledání účtů úložiště s konkrétní značkou ve skupině prostředků

Následující dotaz používá **vnitřní** `join` pro připojení účtů úložiště se skupinami prostředků, které mají zadaný název a hodnotu značky rozlišující velká a malá písmena.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['Key1'] =~ 'Value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20where%20tags%5B%27Key1%27%5D%20%3D~%20%27Value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20where%20tags%5B%27Key1%27%5D%20%3D~%20%27Value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20where%20tags%5B%27Key1%27%5D%20%3D~%20%27Value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

Pokud je nutné vyhledat název značky a hodnotu značky bez rozlišování velikosti písmen, použijte `mv-expand` parametr **bagexpansion** . Tento dotaz používá větší kvótu než předchozí dotaz, takže použijte `mv-expand` pouze v případě potřeby.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | mv-expand bagexpansion=array tags
    | where isnotempty(tags)
    | where tags[0] =~ 'key1' and tags[1] =~ 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%0D%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0D%0A%20%20%20%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%0D%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0D%0A%20%20%20%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%0D%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0D%0A%20%20%20%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="combine-results-from-two-queries-into-a-single-result"></a><a name="unionresults"></a>Kombinování výsledků ze dvou dotazů do jednoho výsledku

Následující dotaz slouží `union` k získání výsledků z tabulky _ResourceContainers_ a jejich přidání do výsledků z tabulky _Resources (prostředky_ ).

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0D%0A%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%0D%0A%7C%20union%20%20%28Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205%29" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0D%0A%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%0D%0A%7C%20union%20%20%28Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205%29" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0D%0A%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%0D%0A%7C%20union%20%20%28Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205%29" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="summarize-virtual-machine-by-the-power-states-extended-property"></a><a name="vm-powerstate"></a>Sumarizace virtuálního počítače pomocí rozšířené vlastnosti stav napájení

Tento dotaz pomocí [rozšířených vlastností](../concepts/query-language.md#extended-properties) na virtuálních počítačích shrnuje podle stavů napájení.


```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D%3D%20%27microsoft.compute%2Fvirtualmachines%27%20%7C%20summarize%20count%28%29%20by%20tostring%28properties.extended.instanceView.powerState.code%29" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D%3D%20%27microsoft.compute%2Fvirtualmachines%27%20%7C%20summarize%20count%28%29%20by%20tostring%28properties.extended.instanceView.powerState.code%29" target="_blank">Portal.Azure.us <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
- Portál Azure Čína 21Vianet: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D%3D%20%27microsoft.compute%2Fvirtualmachines%27%20%7C%20summarize%20count%28%29%20by%20tostring%28properties.extended.instanceView.powerState.code%29" target="_blank">Portal.Azure.cn <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="include-the-tenant-and-subscription-names-with-displaynames"></a><a name="displaynames"></a>Zahrnutí názvů tenanta a předplatného do DisplayNames

Tento dotaz používá parametr **include** s možností _DisplayNames_ k přidání **subscriptionDisplayName** a **tenantDisplayName** do výsledků. Tento parametr je k dispozici pouze pro Azure CLI a Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

Alternativou k získání názvu předplatného je použití `join` operátoru a připojení k tabulce **ResourceContainers** a `Microsoft.Resources/subscriptions` typu. `join` funguje v Azure CLI, Azure PowerShell, portálu a v podporované sadě SDK. Příklad najdete v tématu [trezor ukázek klíčů s názvem předplatného](#join).

> [!NOTE]
> Pokud dotaz nepoužívá **aplikaci Project** k určení vrácených vlastností, jsou do výsledků automaticky zahrnuty **subscriptionDisplayName** a **tenantDisplayName** .
> Pokud dotaz používá **aplikaci Project**, musí být každé pole _DisplayName_ explicitně zahrnuto v **projektu** nebo nebude vráceno ve výsledcích, i když je použit parametr **include** . Parametr **include** nefunguje s [tabulkami](../concepts/query-language.md#resource-graph-tables).

---

## <a name="count-of-non-compliant-guest-configuration-assignments"></a><a name="count-gcnoncompliant"></a>Počet neodpovídajících přiřazení konfigurace hostů

Zobrazí počet nevyhovujících počítačů na [důvod přiřazení konfigurace hostů](../../policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration). Omezí výsledky na prvních 100 pro výkon.

```kusto
GuestConfigurationResources
| extend vmid = split(properties.targetResourceId,'/')
| where properties.complianceStatus == 'NonCompliant'
| mvexpand properties.latestAssignmentReport.resources
| mvexpand properties_latestAssignmentReport_resources.reasons
| project machine = tostring(vmid[(-1)]),
    type = tostring(vmid[(-3)]),
    name,
    status = tostring(properties.complianceStatus),
    resource = tostring(properties_latestAssignmentReport_resources.resourceId),
    phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase)
| summarize count() by resource, name
| order by count_
| limit 100
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | where properties.complianceStatus == 'NonCompliant' | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase) | summarize count() by resource, name | order by count_ | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | where properties.complianceStatus == 'NonCompliant' | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase) | summarize count() by resource, name | order by count_ | limit 100"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%20%7C%20extend%20vmid%20%3D%20split(properties.targetResourceId%2C%22%2F%22)%20%7C%20where%20properties.complianceStatus%20%3D%3D%20'NonCompliant'%20%7C%20mvexpand%20properties.latestAssignmentReport.resources%20%7C%20mvexpand%20properties_latestAssignmentReport_resources.reasons%20%7C%20project%20machine%20%3D%20tostring(vmid%5B(-1)%5D)%2C%20type%20%3D%20tostring(vmid%5B(-3)%5D)%2C%20name%2C%20status%20%3D%20tostring(properties.complianceStatus)%2C%20resource%20%3D%20tostring(properties_latestAssignmentReport_resources.resourceId)%2C%20phrase%20%3D%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)%20%7C%20summarize%20count()%20by%20resource%2C%20name%20%7C%20order%20by%20count_%20%7C%20limit%20100" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="query-details-of-guest-configuration-assignment-reports"></a><a name="query-gcreports"></a>Dotaz na podrobnosti sestav přiřazení konfigurace hostů

Zobrazit sestavu z podrobností z [důvodu přiřazení konfigurace hosta](../../policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) V následujícím příkladu dotaz vrátí pouze výsledky, kde je název přiřazení hosta `installed_application_linux` a výstup obsahuje řetězec `Python` k vypsání všech počítačů se systémem Linux, kde je balíček nainstalován, který obsahuje název **Python**. Chcete-li zadat dotaz na dodržování předpisů u všech počítačů pro konkrétní přiřazení, odeberte druhou `where` klauzuli.

```kusto
GuestConfigurationResources
| extend vmid = split(properties.targetResourceId,'/')
| mvexpand properties.latestAssignmentReport.resources
| mvexpand properties_latestAssignmentReport_resources.reasons
| where name in ('installed_application_linux')
| where properties_latestAssignmentReport_resources_reasons.phrase contains 'Python'
| project machine = tostring(vmid[(-1)]),
    type = tostring(vmid[(-3)]),
    name,
    status = tostring(properties.complianceStatus),
    resource = tostring(properties_latestAssignmentReport_resources.resourceId),
    phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | where name in ('installed_application_linux') | where properties_latestAssignmentReport_resources_reasons.phrase contains 'Python' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring (properties_latestAssignmentReport_resources_reasons.phrase)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | where name in ('installed_application_linux') | where properties_latestAssignmentReport_resources_reasons.phrase contains 'Python' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]), name, status = tostring(properties.complianceStatus), resource = tostring(properties_latestAssignmentReport_resources.resourceId), phrase = tostring (properties_latestAssignmentReport_resources_reasons.phrase)"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%20%7C%20extend%20vmid%20%3D%20split(properties.targetResourceId%2C'%2F')%20%7C%20mvexpand%20properties.latestAssignmentReport.resources%20%7C%20mvexpand%20properties_latestAssignmentReport_resources.reasons%20%7C%20where%20name%20in%20('installed_application_linux')%20%7C%20where%20properties_latestAssignmentReport_resources_reasons.phrase%20contains%20'Python'%20%7C%20project%20machine%20%3D%20tostring(vmid%5B(-1)%5D)%2C%20type%20%3D%20tostring(vmid%5B(-3)%5D)%2C%20name%2C%20status%20%3D%20tostring(properties.complianceStatus)%2C%20resource%20%3D%20tostring(properties_latestAssignmentReport_resources.resourceId)%2C%20phrase%20%3D%20tostring%20(properties_latestAssignmentReport_resources_reasons.phrase)" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="find-all-reasons-a-machine-is-non-compliant-for-guest-configuration-assignments"></a><a name="query-gcmachinedetails"></a>Najde všechny důvody, proč počítač nesplňuje požadavky na přiřazení konfigurace hostů.

Zobrazí všechny [důvody přiřazení konfigurace hostů](../../policy/how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) pro určitý počítač. Odeberte první `where` klauzuli, která zahrnuje audity, ve kterých je počítač kompatibilní.

```kusto
GuestConfigurationResources
| where properties.complianceStatus == 'NonCompliant'
| extend vmid = split(properties.targetResourceId,'/')
| mvexpand properties.latestAssignmentReport.resources
| mvexpand properties_latestAssignmentReport_resources.reasons
| extend machine = tostring(vmid[(-1)])
| where machine == 'MACHINENAME'
| project phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase),
    resource = tostring(properties_latestAssignmentReport_resources.resourceId),
    name,
    machine,
    resourceGroup,
    subscriptionId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | where properties.complianceStatus == 'NonCompliant' | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | extend machine = tostring(vmid[(-1)]) | where machine == 'MACHINENAME' | project phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase), resource = tostring(properties_latestAssignmentReport_resources.resourceId), name, machine, resourceGroup, subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | where properties.complianceStatus == 'NonCompliant' | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | mvexpand properties_latestAssignmentReport_resources.reasons | extend machine = tostring(vmid[(-1)]) | where machine == 'MACHINENAME' | project phrase = tostring(properties_latestAssignmentReport_resources_reasons.phrase), resource = tostring(properties_latestAssignmentReport_resources.resourceId), name, machine, resourceGroup, subscriptionId"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%20%7C%20where%20properties.complianceStatus%20%3D%3D%20'NonCompliant'%20%7C%20extend%20vmid%20%3D%20split(properties.targetResourceId%2C'%2F')%20%7C%20mvexpand%20properties.latestAssignmentReport.resources%20%7C%20mvexpand%20properties_latestAssignmentReport_resources.reasons%20%7C%20extend%20machine%20%3D%20tostring(vmid%5B(-1)%5D)%20%7C%20where%20machine%20%3D%3D%20'MACHINENAME'%20%7C%20project%20phrase%20%3D%20tostring(properties_latestAssignmentReport_resources_reasons.phrase)%2C%20resource%20%3D%20tostring(properties_latestAssignmentReport_resources.resourceId)%2C%20name%2C%20machine%2C%20resourceGroup%2C%20subscriptionId" target="_blank">Portal.Azure.com <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

## <a name="next-steps"></a>Další kroky

- Podívejte se na ukázky [počátečních dotazů](starter.md).
- Přečtěte si další informace o [dotazovacím jazyce](../concepts/query-language.md).
- Přečtěte si další informace o tom, jak [prozkoumat prostředky](../concepts/explore-resources.md).