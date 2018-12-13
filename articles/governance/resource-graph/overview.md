---
title: Přehled služby Azure Resource Graph
description: Azure Resource Graph je služba v Azure, která umožňuje komplexní dotazování na prostředky v potřebném měřítku.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: ed64f6317fefb9e82dbe14e806499965d926d434
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316195"
---
# <a name="what-is-azure-resource-graph"></a>Co je Azure Resource Graph

Azure Resource Graph je služba v Azure, která je navržená k rozšíření správy Azure Resource tím, že poskytuje efektivní a výkonné zkoumání zdrojů s možností dotazu ve velkém měřítku ve všech předplatných a skupin pro správu, abyste mohli efektivně ovládat vaše prostředí. Tyto dotazy poskytují následující funkce:

- Možnost dotazu ohledně zdrojů s komplexním filtrováním, seskupováním a řazením podle vlastností zdroje.
- Schopnost postupně prozkoumat zdroje založené na požadavcích správy a převést výsledný výraz na definici zásad.
- Schopnost posoudit dopad uplatnění zásad v rozsáhlém cloudovém prostředí.

V této dokumentaci si podrobně projdete jednotlivé funkce.

> [!NOTE]
> Azure Resource Graph je využíván novou funkcí prohlížení portálu Azure Portal "Všechny zdroje". Je určená k pomoci zákazníkům s potřebou spravovat prostředí ve velkém měřítku.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Jak Resource Graph doplňuje Azure Resource Manager

Azure Resource Manager aktuálně odesílá data do omezené mezipaměti prostředků, která zpřístupňuje několik polí prostředků, konkrétně Název prostředku, ID, Typ, Skupina prostředků, Předplatná a Umístění. Při práci s vlastnostmi různých prostředků se dříve vyžadovalo volání poskytovatelů jednotlivých prostředků a odeslání požadavku na podrobnosti o vlastnostech jednotlivých prostředků.

S Azure Resource Graph můžete získat přístup k těmto vlastnostem, které poskytovatelé zdrojů vrátí, aniž by bylo nutné provádět individuální vyvolání u každého poskytovatele zdrojů.

## <a name="the-query-language"></a>Dotazovací jazyk

Teď, když už chápete lépe, co je Azure Resource Graph, pusťme se do vytváření dotazů.

Je důležité pochopit, že dotazovací jazyk Azure Resource Graph je založený na [dotazovacím jazyce Azure Data Explorer](../../data-explorer/data-explorer-overview.md).

Nejprve se podívejte na podrobnosti o operacích a funkcích, které lze použít s Azure Resource Graph, viz [ jazyk dotazu pro graf zdrojů ](./concepts/query-language.md). Chcete-li procházet zdroje, podívejte se na [ prozkoumat zdroje ](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Oprávnění v Azure Resource Graph

Pokud chcete používat Resource Graph, musíte mít odpovídající oprávnění v [řízení přístupu na základě role](../../role-based-access-control/overview.md), a to alespoň oprávnění ke čtení pro prostředky, které chcete dotazovat. Pokud pro objekt nebo skupinu objektů Azure nemáte alespoň oprávnění `read`, nevrátí se žádné výsledky.

## <a name="running-your-first-query"></a>Spusťte váš první dotaz

Resource Graph podporuje jak Azure CLI, tak i Azure PowerShell. Struktura dotazu je v obou jazycích stejná. Přečtěte si, jak povolit Resource Graph v [ Azure CLI ](first-query-azurecli.md#add-the-resource-graph-extension) a [ Azure PowerShell ](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Další postup

- Spusťte svůj první dotaz prostřednictvím [Azure CLI](first-query-azurecli.md)
- Spusťte svůj první dotaz prostřednictvím [Azure PowerShell](first-query-powershell.md)
- Začněte se [Starter dotazy](./samples/starter.md)
- Vylepšete své porozumění pomocí [Pokročilých dotazů](./samples/advanced.md)