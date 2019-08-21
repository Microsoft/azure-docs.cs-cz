---
title: Kapacita prostředků pro nasazení – QnA Maker
titleSuffix: Azure Cognitive Services
description: Před vytvořením služby QnA Maker, byste měli rozhodnout, jaké úroveň výše uvedených služeb je pro vás vhodná.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2b2879a1ebcf01a0433873e8da77c4ce55a078cd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647017"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Výběr kapacitu pro vaše nasazení nástroje QnA Maker

Služba QnA Maker je závislá na tři prostředků Azure:
1.  App Service (pro modul runtime)
2.  Azure Search (pro ukládání a hledání QnAs)
3.  App Insights (volitelné, pro ukládání protokolů chatu a telemetrie)

Před vytvořením služby QnA Maker, byste měli rozhodnout, jaké úroveň výše uvedených služeb je pro vás vhodná. 

Obvykle jsou tři parametry, které je potřeba zvážit:

1. **Propustnost, kterou potřebujete ze služby**: Podle svých potřeb vyberte vhodný [plán aplikace](https://azure.microsoft.com/pricing/details/app-service/plans/) pro vaši službu App Service. Je možné [vertikálně navýšit kapacitu](https://docs.microsoft.com/azure/app-service/manage-scale-up) nebo snížit kapacitu aplikace. To by mělo také ovlivnit výběr Azure Search SKU, další podrobnosti najdete v tématu [tady](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Velikost a počet znalostí znalostní báze**: Vyberte příslušnou SKLADOVOU položku služby [Azure Search](https://azure.microsoft.com/pricing/details/search/) pro váš scénář. Je možné publikovat N-1 znalostních bází na určitou úroveň, kde N je maximální počet indexů na úrovni povoleno. Zkontrolujte také maximální velikosti a počtu dokumentů povolené a úroveň.

    Pokud má vaše úroveň například 15 povolených indexů, můžete publikovat 14 báze znalostí (1 index na publikovanou znalostní bázi). Patnáctý index se používá pro všechny znalostní báze pro vytváření a testování. 

1. **Počet dokumentů jako zdrojů**: Bezplatná SKU služby QnA Maker Management omezuje počet dokumentů, které můžete spravovat přes portál, a rozhraní API na 3 (o velikosti 1 MB). Standardní skladová položka nemá žádná omezení pro počet dokumentů, které můžete spravovat. Další podrobnosti najdete [tady](https://aka.ms/qnamaker-pricing).

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
