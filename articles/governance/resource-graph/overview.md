---
title: Přehled služby Azure Resource Graph
description: Zjistěte, jak služba Graph prostředků Azure umožňuje složitých dotazů na prostředky v potřebném měřítku.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 28efdabc024fd32c83ba966b15284ec6ff368d4d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788990"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Přehled služby Azure Graph prostředků

Azure Resource Graph je služba v Azure, která je navržená k rozšíření správy Azure Resource tím, že poskytuje efektivní a výkonné zkoumání zdrojů s možností dotazu ve velkém měřítku ve všech předplatných a skupin pro správu, abyste mohli efektivně ovládat vaše prostředí. Tyto dotazy poskytují následující funkce:

- Možnost dotazu ohledně zdrojů s komplexním filtrováním, seskupováním a řazením podle vlastností zdroje.
- Schopnost postupně prozkoumat zdroje založené na požadavcích správy a převést výsledný výraz na definici zásad.
- Schopnost posoudit dopad uplatnění zásad v rozsáhlém cloudovém prostředí.

V této dokumentaci si podrobně projdete jednotlivé funkce.

> [!NOTE]
> Azure Resource Graph je využíván novou funkcí prohlížení portálu Azure Portal "Všechny zdroje". Je navržen tak, abychom zákazníkům s nutnost spravovat prostředí ve velkém měřítku.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Jak Resource Graph doplňuje Azure Resource Manager

Azure Resource Manager aktuálně odesílá data do omezené mezipaměti prostředků, která zpřístupňuje několik polí prostředků, konkrétně Název prostředku, ID, Typ, Skupina prostředků, Předplatná a Umístění. Při práci s vlastnostmi různých prostředků se dříve vyžadovalo volání poskytovatelů jednotlivých prostředků a odeslání požadavku na podrobnosti o vlastnostech jednotlivých prostředků.

S Azure Resource Graph můžete získat přístup k těmto vlastnostem, které poskytovatelé zdrojů vrátí, aniž by bylo nutné provádět individuální vyvolání u každého poskytovatele zdrojů. Seznam podporovaných typů prostředků, Hledat **Ano** v [prostředky pro nasazení úplný režim](../../azure-resource-manager/complete-mode-deletion.md) tabulky.

## <a name="the-query-language"></a>Dotazovací jazyk

Teď, když už chápete lépe, co je Azure Resource Graph, pusťme se do vytváření dotazů.

Je důležité pochopit, že je na základě grafu prostředků Azure dotazovací jazyk [Kusto dotazovací jazyk](../../data-explorer/data-explorer-overview.md) používá Průzkumník dat Azure.

Nejprve se podívejte na podrobnosti o operacích a funkcích, které lze použít s Azure Resource Graph, viz [ jazyk dotazu pro graf zdrojů ](./concepts/query-language.md). Chcete-li procházet zdroje, podívejte se na [ prozkoumat zdroje ](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Oprávnění v Azure Resource Graph

Pokud chcete používat Resource Graph, musíte mít odpovídající oprávnění v [řízení přístupu na základě role](../../role-based-access-control/overview.md), a to alespoň oprávnění ke čtení pro prostředky, které chcete dotazovat. Pokud pro objekt nebo skupinu objektů Azure nemáte alespoň oprávnění `read`, nevrátí se žádné výsledky.

> [!NOTE]
> Prostředek grafu používá dostupná předplatná pro objekt zabezpečení při přihlášení. Zobrazte prostředky k novému předplatnému přidali během aktivní relace, musíte aktualizovat objekt zabezpečení kontextu. Tato akce se stane automaticky po odhlášení a znovu přihlaste.

## <a name="throttling"></a>Throttling

Dotazy do grafu prostředků jsou omezené na poskytují nejlepší prostředí a odpovědi na všechny zákazníky. Pokud vaše organizace chce používat rozhraní Graph API prostředku ve velkém měřítku a častých dotazů, použijte prosím portál "Názory" na stránce prostředků grafu. Nezapomeňte zadat váš obchodní případ a zaškrtněte políčko "Microsoft může poslat e-mail o svůj názor" v pořadí pro tým, který se vás kontaktovat.

## <a name="running-your-first-query"></a>Spusťte váš první dotaz

Diagram zdrojů podporuje rozhraní příkazového řádku Azure, Azure Powershellu a Azure SDK pro .NET. Strukturované dotaz stejný pro jednotlivé jazyky. Přečtěte si, jak povolit Resource Graph v [ Azure CLI ](first-query-azurecli.md#add-the-resource-graph-extension) a [ Azure PowerShell ](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Další postup

- Spusťte svůj první dotaz prostřednictvím [Azure CLI](first-query-azurecli.md)
- Spusťte svůj první dotaz prostřednictvím [Azure PowerShell](first-query-powershell.md)
- Začněte se [Starter dotazy](./samples/starter.md)
- Vylepšete své porozumění pomocí [Pokročilých dotazů](./samples/advanced.md)