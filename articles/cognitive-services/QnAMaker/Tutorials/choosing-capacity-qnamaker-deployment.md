---
title: Výběr kapacitu pro vaše nasazení nástroje QnA Maker – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Tento průvodce výběrem kapacitu pro vaše nasazení nástroje QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 71af374fbd08fe1f7568bc1ece2a65af2de3ad19
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573362"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Výběr kapacitu pro vaše nasazení nástroje QnA Maker

Služba QnA Maker je závislá na tři prostředků Azure:
1.  App Service (pro modul runtime)
2.  Azure Search (pro ukládání maximálně)
3.  App Insights (volitelné, pro ukládání chatlogs a telemetrie)

Před vytvořením služby QnA Maker, byste měli rozhodnout, jaké úrovně výše uvedené služby je pro vás vhodná. 

Obvykle jsou tři parametry, které je potřeba zvážit:
1. **Propustnost je nutné ze služby**: vyberte příslušné [plán služby App](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/) pro službu App service podle svých potřeb. Je možné [vertikálně navýšit kapacitu](https://docs.microsoft.com/azure/app-service/web-sites-scale) nebo snížit kapacitu aplikace. To by mělo také ovlivnit výběr Azure Search SKU, další podrobnosti najdete v tématu [tady](https://docs.microsoft.com/azure/search/search-sku-tier).

2. **Velikosti a počtu znalostních bází**: Zvolte odpovídající [Azure vyhledávání SKU](https://azure.microsoft.com/en-in/pricing/details/search/) pro váš scénář. Je možné publikovat N-1 znalostních bází na určitou úroveň, kde N je maximální počet indexů na úrovni povoleno. Zkontrolujte také maximální velikosti a počtu dokumentů povolené a úroveň.

3. **Počet dokumentů, jako zdroje**: SKU zdarma službu QnA Maker management omezuje počet dokumentů, můžete spravovat prostřednictvím portálu a rozhraní API pro 3 (1 MB. velikost každého). Standardní skladová položka nemá žádná omezení pro počet dokumentů, které můžete spravovat. Další podrobnosti najdete [tady](https://aka.ms/qnamaker-pricing).

V následující tabulce jsou uvedené některé podrobné pokyny.

|                        | Správa nástroje QnA Maker | App Service | Azure Search | Omezení                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentování        | SKU zdarma             | Úroveň Free   | Úroveň Free    | Publikování až 2 znalostní báze, velikost 50 MB  |
| Prostředí pro vývoj/testování   | Standardní SKU         | Shared      | Basic        | Publikování až 4 kB, velikosti 2GB    |
| Produkční prostředí | Standardní SKU         | Basic       | Standard     | Publikovat do 49 znalostní báze, velikost 25 GB |

Upgrade nástroje QnA Maker technologie, naleznete v tématu [upgradovat vaše služba QnA Maker](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Upgrade služby QnA Maker](../How-To/upgrade-qnamaker-service.md)
