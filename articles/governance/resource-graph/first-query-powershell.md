---
title: Spuštění prvního dotazu pomocí Azure PowerShell
description: Tento článek vás provede kroky k povolení modulu Resource Graph pro Azure PowerShell a spuštění prvního dotazu.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 95cf19d4a782f9e4c866d31fac0da74aebff5d2d
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976777"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Rychlý Start: spuštění prvního dotazu na diagram prostředku pomocí Azure PowerShell

Prvním krokem při použití Azure Resource graphu je ověření, že je nainstalovaný modul pro Azure PowerShell. Tento rychlý Start vás provede procesem přidání modulu do instalace Azure PowerShell.

Na konci tohoto procesu jste přidali modul do Azure PowerShell instalace volby a spustíte svůj první dotaz na graf prostředku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="add-the-resource-graph-module"></a>Přidat modul grafu prostředků

Pokud chcete povolit Azure PowerShell dotazování na Azure Resource Graph, modul musí být přidaný. Tento modul se dá použít s místně nainstalovaným prostředím PowerShell, s [Azure Cloud Shell](https://shell.azure.com)nebo s [imagí Docker prostředí PowerShell](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Základní požadavky

Modul Azure Resource Graph vyžaduje následující software:

- Azure PowerShell 1.0.0 nebo vyšší. Pokud ještě není nainstalovaná, postupujte podle [těchto pokynů](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 nebo novější. Pokud není nainstalovaný nebo aktualizovaný, postupujte podle [těchto pokynů](/powershell/gallery/installing-psget).

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

## <a name="run-your-first-resource-graph-query"></a>Spustit váš první dotaz na diagram prostředku

Když je modul Azure PowerShell přidaný do vašeho prostředí dle vašeho výběru, je čas si vyzkoušet jednoduchý dotaz na graf prostředků. Dotaz vrátí prvních pět prostředků Azure s **názvem** a **typem prostředku** každého prostředku.

1. Spusťte svůj první dotaz na Azure Resource Graph pomocí rutiny `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Vzhledem k tomu, že tento příklad dotazu neposkytuje modifikátor řazení, jako je například `order by`, spuštění tohoto dotazu bude pravděpodobně zajišťovat jinou sadu prostředků na žádost.

1. Aktualizujte dotaz tak, aby `order by` vlastnost **Name** :

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Stejně jako u prvního dotazu může spuštění tohoto dotazu několikrát vyvolat jinou sadu prostředků na žádost. Pořadí příkazů dotazu je důležité. V tomto příkladu `order by` přichází za `limit`. Tím se nejdřív omezí výsledky dotazu a pak se seřadí.

1. Aktualizujte dotaz na první @no__t hodnotu vlastnosti **Name** a pak `limit` na prvních 5 výsledků:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Když se konečný dotaz několikrát spustí, za předpokladu, že se nic ve vašem prostředí nemění, vrácené výsledky budou konzistentní a podle očekávání – seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

> [!NOTE]
> Pokud dotaz nevrátí výsledky z předplatného, ke kterému už máte přístup, a pak si všimněte, že rutina `Search-AzGraph` je standardně předplatná ve výchozím kontextu. Pokud chcete zobrazit seznam ID předplatných, které jsou součástí výchozího kontextu, spusťte tuto `(Get-AzContext).Account.ExtendedProperties.Subscriptions` Pokud chcete hledat ve všech předplatných, ke kterým máte přístup, jedna může nastavit rutinu PSDefaultParameterValues pro `Search-AzGraph` spuštěním `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`.
   
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat modul grafu prostředků z prostředí Azure PowerShell, můžete k tomu použít následující příkaz:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Tímto se neodstraní dříve stažený soubor modulu. Odebere ho jenom z běžící relace PowerShellu.

## <a name="next-steps"></a>Další kroky

- Získat další informace o [dotazovacím jazyku](./concepts/query-language.md)
- Naučte se [prozkoumat prostředky](./concepts/explore-resources.md) .
- Spuštění prvního dotazu pomocí [Azure CLI](first-query-azurecli.md)
- Zobrazit ukázky [počátečních dotazů](./samples/starter.md)
- Zobrazit ukázky [pokročilých dotazů](./samples/advanced.md)
- Poskytnutí zpětné vazby na [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)