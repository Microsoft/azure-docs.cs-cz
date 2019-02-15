---
title: Přehled služby Azure Resource Graph
description: Azure Resource Graph je služba v Azure, která umožňuje komplexní dotazování na prostředky v potřebném měřítku.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 31b9405b7d4141890d786ab3d1cb70d11ef0c1b3
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267950"
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

Je důležité pochopit, že je na základě grafu prostředků Azure dotazovací jazyk [Kusto dotazovací jazyk](../../data-explorer/data-explorer-overview.md) používá Průzkumník dat Azure.

Nejprve se podívejte na podrobnosti o operacích a funkcích, které lze použít s Azure Resource Graph, viz [ jazyk dotazu pro graf zdrojů ](./concepts/query-language.md). Chcete-li procházet zdroje, podívejte se na [ prozkoumat zdroje ](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Oprávnění v Azure Resource Graph

Pokud chcete používat Resource Graph, musíte mít odpovídající oprávnění v [řízení přístupu na základě role](../../role-based-access-control/overview.md), a to alespoň oprávnění ke čtení pro prostředky, které chcete dotazovat. Pokud pro objekt nebo skupinu objektů Azure nemáte alespoň oprávnění `read`, nevrátí se žádné výsledky.

## <a name="throttling"></a>Throttling

Dotazy do grafu prostředků jsou omezené na poskytují nejlepší prostředí a odpovědi na všechny zákazníky. Pokud vaše organizace chce používat rozhraní Graph API prostředku ve velkém měřítku a častých dotazů, použijte prosím portál "Názory" na stránce prostředků grafu. Nezapomeňte zadat váš obchodní případ a zaškrtněte políčko "Microsoft může poslat e-mail o svůj názor" v pořadí pro tým, který se vás kontaktovat.

## <a name="running-your-first-query"></a>Spusťte váš první dotaz

Resource Graph podporuje jak Azure CLI, tak i Azure PowerShell. Struktura dotazu je v obou jazycích stejná. Přečtěte si, jak povolit Resource Graph v [ Azure CLI ](first-query-azurecli.md#add-the-resource-graph-extension) a [ Azure PowerShell ](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Další postup

- Spusťte svůj první dotaz prostřednictvím [Azure CLI](first-query-azurecli.md)
- Spusťte svůj první dotaz prostřednictvím [Azure PowerShell](first-query-powershell.md)
- Začněte se [Starter dotazy](./samples/starter.md)
- Vylepšete své porozumění pomocí [Pokročilých dotazů](./samples/advanced.md)