---
title: Instalační služba QnA Maker – QnA Maker
titleSuffix: Azure Cognitive Services
description: Než budete moct vytvořit žádné nástroje QnA Maker znalostních bází, nejprve musíte vytvořit služba QnA Maker v Azure. Každý, kdo má oprávnění k vytvoření nových prostředků v předplatném můžete nastavit služba QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: f30b55eda4a02cfb3e961c0019128e4fe686cf53
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967675"
---
# <a name="create-a-qna-maker-service"></a>Vytvoření služby QnA Maker

Než budete moct vytvořit žádné nástroje QnA Maker znalostních bází, nejprve musíte vytvořit služba QnA Maker v Azure. Každý, kdo má oprávnění k vytvoření nových prostředků v předplatném můžete nastavit služba QnA Maker.

## <a name="create-a-new-service"></a>Vytvoření nové služby

Tento postup nasadí několik prostředků Azure. Společně tyto prostředky spravovat obsah znalostní báze knowledge base a poskytují možnosti odpovídání na otázku, i když koncový bod.

1. Přihlaste se k Azure Portal a [vytvořte prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Po přečtení podmínek a ujednání vyberte **vytvořit** .

    ![Vytvořit novou službu QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. V **QnA Maker**, vyberte příslušné úrovně a oblasti.

    ![Vytvořit novou službu QnA Maker – cenová úroveň a oblasti](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Zadejte **název** s jedinečný název pro identifikaci této služby QnA Maker. Tento název také identifikuje koncový bod QnA Maker, do kterého mají být asociována znalostních bází.
    * Zvolte **předplatné** ve kterém bude nasazen nástroj QnA Maker prostředků.
    * Vyberte **cenovou úroveň** pro službu QnA maker Management Services (rozhraní API pro portál a správu). Zobrazit [tady](https://aka.ms/qnamaker-pricing) podrobnosti o cenách za skladové jednotky.
    * Vytvořte nový **skupiny prostředků** (doporučeno) nebo použijte již existující, ve které chcete nasadit tento prostředek QnA Maker. QnA Maker vytvoří několik prostředků Azure; Když vytvoříte skupinu prostředků pro tyto prostředky, můžete tyto prostředky snadno najít, spravovat a odstranit podle názvu skupiny prostředků.
    * Vyberte **umístění skupiny prostředků**.
    * Zvolte **cenová úroveň hledání** služby Azure Search. Pokud se zobrazí možnost úrovně Free šedě, znamená to, že už máte bezplatnou Azure Search úrovni nasazené ve vašem předplatném. V takovém případě je potřeba spustit s úrovní Basic Azure Search. Zobrazit podrobnosti o cenách Azure search [tady](https://azure.microsoft.com/pricing/details/search/).
    * Zvolte **poloha při hledání** kde chcete data Azure Search k nasazení. Omezení kde musí být zákaznická data uložena informuje umístění, které jste vybrali pro Azure Search.
    * Zadejte název služby App service v **název aplikace**.
    * Ve výchozím nastavení výchozí služby App service na úrovni standard (S1). Tento plán můžete změnit po vytvoření. Viz podrobnosti o App service – ceny [tady](https://azure.microsoft.com/pricing/details/app-service/).
    * Zvolte **umístění webu** nasazená služby App Service.

        > [!NOTE]
        > Umístění pro hledání se může lišit od umístění webu.

    * Vyberte, jestli chcete povolit **Application Insights** nebo ne. Pokud **Application Insights** je povolené, nástroj QnA Maker shromažďuje telemetrická data na provoz, chatu a chyby.
    * Zvolte **umístění App insights** nasazená prostředku Application Insights.
    * V případě míry úspory nákladů můžete [sdílet](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) některé, ale ne všechny prostředky Azure vytvořené pro QnA maker. 

1. Po ověření všech polí můžete vybrat **vytvořit** a zahájit nasazení těchto služeb ve vašem předplatném. To bude trvat několik minut.

1. Jakmile se nasazení dokončí, zobrazí se následující prostředky vytvořené v rámci vašeho předplatného.

    ![Prostředek vytvoří nová služba QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Oblast služby správy

Služba správy QnA Maker, která se používá pouze pro & portálu pro počáteční zpracování dat, je k dispozici pouze v Západní USA. V této Západní USA službě se neukládají žádná zákaznická data.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření a publikování znalostní báze](../Quickstarts/create-publish-knowledge-base.md)
