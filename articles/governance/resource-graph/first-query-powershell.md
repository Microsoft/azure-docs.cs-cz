---
title: Spusťte nejdříve dotaz na Resource Graph použitím Azure PowerShell
description: Tento článek vás provede kroky pro povolení modulu Resource Graph pro prostředí Azure PowerShell a spusťte svůj první dotaz.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 1a2bc5626e94f5fcb0ec8c2be8d91c8fc6484e0b
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224558"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure PowerShell

Prvním krokem k použití Azure Resource Graph je zajistit, aby bylo nainstalováno rozšíření Azure PowerShell. Tento rychlý start vás provede procesem přidání modulu do instalace Azure PowerShell.

Na konci tohoto procesu přidáte modul k vybrané instalaci Azure PowerShell a spustíte svůj první dotaz na Resource Graph.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="add-the-resource-graph-module"></a>Přidat modul k Resource Graph

Chcete-li pro Azure PowerShell povolit dotaz na Azure Resource Graph, musí být modul přidán. Tento modul lze použít s lokálně nainstalovaným systémem Windows PowerShell a PowerShell Core, stejně jako s [ Azure PowerShell Docker image](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Základní požadavky

Modul Azure Resource Graph vyžaduje následující software:

- Azure PowerShell 6.3.0 nebo vyšší. Pokud ještě není nainstalován, postupujte bod [ těchto pokynů ](/powershell/azure/install-azurerm-ps).

  - Pro PowerShell Core použijte verzi **Az** modulu Azure PowerShell.

  - Pro Windows PowerShell použijte verzi **AzureRm** modulu Azure PowerShell.

  > [!NOTE]
  > Aktuálně není doporučeno nainstalovat modul ve službě Cloud Shell.

- PowerShellGet. Pokud není nainstalovaný nebo aktualizovat, postupujte podle bodu [těchto pokynů](/powershell/gallery/installing-psget).

### <a name="powershell-core"></a>PowerShell Core

Modulem Resource Graph pro PowerShell Core je **Az.ResourceGraph**.

1. Z příkazového **administrativního**  řádku PowerShell Core spusťte následující příkaz:

   ```powershell
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Ověřte, že modul byl naimportován a jde o správnou verzi (0.2.0):

   ```powershell
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

1. Aliasy pro povolení zpětné **Az** k **AzureRm** pomocí následujícího příkazu:

   ```powershell
   # Enable backwards alias compatibility
   Enable-AzureRmAlias
   ```

### <a name="windows-powershell"></a>Windows PowerShell

Modul Resource Graph pro Windows PowerShell je **AzureRm.ResourceGraph**.

1. Z příkazového **administrativního** řádku Windows PowerShell spusťte následující příkaz:

   ```powershell
   # Install the Resource Graph (prerelease) module from PowerShell Gallery
   Install-Module -Name AzureRm.ResourceGraph -AllowPrerelease
   ```

1. Ověřte, že modul byla naimportováný a má správnou verzi (0.1.0-přehled):

   ```powershell
   # Get a list of commands for the imported AzureRm.ResourceGraph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Teď, když se modul Azure PowerShell přidal do vašeho prostředí podle výběru, můžete vyzkoušet jednoduchý dotaz na Resource Graph. Dotaz vrátí prvních pět zdrojů Azure pomocí **Názvem** a **Typem zdroje** každého zdroje.

1. Spusťte první dotaz Azure Resource Graph použitím `Search-AzureRmGraph` cmdlet:

   ```powershell
   # Login first with Connect-AzureRmAccount

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Na tento dotaz příklad neposkytuje modifikátor řazení jako `order by`, opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost.

1. Aktualizuje dotaz pro `order by` **Název** vlastnosti:

   ```powershell
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Tak se nejdřív omezí rozsah výsledků dotazu a ty se pak seřadí.

1. Aktualizujte dotaz na `order by` **název** vlastnosti a potom `limit` na prvních 5 výsledků:

   ```powershell
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Pokud konečný dotaz proběhne více než jednou za předpokladu, že se ve vašem prostředí nic nemění, vrácené výsledky budou konzistentní jak se očekává – seřazené podle **názvu** vlastností, ale stále s omezením na prvních 5 výsledků.

## <a name="cleanup"></a>Vyčištění

Pokud chcete odebrat modul Resource Graph z prostředí Azure PowerShell, můžete tak provést pomocí následujícího příkazu:

```powershell
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'AzureRm.ResourceGraph'
```

> [!NOTE]
> Tím se neodstraní dříve stažený soubor s modulem. Pouze ho odebere ze spuštěného relace PowerShell.

## <a name="next-steps"></a>Další kroky

- Získejte další informace o [dotazovacím jazyce](./concepts/query-language.md)
- Naučte se [prozkoumat zdroje](./concepts/explore-resources.md)
- Spusťte svůj první dotaz prostřednictvím [Azure CLI](first-query-azurecli.md)
- Zobrazit ukázky [Starter dotazy](./samples/starter.md)
- Zobrazit ukázky [Pokročilé dotazy](./samples/advanced.md)
- Váš názor na [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)