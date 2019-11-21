---
title: 'Quickstart: Your first PowerShell query'
description: In this quickstart, you follow the steps to enable the Resource Graph module for Azure PowerShell and run your first query.
ms.date: 10/18/2019
ms.topic: quickstart
ms.openlocfilehash: de0103ea5fe12ae26ac64d04a8efc50b9e708fc5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74216461"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Quickstart: Run your first Resource Graph query using Azure PowerShell

Prvním krokem k použití služby Azure Resource Graph je zkontrolovat, že je nainstalovaný modul pro Azure PowerShell. Tento rychlý start vás provede procesem přidání modulu do instalace Azure PowerShell.

Na konci tohoto procesu budete mít za sebou přidání modulu k vybrané instalaci Azure PowerShellu a spuštění prvního dotazu na službu Resource Graph.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="add-the-resource-graph-module"></a>Přidat modul k Resource Graph

Chcete-li pro Azure PowerShell povolit dotaz na Azure Resource Graph, musí být modul přidán. This module can be used with locally installed PowerShell, with [Azure Cloud Shell](https://shell.azure.com), or with the [PowerShell Docker image](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Základní požadavky

Modul Azure Resource Graph vyžaduje následující software:

- Azure PowerShell 1.0.0 or higher. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 nebo novější. Pokud není nainstalovaný nebo aktualizovaný, postupujte podle [těchto pokynů](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Install the module

The Resource Graph module for PowerShell is **Az.ResourceGraph**.

1. From an **administrative** PowerShell prompt, run the following command:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Validate that the module has been imported and is the latest version (0.7.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Když se modul Azure PowerShell přidal do vašeho vybraného prostředí, můžete vyzkoušet jednoduchý dotaz na službu Resource Graph. Dotaz vrátí prvních pět zdrojů Azure pomocí **Názvem** a **Typem zdroje** každého zdroje.

1. Spusťte první dotaz Azure Resource Graph použitím `Search-AzGraph` cmdlet:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Na tento dotaz příklad neposkytuje modifikátor řazení jako `order by`, opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost.

1. Aktualizuje dotaz pro `order by` **Název** vlastnosti:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Tak se nejdřív omezí rozsah výsledků dotazu a ty se pak seřadí.

1. Aktualizujte dotaz tak, aby se nejprve výsledky seřadily podle názvu (nastavte `order by` na **Name**) a pak nastavte omezení (`limit`) na prvních pět výsledků:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Pokud se konečný dotaz spustí několikrát, za předpokladu, že se ve vašem prostředí nic nemění, budou vrácené výsledky konzistentní a podle očekávání – seřazené podle vlastnosti **Name**, ale stále s omezením na prvních pět výsledků.

> [!NOTE]
> If the query does not return results from a subscription you already have access to, then note that `Search-AzGraph` cmdlet defaults to subscriptions in the default context. To see the list of subscription IDs which are part of the default context run this `(Get-AzContext).Account.ExtendedProperties.Subscriptions` If you wish to search across all the subscriptions you have access to, one can set the PSDefaultParameterValues for `Search-AzGraph` cmdlet by running `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat modul Resource Graph z prostředí Azure PowerShell, můžete tak provést pomocí následujícího příkazu:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Tím se neodstraní dříve stažený soubor s modulem. Pouze ho odebere ze spuštěného relace PowerShell.

## <a name="next-steps"></a>Další kroky

- Get more information about the [query language](./concepts/query-language.md).
- Learn more about how to [explore resources](./concepts/explore-resources.md).
- Run your first query by using the [Azure portal](first-query-portal.md).
- Run your first query with [Azure CLI](first-query-azurecli.md).
- See samples of [Starter queries](./samples/starter.md).
- See samples of [Advanced queries](./samples/advanced.md).
- Provide feedback on [UserVoice](https://feedback.azure.com/forums/915958-azure-governance).