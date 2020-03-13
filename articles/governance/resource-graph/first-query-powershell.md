---
title: 'Rychlý Start: váš první dotaz na PowerShell'
description: V tomto rychlém startu budete postupovat podle pokynů pro povolení modulu Resource Graph pro Azure PowerShell a spuštění prvního dotazu.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: dd96324671f46f98d5b6c8bae1839a5b02d38b23
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240657"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Rychlý Start: spuštění prvního dotazu na diagram prostředku pomocí Azure PowerShell

Prvním krokem k použití služby Azure Resource Graph je zkontrolovat, že je nainstalovaný modul pro Azure PowerShell. Tento rychlý start vás provede procesem přidání modulu do instalace Azure PowerShell.

Na konci tohoto procesu budete mít za sebou přidání modulu k vybrané instalaci Azure PowerShellu a spuštění prvního dotazu na službu Resource Graph.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>Přidat modul k Resource Graph

Chcete-li pro Azure PowerShell povolit dotaz na Azure Resource Graph, musí být modul přidán. Tento modul se dá použít s místně nainstalovaným prostředím PowerShell, s [Azure Cloud Shell](https://shell.azure.com)nebo s [imagí Docker prostředí PowerShell](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Základní požadavky

Modul Azure Resource Graph vyžaduje následující software:

- Azure PowerShell 1.0.0 nebo vyšší. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 nebo novější. Pokud není nainstalovaný nebo aktualizovaný, postupujte podle [těchto pokynů](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Nainstalovat modul

Modul Resource Graph pro PowerShell je **AZ. ResourceGraph**.

1. Na příkazovém řádku PowerShellu **pro správu** spusťte následující příkaz:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Ověřte, že je modul naimportovaný a má nejnovější verzi (0.7.5):

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

1. Aktualizuje dotaz pro `order by`**Název** vlastnosti:

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
> Pokud dotaz nevrátí výsledky z předplatného, ke kterému už máte přístup, a pak si všimněte, že `Search-AzGraph` rutina se standardně předplatná ve výchozím kontextu. Pokud chcete zobrazit seznam ID předplatných, které jsou součástí výchozího kontextu, spusťte tento `(Get-AzContext).Account.ExtendedProperties.Subscriptions`, pokud chcete hledat ve všech předplatných, ke kterým máte přístup, a to tak, že nakonfigurujete rutinu PSDefaultParameterValues for `Search-AzGraph` spuštěním `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
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

V tomto rychlém startu jste přidali modul Resource Graph do prostředí Azure PowerShell a spustíte svůj první dotaz. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)