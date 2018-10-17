---
title: Přehled služby Azure Resource Graph
description: Azure Resource Graph je služba v Azure, která umožňuje komplexní dotazování na prostředky v potřebném měřítku.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d68183f4d0a928ac72f3f73ea5225ad174820cb7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162096"
---
# <a name="what-is-azure-resource-graph"></a>Co je Azure Resource Graph

Azure Resource Graph je služba v Azure, která je navržená k rozšíření správy Azure Resource tím, že poskytuje efektivní a výkonné zkoumání zdrojů s možností dotazu ve velkém měřítku ve všech předplatných a skupin pro správu, abyste mohli efektivně ovládat vaše prostředí. Tyto dotazy poskytují následující možnosti:

- Možnost dotazu ohledně zdrojů s komplexním filtrováním, seskupováním a řazením podle vlastností zdroje.
- Schopnost postupně prozkoumat zdroje založené na požadavcích správy a převést výsledný výraz na definici zásad.
- Schopnost posoudit dopad uplatnění zásad v rozsáhlém cloudovém prostředí.

V této dokumentaci přejdete podrobně přes jednotlivé schopnosti.

> [!NOTE]
> Azure Resource Graph je využíván novou funkcí prohlížení portálu Azure Portal "Všechny zdroje". Je určená k pomoci zákazníkům s potřebou spravovat prostředí ve velkém měřítku.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Jak Resource Graph doplňuje Azure Resource Manager

Azure Resource Manager aktuálně odesílá data do omezené mezipaměti zdrojů, která zveřejňuje několik polí zdrojů, konkrétně – název zdroje, ID, typ, skupinu zdrojů, předplatné a umístění. Pokud byste chtěli dnes pracovat s více vlastnostmi zdrojů, museli byste vyvolat každého jednotlivého poskytovatele zdrojů a požádat o podrobnosti o vlastnostech každého zdroje.

S Azure Resource Graph můžete získat přístup k těmto vlastnostem, které poskytovatelé zdrojů vrátí, aniž by bylo nutné provádět individuální vyvolání u každého poskytovatele zdrojů.

## <a name="the-query-language"></a>Dotazovací jazyk

Teď, když už chápete lépe, co je Azure Resource Graph, pusťme se do vytváření dotazů.

Je důležité pochopit, že dotazovací jazyk Azure Resource Graph je založený na [dotazovacím jazyce Azure Data Explorer](../../data-explorer/data-explorer-overview.md).

Nejprve se podívejte na podrobnosti o operacích a funkcích, které lze použít s Azure Resource Graph, viz [ jazyk dotazu pro graf zdrojů ](./concepts/query-language.md). Chcete-li procházet zdroje, podívejte se na [ prozkoumat zdroje ](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Oprávnění v Azure Resource Graph

Chcete-li používat Resource Graph, musíte být autorizováni prostřednictvím [ řízení přístupu založeného na rolích ](../../role-based-access-control/overview.md) (RBAC) s alespoň přístupem ke čtení zdrojů, které chcete dotazovat. Pokud nemáte oprávnění `read` pro skupinu správy, předplatné, skupinu zdrojů nebo v jednotlivé zdroje, nedojde k návratu ve výsledcích dotazu na Resource Graph.

## <a name="running-your-first-query"></a>Spusťte váš první dotaz

Resource Graph podporuje jak Azure CLI, tak i Azure PowerShell. Komponenta dotazu je strukturována stejným způsobem bez ohledu na to, který jazyk je použitý. Podpora pro Azure Resource Graph není ve výchozím nastavení dostupná ani v SDK, takže musí být načteno rozšíření nebo modul, aby byly poskytnuty potřebné příkazy.
Přečtěte si, jak povolit Resource Graph v [ Azure CLI ](first-query-azurecli.md#add-the-resource-graph-extension) a [ Azure PowerShell ](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Další kroky

- Spusťte svůj první dotaz prostřednictvím [Azure CLI](first-query-azurecli.md)
- Spusťte svůj první dotaz prostřednictvím [Azure PowerShell](first-query-powershell.md)
- Začněte se [Starter dotazy](./samples/starter.md)
- Vylepšete své porozumění pomocí [Pokročilých dotazů](./samples/advanced.md)