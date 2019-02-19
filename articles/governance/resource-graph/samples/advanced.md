---
title: Ukázky rozšířený dotaz
description: Použití Azure Graph prostředků ke spuštění některé pokročilé dotazy, včetně VMSS kapacitu, výpis všechny značky, které používají a odpovídající virtuální počítače pomocí regulárních výrazů.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 8bb1733090ac2a4f3fad837e55943706309c4a5a
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56344008"
---
# <a name="advanced-resource-graph-queries"></a>Pokročilé dotazy na Resource Graph

Prvním krokem k porozumění dotazům s Azure Resource Graph jsou základní znalosti [dotazovacího jazyka](../concepts/query-language.md). Pokud ještě neznáte [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), doporučujeme přečíst si základní informace, abyste pochopili, jak vytvářet požadavky na prostředky, které hledáte.

Projdeme následující rozšířené dotazy:

> [!div class="checklist"]
> - [Získání kapacity a velikosti VMSS](#vmss-capacity)
> - [Vypsat všechny názvy značek](#list-all-tags)
> - [Virtuální počítače odpovídající regulárnímu výrazu](#vm-regex)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Podpora jazyků

Azure PowerShell (prostřednictvím modulu) a Azure CLI (prostřednictvím rozšíření) podporují Azure Resource Graph. Před spuštěním jakéhokoli z následujících dotazů zkontrolujte, že je vaše prostředí připravené. Zobrazte [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) a [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pro pokyny k instalaci a ověření vašeho výběru prostředí.

## <a name="vmss-capacity"></a>Získání kapacity a velikosti škálovací sady virtuálních počítačů

Tento dotaz hledá prostředky škálovací sady virtuálních počítačů a získá různé podrobnosti, včetně velikosti virtuálních počítačů a kapacity škálovací sady. Dotaz pomocí funkce `toint()` přetypuje kapacitu na číslo, aby bylo možné ji řadit. Nakonec se sloupce přejmenují na vlastní pojmenované vlastnosti.

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>Vypsat všechny názvy značek

Tento dotaz spustí se značkou a vytvoří objekt JSON obsahující všechny jedinečné názvy značek a jejich odpovídající typy.

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Virtuální počítače odpovídající regulárnímu výrazu

Tento dotaz vyhledá virtuální počítače, které odpovídají [regulárnímu výrazu](/dotnet/standard/base-types/regular-expression-language-quick-reference) (označovanému jako _regulární výraz_).
**Odpovídá regulárnímu @** umožňuje definovat regulární výraz tak, aby odpovídaly, což je `^Contoso(.*)[0-9]+$`. Tato definice regulárního výrazu je vysvětlena jako:

- `^` – Porovnání musí začít na začátku řetězce.
- `Contoso` – Řetězec s rozlišováním velkých a malých písmen.
- `(.*)` – Shoda dílčího výrazu:
  - `.` – Odpovídá jakémukoli jednomu znaku (s výjimkou nového řádku).
  - `*` – Shoduje se s předchozím prvkem nulakrát nebo vícekrát.
- `[0-9]` – Shoda skupiny znaků pro čísla 0 až 9.
- `+` – Shoduje se s předchozím prvkem jednou nebo vícekrát.
- `$` – Shoda s předchozím prvkem se musí vyskytovat na konci řetězce.

Po porovnání podle názvu, dotaz promítne název a pořadí vzestupně podle názvu.

```Query
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>Další postup

- Zobrazit ukázky [Starter dotazy](starter.md)
- Další informace o [dotazovacím jazyku](../concepts/query-language.md)
- Naučte se [prozkoumat prostředky](../concepts/explore-resources.md)