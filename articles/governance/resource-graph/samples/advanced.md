---
title: Ukázky dotazů pro pokročilé
description: Pomocí Azure Resource Graph spusťte některé pokročilé dotazy, včetně práce se sloupci, výpisu použitých značek a odpovídajících prostředků s regulárními výrazy.
ms.date: 03/20/2020
ms.topic: sample
ms.openlocfilehash: 98e80d0977ec23cba8b8c497373b6e89b52d430e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130512"
---
# <a name="advanced-resource-graph-query-samples"></a>Ukázky dotazů na pokročilý graf prostředků

Prvním krokem k porozumění dotazům s Azure Resource Graph jsou základní znalosti [dotazovacího jazyka](../concepts/query-language.md). Pokud ještě neznáte [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), doporučujeme přečíst si základní informace, abyste pochopili, jak vytvářet požadavky na prostředky, které hledáte.

Projdeme následující rozšířené dotazy:

> [!div class="checklist"]
> - [Zobrazit verzi rozhraní API pro každý typ prostředku](#apiversion)
> - [Získání kapacity a velikosti škálovací sady virtuálních strojů](#vmss-capacity)
> - [Odebrání sloupců z výsledků](#remove-column)
> - [Vypsat všechny názvy značek](#list-all-tags)
> - [Virtuální počítače odpovídající regulárnímu výrazu](#vm-regex)
> - [Seznam Cosmos DB s konkrétními umístěními zápisu](#mvexpand-cosmosdb)
> - [Trezor klíčů s názvem předplatného](#join)
> - [Seznam databází SQL a jejich elastických fondů](#join-sql)
> - [Seznam virtuálních počítačů s jejich síťovým rozhraním a veřejnou IP adresou](#join-vmpip)
> - [Vyhledání účtů úložiště s určitou značkou ve skupině prostředků](#join-findstoragetag)
> - [Sloučení výsledků ze dvou dotazů do jednoho výsledku](#unionresults)
> - [Zahrnutí názvů tenanta a předplatného pomocí DisplayNames](#displaynames)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free) než začnete.

## <a name="language-support"></a>Podpora jazyků

Azure PowerShell (prostřednictvím modulu) a Azure CLI (prostřednictvím rozšíření) podporují Azure Resource Graph. Před spuštěním jakéhokoli z následujících dotazů zkontrolujte, že je vaše prostředí připravené. Zobrazte [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) a [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pro pokyny k instalaci a ověření vašeho výběru prostředí.

## <a name="show-resource-types-and-api-versions"></a><a name="apiversion" />Zobrazit typy prostředků a verze rozhraní API

Resource Graph používá především nejnovější verzi rozhraní API zprostředkovatele prostředků `GET` bez náhledu na vlastnosti prostředků během aktualizace. V některých případech byla použitá verze rozhraní API přepsána, aby poskytovala aktuálnější nebo široce používané vlastnosti ve výsledcích. Následující dotaz podrobně popisuje verzi rozhraní API používanou pro shromažďování vlastností pro každý typ prostředku:

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

## <a name="get-virtual-machine-scale-set-capacity-and-size"></a><a name="vmss-capacity" />Získání kapacity a velikosti škálovací sady virtuálních počítačů

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

## <a name="remove-columns-from-results"></a><a name="remove-column" />Odebrání sloupců z výsledků

Následující dotaz `summarize` používá k počítání `join` prostředků podle předplatného, kombinovat s podrobnosti o předplatném z _tabulky ResourceContainers_ a potom `project-away` odebrat některé sloupce.

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

## <a name="list-all-tag-names"></a><a name="list-all-tags" />Vypsat všechny názvy značek

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

## <a name="virtual-machines-matched-by-regex"></a><a name="vm-regex" />Virtuální počítače odpovídající regulárnímu výrazu

Tento dotaz vyhledá virtuální počítače, které odpovídají [regulárnímu výrazu](/dotnet/standard/base-types/regular-expression-language-quick-reference) (označovanému jako _regulární výraz_). Zápasy **regulární \@ nám** umožňuje definovat regulární výraz tak, aby odpovídaly, což je `^Contoso(.*)[0-9]+$`.
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

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

## <a name="list-cosmos-db-with-specific-write-locations"></a><a name="mvexpand-cosmosdb" />Seznam Cosmos DB s konkrétními umístěními zápisu

Následující dotaz omezuje prostředky Cosmos `mv-expand` DB, používá k rozšíření vak vlastností pro **properties.writeLocations**, pak projekt konkrétní pole a omezit výsledky dále **vlastnosti.writeLocations.locationName** hodnoty odpovídající buď 'Východ USA ' nebo 'Západ USA'.

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

## <a name="key-vault-with-subscription-name"></a><a name="join" />Trezor klíčů s názvem předplatného

Následující dotaz ukazuje komplexní `join`použití . Dotaz omezuje připojenou tabulku na prostředky `project` odběrů a zahrnuje pouze původní _pole subscriptionId_ a pole _název_ přejmenované na _SubName_. Přejmenování pole zabrání `join` jeho přidání jako _názvu1,_ protože pole již ve _zdrojích_existuje . Původní tabulka je filtrována `where` `project` a následující obsahuje sloupce z obou tabulek. Výsledkem dotazu je jeden trezor klíčů zobrazující typ, název trezoru klíčů a název předplatného, ve které se nachází.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

## <a name="list-sql-databases-and-their-elastic-pools"></a><a name="join-sql" />Seznam databází SQL a jejich elastických fondů

Následující dotaz používá **leftouter** `join` sdružovat prostředky databáze SQL a jejich související elastické fondy, pokud mají nějaké.

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

## <a name="list-virtual-machines-with-their-network-interface-and-public-ip"></a><a name="join-vmpip" />Seznam virtuálních počítačů s jejich síťovým rozhraním a veřejnou IP adresou

Tento dotaz používá dva **příkazy leftouter** `join` ke spojení virtuálních počítačů, jejich souvisejících síťových rozhraní a všech veřejných IP adres souvisejících s těmito síťovými rozhraními.

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

## <a name="find-storage-accounts-with-a-specific-tag-on-the-resource-group"></a><a name="join-findstoragetag" />Vyhledání účtů úložiště s určitou značkou ve skupině prostředků

Následující dotaz používá **vnitřní** `join` pro připojení účtů úložiště se skupinami prostředků, které mají zadaný název značky rozlišující malá a velká písmena a hodnotu značky.

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

Pokud je nutné vyhledat název značky bez rozlišování `mv-expand` velkých a malých písmen a hodnotu značky, použijte parametr **bagexpansion.** Tento dotaz používá více kvóty než `mv-expand` předchozí dotaz, proto použijte pouze v případě potřeby.

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

## <a name="combine-results-from-two-queries-into-a-single-result"></a><a name="unionresults" />Sloučení výsledků ze dvou dotazů do jednoho výsledku

Následující dotaz `union` používá k získání výsledků z tabulky _ResourceContainers_ a jejich přidání do výsledků z tabulky _Prostředky._

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

![Ikona aplikace Resource Graph Explorer](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku zdrojů Azure:

- Portál Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">portal.azure.com</a> ![odkaz Otevřít v novém okně](../../media/new-window.png)
- Portál Azure Government: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">portal.azure.us</a> ![otevřít odkaz v novém okně](../../media/new-window.png)
- Portál Azure China: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">portal.azure.cn</a> ![otevřít odkaz v novém okně](../../media/new-window.png)

---

## <a name="include-the-tenant-and-subscription-names-with-displaynames"></a><a name="displaynames" />Zahrnutí názvů tenanta a předplatného pomocí DisplayNames

Tento dotaz používá nový parametr **Include** s možností _DisplayNames_ k přidání **předplatnéhoDisplayName** a **tenantDisplayName** k výsledkům. Tento parametr je k dispozici jenom pro Azure CLI a Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

Alternativou k získání názvu předplatného `join` je použít operátor a připojit `Microsoft.Resources/subscriptions` se k **ResourceContainers** tabulka a typ. `join`funguje v Azure CLI, Azure PowerShell, portál a všechny podporované Sady SDK. Příklad najdete v [tématu Ukázka - trezor klíčů s názvem předplatného](#join).

> [!NOTE]
> Pokud dotaz nepoužívá **projekt** k určení vrácených vlastností, **subscriptionDisplayName** a **tenantDisplayName** jsou automaticky zahrnuty do výsledků.
> Pokud dotaz používá **projekt**, musí být každé pole _DisplayName_ explicitně zahrnuto do **projektu** nebo nebudou vráceny ve výsledcích, i když je použit parametr **Include.** Parametr **Include** nefunguje s [tabulkami](../concepts/query-language.md#resource-graph-tables).

## <a name="next-steps"></a>Další kroky

- Viz ukázky [počátečnídotazy](starter.md).
- Přečtěte si další informace o [dotazovacím jazyce](../concepts/query-language.md).
- Přečtěte si další informace o tom, jak [prozkoumat zdroje](../concepts/explore-resources.md).