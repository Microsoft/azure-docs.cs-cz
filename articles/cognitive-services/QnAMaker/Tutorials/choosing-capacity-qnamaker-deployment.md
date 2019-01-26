---
title: Kapacita prostředků pro nasazení – QnA Maker
titleSuffix: Azure Cognitive Services
description: Před vytvořením služby QnA Maker, byste měli rozhodnout, jaké úroveň výše uvedených služeb je pro vás vhodná.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 7e9093582776db82cfe4bdd36094f94634bd3c23
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911298"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Výběr kapacitu pro vaše nasazení nástroje QnA Maker

Služba QnA Maker je závislá na tři prostředků Azure:
1.  App Service (pro modul runtime)
2.  Azure Search (pro ukládání maximálně)
3.  App Insights (volitelné, pro ukládání protokolů chatu a telemetrie)

Před vytvořením služby QnA Maker, byste měli rozhodnout, jaké úroveň výše uvedených služeb je pro vás vhodná. 

Obvykle jsou tři parametry, které je potřeba zvážit:

1. **Propustnost je nutné ze služby**: Vyberte příslušné [plán služby App](https://azure.microsoft.com/pricing/details/app-service/plans/) pro službu App service podle svých potřeb. Je možné [vertikálně navýšit kapacitu](https://docs.microsoft.com/azure/app-service/web-sites-scale) nebo snížit kapacitu aplikace. To by mělo také ovlivnit výběr Azure Search SKU, další podrobnosti najdete v tématu [tady](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Velikosti a počtu znalostních bází**: Zvolte odpovídající [Azure vyhledávání SKU](https://azure.microsoft.com/pricing/details/search/) pro váš scénář. Je možné publikovat N-1 znalostních bází na určitou úroveň, kde N je maximální počet indexů na úrovni povoleno. Zkontrolujte také maximální velikosti a počtu dokumentů povolené a úroveň.

    Pokud svou úroveň má 15 povolené indexy, můžete publikovat 14 znalostních bází (1 index na publikované znalostní báze). Patnáctý index se používá pro všechny znalostních bází, vytváření a testování. 

1. **Počet dokumentů, jako zdroje**: SKU zdarma službu QnA Maker management omezuje počet dokumentů, které můžete spravovat prostřednictvím portálu a rozhraní API pro 3 (1 MB. velikost každého). Standardní skladová položka nemá žádná omezení pro počet dokumentů, které můžete spravovat. Další podrobnosti najdete [tady](https://aka.ms/qnamaker-pricing).

V následující tabulce jsou uvedené některé podrobné pokyny.

|                        | Správa nástroje QnA Maker | App Service | Azure Search | Omezení                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentování        | SKU zdarma             | Úroveň Free   | Úroveň Free    | Publikování až 2 znalostní báze, velikost 50 MB  |
| Prostředí pro vývoj/testování   | Standardní SKU         | Shared      | Basic        | Publikování až 14 znalostní báze, velikosti 2 GB    |
| Produkční prostředí | Standardní SKU         | Basic       | Standard     | Publikovat do 49 znalostní báze, velikost 25 GB |

Upgrade nástroje QnA Maker technologie, naleznete v tématu [upgradovat vaše služba QnA Maker](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Upgrade služby QnA Maker](../How-To/upgrade-qnamaker-service.md)
