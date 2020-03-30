---
title: Data poradce v Azure Resource Graph
description: Dotazování na data poradce v Azure Resource Graph
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502448"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Dotaz na data poradce v Průzkumníku grafů prostředků (Azure Resource Graph)

Prostředky poradce jsou teď na palubě [Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/). To je základem pro mnoho scénářů zákazníků ve velkém měřítku pro doporučení poradce. Několik scénářů, které nebyly možné před provést ve velkém měřítku a nyní lze dosáhnout pomocí resource graph jsou:
* Umožňuje provádět komplexní dotazy pro všechna vaše předplatná na webu Azure Portal.
* Doporučení shrnutá podle typů kategorií (například vysoká dostupnost, výkon) a typů dopadů (vysoká, střední, nízká)
* Všechna doporučení pro konkrétní typ doporučení
* Ovlivněný počet zdrojů podle kategorie doporučení

![Poradce v průzkumníku grafů prostředků Azure](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Typy prostředků poradce v Azure Graphu

Dostupné typy prostředků poradce v [grafu prostředků](https://docs.microsoft.com/azure/governance/resource-graph/): Pro dotazování v části Prostředky poradce jsou k dispozici 3 typy prostředků. Zde je seznam prostředků, které jsou nyní k dispozici pro dotazování v grafu prostředků.
* Microsoft.Advisor/konfigurace
* Microsoft.Advisor/doporučení
* Microsoft.Advisor/potlačení

Tyto typy prostředků jsou uvedeny v nové tabulce s názvem AdvisorResources, na kterou se můžete taky dotazovat v Průzkumníku grafů prostředků na webu Azure Portal.


## <a name="next-steps"></a>Další kroky

Další informace o doporučeních poradců naleznete v tématu:
* [Úvod do Azure Advisoru](advisor-overview.md)
* [Začínáme s poradcem](advisor-get-started.md)
* [Doporučení nákladů poradce](advisor-cost-recommendations.md)
* [Doporučení pro výkon poradce](advisor-performance-recommendations.md)
* [Doporučení pro zabezpečení poradce](advisor-security-recommendations.md)
* [Doporučení poradce pro provozní excelenci](advisor-operational-excellence-recommendations.md)
* [Rozhraní REST PORADCE](https://docs.microsoft.com/rest/api/advisor/)
