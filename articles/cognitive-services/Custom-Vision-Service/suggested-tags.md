---
title: Rychlejší označení obrázků pomocí funkce Smart Labeler
titleSuffix: Azure Cognitive Services
description: V této příručce se dozvíte, jak pomocí inteligentního štítkovače generovat navrhované značky pro obrázky. To vám umožní označit velký počet obrázků rychleji při trénování modelu vlastní vize.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647757"
---
# <a name="label-images-faster-with-smart-labeler"></a>Rychlejší označení obrázků pomocí funkce Smart Labeler

V této příručce se dozvíte, jak pomocí inteligentního štítkovače generovat navrhované značky pro obrázky. To vám umožní označit velký počet obrázků rychleji při trénování modelu vlastní vize.

Když tagujete obrazy pro vlastní model vidění, služba používá nejnovější trénované iterace modelu předpovědět popisky netagovaných obrazů. Potom zobrazí tyto předpovědi jako navrhované značky, na základě vybrané prahové hodnoty spolehlivosti a nejistoty předpovědi. Návrhy pak můžete potvrdit nebo změnit a urychlit tak proces ručního označování obrázků pro školení.

## <a name="when-to-use-smart-labeler"></a>Kdy použít smart labeler

Mějte na paměti následující omezení:

* Měli byste požadovat pouze navrhované značky pro obrázky, jejichž obsah již byl trénován jednou. Nedostávají návrhy na novou značku, kterou právě začínáte trénovat.

> [!IMPORTANT]
> Funkce Smart Labeler používá stejný [cenový model](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) jako běžné předpovědi. Při prvním spuštění navržených značek pro sadu obrázků vám budou účtovány stejné poplatky jako u předpovědních volání. Poté služba ukládá výsledky vybraných obrázků do databáze po dobu 30 dnů a můžete k nim přistupovat kdykoli v tomto období zdarma. Po 30 dnech vám budou účtovány poplatky, pokud znovu požádáte o jejich navrhované značky.

## <a name="smart-labeler-workflow"></a>Pracovní postup Inteligentní labeler

Následující kroky ukazují, jak používat inteligentní štítkovač:

1. Nahrajte všechny své tréninkové obrázky do projektu Custom Vision.
1. Označte část datové sady a pro každou značku zvolte stejný počet obrázků.
    > [!TIP]
    > Ujistěte se, že používáte všechny značky, pro které chcete návrhy později.
1. Zahajte tréninkový proces.
1. Po dokončení školení přejděte do zobrazení **Netagované** a v levém podokně vyberte tlačítko **Získat navrhované značky.**
    > [!div class="mx-imgBorder"]
    > ![Tlačítko navrhované značky se zobrazí pod kartou Neoznačené obrázky.](./media/suggested-tags/suggested-tags-button.png)
1. V převíracích okně nastavte počet obrázků, pro které chcete návrhy. Návrhy počátečních značek byste měli získat pouze pro část neoznačených obrazů. Budete mít lepší návrhy značek, jak budete iterate prostřednictvím tohoto procesu.
1. Potvrďte navrhované značky a opravte všechny, které nejsou správné.
    > [!TIP]
    > Obrázky s navrženými značkami jsou seřazeny podle jejich předpovědní nejistoty (nižší hodnoty označují vyšší spolehlivost). Pořadí řazení můžete změnit pomocí možnosti **Seřadit podle nejistoty.** Pokud nastavíte pořadí na **vysokou na nízkou**, můžete nejprve opravit předpovědi vysoké nejistoty a pak rychle potvrdit ty s nízkou nejistotou.
    * V projektech klasifikace obrázků můžete vybrat a potvrdit značky v dávkách. Vyfiltrujte zobrazení podle dané navrhované značky, odznačte obrázky, které jsou nesprávně tagované, a pak potvrďte zbytek v dávce.
        > [!div class="mx-imgBorder"]
        > ![Navrhované značky jsou zobrazeny v dávkovém režimu pro ic s filtry.](./media/suggested-tags/ic-batch-mode.png)

        Navrhované tagy můžete také použít v režimu jednotlivých obrazů výběrem obrázku z galerie.

        ![Navrhované značky se zobrazují v individuálním obrazovém režimu pro IC.](./media/suggested-tags/ic-individual-image-mode.png)
    * V projektech zjišťování objektů nejsou dávková potvrzení podporována, ale stále můžete filtrovat a řadit podle navržených značek pro organizovanější prostředí pro označování. Miniatury neoznačených obrázků zobrazí překrytí ohraničovacích rámečků označujících umístění navrhovaných značek. Pokud nevyberete navrhovaný filtr značek, zobrazí se všechny neoznačené obrázky bez překrytí ohraničovacích rámečků.
        > [!div class="mx-imgBorder"]
        > ![Navrhované značky jsou zobrazeny v dávkovém režimu pro OD s filtry.](./media/suggested-tags/od-batch-mode.png)

        Chcete-li potvrdit značky detekce objektů, musíte je použít na každý jednotlivý obrázek v galerii.

        ![Navrhované značky jsou zobrazeny v individuálním obrazovém režimu pro OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Spusťte znovu tréninkový proces.
1. Opakujte předchozí kroky, dokud nebudete spokojeni s kvalitou návrhu.

## <a name="next-steps"></a>Další kroky

Chcete-li začít vytvářet a školit vlastní projekt vize, postupujte podle rychlého startu.

* [Sestavení klasifikátoru](getting-started-build-a-classifier.md)
* [Sestavení detektoru objektů](get-started-build-detector.md)