---
title: Data služby Advisor v grafu prostředků Azure
description: Vytváření dotazů na data služby Advisor v Azure Resource graphu
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502448"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Dotaz na data služby Advisor v Průzkumníku grafů prostředků (Azure Resource Graph)

Prostředky služby Advisor se teď přiřadí do [grafu prostředků Azure](https://azure.microsoft.com/features/resource-graph/). Základem pro celou řadu zákaznických scénářů pro doporučení poradce je řada. Několik scénářů, které nebyly před vlastním škálováním možné a teď je možné dosáhnout pomocí grafu prostředků:
* Poskytuje možnost provádět komplexní dotaz pro všechna Vaše předplatná v Azure Portal
* Doporučení shrnutá podle typů kategorií (jako vysoká dostupnost, výkon) a typy dopadu (vysoká, střední, nízká)
* Všechna doporučení pro konkrétní typ doporučení
* Kategorie ovlivněného počtu prostředků podle doporučení

![Poradce v Azure Resource Graph Exploreru](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Typy prostředků služby Advisor v Azure graphu

Dostupné typy prostředků služby Advisor v [grafu prostředků](https://docs.microsoft.com/azure/governance/resource-graph/): existují 3 typy prostředků, které jsou k dispozici pro dotazování v rámci prostředků služby Advisor. Zde je seznam prostředků, které jsou nyní k dispozici pro dotazování v grafu prostředků.
* Microsoft. Advisor/Configurations
* Microsoft. Advisor/doporučení
* Microsoft. Advisor/potlačení

Tyto typy prostředků jsou uvedeny pod novou tabulkou s názvem jako AdvisorResources, kterou můžete také dotazovat v Průzkumníku grafu prostředků v Azure Portal.


## <a name="next-steps"></a>Další kroky

Další informace o doporučeních služby Advisor najdete v těchto tématech:
* [Úvod do Azure Advisor](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Doporučení pro náklady na poradce](advisor-cost-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
* [REST API Advisoru](https://docs.microsoft.com/rest/api/advisor/)
