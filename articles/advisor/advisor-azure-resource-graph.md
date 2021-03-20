---
title: Data Advisoru v Azure Resource Graphu
description: Vytváření dotazů na data služby Advisor v Azure Resource graphu
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: 66bb500d419d1f5537afafd7a2df543ded8cc7ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87057783"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Dotaz na data služby Advisor v Průzkumníku grafů prostředků (Azure Resource Graph)

Prostředky služby Advisor se teď přiřadí do [grafu prostředků Azure](https://azure.microsoft.com/features/resource-graph/). Základem pro celou řadu zákaznických scénářů pro doporučení poradce je řada. Několik scénářů, které nebyly před vlastním škálováním možné a teď je možné dosáhnout pomocí grafu prostředků:
* Poskytuje možnost provádět komplexní dotaz pro všechna Vaše předplatná v Azure Portal
* Doporučení shrnutá podle typů kategorií (jako je spolehlivost, výkon) a typy dopadu (vysoká, střední, nízká)
* Všechna doporučení pro konkrétní typ doporučení
* Kategorie ovlivněného počtu prostředků podle doporučení

![Poradce v Azure Resource Graph Exploreru](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Typy prostředků služby Advisor v Azure graphu

Dostupné typy prostředků služby Advisor v [grafu prostředků](../governance/resource-graph/index.yml): existují 3 typy prostředků, které jsou k dispozici pro dotazování v rámci prostředků služby Advisor. Zde je seznam prostředků, které jsou nyní k dispozici pro dotazování v grafu prostředků.
* Microsoft. Advisor/Configurations
* Microsoft. Advisor/doporučení
* Microsoft. Advisor/potlačení

Tyto typy prostředků jsou uvedeny pod novou tabulkou s názvem jako AdvisorResources, kterou můžete také dotazovat v Průzkumníku grafu prostředků v Azure Portal.


## <a name="next-steps"></a>Další kroky

Další informace o doporučeních služby Advisor najdete v těchto tématech:
* [Seznámení s Azure Advisorem](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Doporučení Advisoru týkající se cen](advisor-cost-recommendations.md)
* [Doporučení pro spolehlivost Advisoru](advisor-high-availability-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
* [REST API Advisoru](/rest/api/advisor/)
