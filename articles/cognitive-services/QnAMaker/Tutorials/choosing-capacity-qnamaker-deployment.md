---
title: Kapacita prostředků pro nasazení – QnA Maker
titleSuffix: Azure Cognitive Services
description: Než začnete vytvářet službu QnA Maker, měli byste se rozhodnout, která úroveň výše uvedených služeb je pro vás vhodná.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5cbdb6fcf9fcdf12b54ff1db4b577bb975517131
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73793944"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Volba kapacity pro nasazení QnA Maker

Služba QnA Maker využívá závislost na třech prostředcích Azure:
1.  App Service (pro modul runtime)
2.  Azure Kognitivní hledání (pro ukládání a hledání QnAs)
3.  App Insights (volitelné, pro ukládání protokolů a telemetrie chatu)

Než začnete vytvářet službu QnA Maker, měli byste se rozhodnout, která úroveň výše uvedených služeb je pro vás vhodná. 

Obvykle existují tři parametry, které je třeba vzít v úvahu:

1. **Propustnost, kterou potřebujete ze služby**: vyberte příslušný [plán aplikace](https://azure.microsoft.com/pricing/details/app-service/plans/) pro službu App Service podle svých potřeb. Aplikaci můžete [škálovat směrem nahoru](https://docs.microsoft.com/azure/app-service/manage-scale-up) nebo dolů. Mělo by to mít vliv i na výběr skladové položky v Azure Kognitivní hledání. Další podrobnosti najdete [tady](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Velikost a počet znalostí znalostní báze**: vyberte příslušnou skladovou položku [Azure Search](https://azure.microsoft.com/pricing/details/search/) pro váš scénář. Můžete publikovat N-1 znalostní báze v určité úrovni, kde N je maximální počet indexů povolených v této vrstvě. Také ověřte maximální velikost a počet dokumentů povolených na jednu úroveň.

    Pokud má vaše úroveň například 15 povolených indexů, můžete publikovat 14 báze znalostí (1 index na publikovanou znalostní bázi). Patnáctý index se používá pro všechny znalostní báze pro vytváření a testování. 

1. **Počet dokumentů jako zdrojů**: bezplatná SKU služby QnA maker Management omezuje počet dokumentů, které můžete spravovat přes portál, a rozhraní API na 3 (velikost 1 MB). Standardní SKU neomezuje počet dokumentů, které můžete spravovat. Další podrobnosti najdete [tady](https://aka.ms/qnamaker-pricing).

V následující tabulce jsou uvedeny některé pokyny vysoké úrovně.

|                        | Správa QnA Maker | App Service | Kognitivní hledání Azure | Omezení                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentování        | SKU zdarma             | Úroveň Free   | Úroveň Free    | Publikovat až 2 aktualizací KB velikost, 50 MB  |
| Vývojové a testovací prostředí   | Standardní SKU         | Sdílená      | Basic        | Publikování až o 14 aktualizací KB velikosti 2 GB    |
| Produkční prostředí | Standardní SKU         | Basic       | Standard     | Publikování až 49 aktualizací KB, velikost 25 GB |

Pokud chcete upgradovat QnA Maker stacku, přečtěte si téma [upgrade služby QnA maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Upgrade služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
