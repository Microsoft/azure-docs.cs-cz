---
title: Výběr kapacitu pro vaše nasazení QnA Maker - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Průvodce výběr kapacitu pro vaše nasazení QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b0219b9f7dbbee52406dab9d808134fa2e2a689d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343258"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Výběr kapacitu pro vaše nasazení QnA Maker

Službu QnA Maker, má závislost na tři prostředků Azure:
1.  App Service (pro modul runtime)
2.  Službě Azure Search (pro ukládání QnAs)
3.  Přehled aplikace (volitelné, pro ukládání chatlogs a telemetrie)

Před vytvořením služby QnA Maker, byste měli rozhodnout, které úrovně služby výše je vhodný pro vás. 

Obvykle existují tři parametry, které je třeba vzít v úvahu:
1. **Propustnost, je nutné ze služby**: Vyberte odpovídající [plán služby App](https://azure.microsoft.com/en-in/pricing/details/app-service/plans/) pro aplikační služby na základě potřeb. Můžete [škálovat](https://docs.microsoft.com/en-us/azure/app-service/web-sites-scale) nebo dolů o aplikaci. To by mělo také ovlivnit výběr Azure Search SKU najdete víc podrobností [zde](https://docs.microsoft.com/en-us/azure/search/search-sku-tier).

2. **Velikost a počet znalostních bází**: Zvolte odpovídající [Azure vyhledávání SKU](https://azure.microsoft.com/en-in/pricing/details/search/) pro váš scénář. N-1 znalostních bází můžete publikovat v konkrétní úrovni, kde N je maximální povolená ve vrstvě indexy. Zkontrolujte také maximální velikost a počet dokumentů, které jsou povolené na vrstvě.

3. **Počet dokumentů jako zdroje**: volné SKU služby management QnA Maker omezuje počet dokumentů, můžete spravovat prostřednictvím portálu a rozhraní API pro 3 (velikosti 1 MB každý). Standardní SKU nemá žádné omezení počtu dokumenty, které můžete spravovat. Další podrobnosti najdete v části [zde](https://aka.ms/qnamaker-pricing).

V následující tabulce jsou uvedeny základní pokyny.

|                        | Správa QnA Maker | App Service | Azure Search | Omezení                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentování        | Volné SKU             | Úroveň Free   | Úroveň Free    | Publikování až 2 články znalostní báze, velikost 50 MB  |
| Prostředí pro vývoj/testování   | Standardní SKU         | Shared      | Basic        | Publikování až 4 články znalostní báze, velikost 2GB    |
| Produkční prostředí | Standardní SKU         | Basic       | Standard     | Publikování až 49 články znalostní báze, velikost 25 GB |

Upgrade zásobník QnA Maker, najdete v části [upgradujte QnA Maker službu](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Upgrade služby QnA Maker](../How-To/upgrade-qnamaker-service.md)
