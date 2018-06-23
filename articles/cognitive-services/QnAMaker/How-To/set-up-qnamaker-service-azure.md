---
title: Postup nastavení služby QnA Maker - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Jak nastavit službu QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce452dd686529e017b4eae4717eadb044b389409
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343260"
---
# <a name="create-a-qna-maker-service"></a>Vytvoření služby QnA Maker

Před vytvořením žádné program QnA Maker znalostních bází, musíte nejprve nastavit až QnA Maker služby v Azure. Každý, kdo má oprávnění pro vytvoření nových prostředků v předplatném můžete nastavit QnA Maker služby.

Tento instalační program nasadí několik prostředků Azure. Tyto prostředky společně správy obsahu znalostní báze knowledge base a nabízí odpovědi na otázky možnosti, když se koncový bod.

1. Přihlaste se k portálu [Azure Portal](<https://portal.azure.com>).

2.  Klikněte na **přidat nový prostředek**, zadejte "qna maker" vyhledávání a vyberte prostředek QnA Maker

    ![Vytvoření nové služby QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource.png)

3.  Klikněte na **vytvořit** po přečtení podmínky a ujednání.

    ![Vytvoření nové služby QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

4. V **QnA Maker**, vyberte odpovídající úrovně a oblastech.

    ![Vytvoření nové služby QnA Maker](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Vyplnění **název** s jedinečný název pro identifikaci této služby QnA Maker. Tento název taky identifikuje koncový bod QnA Maker, pro které bude přidružené znalostních bází.
    * Vyberte **předplatné** ve které se nasadí QnA Maker prostředků.
    * Vyberte **správu cenová úroveň** QnA Maker management Services (portál a rozhraní API pro správu). V tématu [sem](https://aka.ms/qnamaker-pricing) podrobnosti o cenách skladové jednotky.
    * Vytvořte novou **skupiny prostředků** (doporučeno) nebo použijte existující ve které chcete nasadit tento prostředek QnA Maker.
    * Vyberte **vyhledávání cenová úroveň** služby Azure Search. Pokud se zobrazí možnost úroveň Free šedá, znamená to, že už máte bezplatnou Azure Search vrstvy nasazené v rámci vašeho předplatného. V takovém případě musíte spustit s vrstvou základní Azure Search. Zobrazit podrobnosti o cenách Azure search [zde](https://azure.microsoft.com/en-us/pricing/details/search/).
    * Vyberte **vyhledávání umístění** místo Azure Search dat k nasazení. Omezení v uložení dat zákazníka bude informovat umístění, které zvolíte pro službu Azure Search.
    * Zadejte název vaší služby App service v **název aplikace**.
    * Ve výchozím nastavení výchozí služby App service na plán úrovně standard (S1). Plán můžete změnit po vytvoření. Zobrazit další podrobnosti o App service – ceny [zde](https://azure.microsoft.com/en-in/pricing/details/app-service/).
    * Vyberte **web umístění** kde bude nasazen App Service.

        > [!NOTE]
        > Vyhledávání umístění se může lišit od umístění webu.

    * Zvolte, zda chcete povolit **Application Insights** nebo ne. Pokud **Application Insights** je povoleno, QnA Maker shromažďuje telemetrická data na provoz, chatu a chyby.
    * Vyberte **umístění Statistika aplikace** kde bude nasazen prostředek Application Insights.

5. Po ověření jsou všechna pole, můžete kliknutím na **vytvořit** ke spuštění nasazení z těchto služeb v rámci vašeho předplatného. To bude trvat několik minut.

6.  Po nasazení se provádí, zobrazí se následující prostředky vytvořené v rámci vašeho předplatného.

    ![Vytvoření nové služby QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření a publikování znalostní bázi](../Quickstarts/create-publish-knowledge-base.md)