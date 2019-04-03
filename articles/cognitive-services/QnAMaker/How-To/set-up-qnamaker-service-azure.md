---
title: Instalační služba QnA Maker – QnA Maker
titleSuffix: Azure Cognitive Services
description: Než budete moct vytvořit žádné nástroje QnA Maker znalostních bází, nejprve musíte vytvořit služba QnA Maker v Azure. Každý, kdo má oprávnění k vytvoření nových prostředků v předplatném můžete nastavit služba QnA Maker.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/02/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 90e7abf229e0ee1ca396150fee0c1f0970d257aa
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876397"
---
# <a name="create-a-qna-maker-service"></a>Vytvoření služby QnA Maker

Než budete moct vytvořit žádné nástroje QnA Maker znalostních bází, nejprve musíte vytvořit služba QnA Maker v Azure. Každý, kdo má oprávnění k vytvoření nových prostředků v předplatném můžete nastavit služba QnA Maker.

## <a name="create-a-new-service"></a>Vytvoření nové služby

Tento postup nasadí několik prostředků Azure. Společně tyto prostředky spravovat obsah znalostní báze knowledge base a poskytují možnosti odpovídání na otázku, i když koncový bod.

1. Přihlaste se k webu [Azure Portal](<https://portal.azure.com>).

1. Vyberte **přidat nový prostředek**a do vyhledávacího pole zadejte "qna maker" a vyberte prostředek QnA Maker

    ![Vytvořit novou službu QnA Maker – přidat nový prostředek](../media/qnamaker-how-to-setup-service/create-new-resource.png)

1. Vyberte **vytvořit** po přečtení podmínek a ujednání.

    ![Vytvořit novou službu QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. V **QnA Maker**, vyberte příslušné úrovně a oblasti.

    ![Vytvořit novou službu QnA Maker – cenová úroveň a oblasti](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Zadejte **název** s jedinečný název pro identifikaci této služby QnA Maker. Tento název také identifikuje koncový bod QnA Maker, do kterého mají být asociována znalostních bází.
    * Zvolte **předplatné** ve kterém bude nasazen nástroj QnA Maker prostředků.
    * Vyberte **cenovou úroveň účtu správy** QnA Maker management Services (portál a rozhraní API pro správu). Zobrazit [tady](https://aka.ms/qnamaker-pricing) podrobnosti o cenách za skladové jednotky.
    * Vytvořte nový **skupiny prostředků** (doporučeno) nebo použijte již existující, ve které chcete nasadit tento prostředek QnA Maker. Nástroj QnA Maker vytvoří několik prostředků Azure; Při vytváření skupiny prostředků pro uložení těchto prostředků, můžete snadno vyhledat, spravovat a odstranit tyto prostředky podle názvu skupiny prostředků.
    * Zvolte **cenová úroveň hledání** služby Azure Search. Pokud se zobrazí možnost úrovně Free šedě, znamená to, že už máte bezplatnou Azure Search úrovni nasazené ve vašem předplatném. V takovém případě je potřeba spustit s úrovní Basic Azure Search. Zobrazit podrobnosti o cenách Azure search [tady](https://azure.microsoft.com/pricing/details/search/).
    * Zvolte **poloha při hledání** kde chcete data Azure Search k nasazení. Omezení kde musí být zákaznická data uložena informuje umístění, které jste vybrali pro Azure Search.
    * Zadejte název služby App service v **název aplikace**.
    * Ve výchozím nastavení výchozí služby App service na úrovni standard (S1). Tento plán můžete změnit po vytvoření. Viz podrobnosti o App service – ceny [tady](https://azure.microsoft.com/pricing/details/app-service/).
    * Zvolte **umístění webu** nasazená služby App Service.

        > [!NOTE]
        > Umístění pro hledání se může lišit od umístění webu.

    * Vyberte, jestli chcete povolit **Application Insights** nebo ne. Pokud **Application Insights** je povolené, nástroj QnA Maker shromažďuje telemetrická data na provoz, chatu a chyby.
    * Zvolte **umístění App insights** nasazená prostředku Application Insights.
    * Pro míry úspor nákladů, můžete [sdílet](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) některé, ale ne všechny prostředky Azure vytvořené pro nástroj QnA Maker. 

1. Jakmile se ověří všechna pole, můžete vybrat **vytvořit** ke spuštění nasazení z těchto služeb v rámci vašeho předplatného. To bude trvat několik minut.

1. Jakmile se nasazení dokončí, zobrazí se následující prostředky vytvořené v rámci vašeho předplatného.

    ![Prostředek vytvoří nová služba QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Oblasti služby správy

Služba správy nástroje QnA Maker, použít jenom pro portál & Počáteční zpracování dat, je dostupná jenom v západní části USA. Žádná data zákazníků uložená v této službě západní USA.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření a publikování znalostní báze](../Quickstarts/create-publish-knowledge-base.md)
