---
title: Rychlejší značení obrázků pomocí inteligentního popisku
titleSuffix: Azure Cognitive Services
description: V této příručce se dozvíte, jak pomocí inteligentního popisku vygenerovat navrhované značky pro obrázky. Díky tomu můžete při výuce Custom Visionho modelu rychleji označit velký počet obrázků.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "73647757"
---
# <a name="label-images-faster-with-smart-labeler"></a>Rychlejší značení obrázků pomocí inteligentního popisku

V této příručce se dozvíte, jak pomocí inteligentního popisku vygenerovat navrhované značky pro obrázky. Díky tomu můžete při výuce Custom Visionho modelu rychleji označit velký počet obrázků.

Když označíte obrázky pro model Custom Vision, služba používá nejnovější vyučenou iteraci modelu pro předpověď popisků netagovaných obrázků. Pak zobrazuje tyto předpovědi jako navrhované značky na základě vybrané prahové hodnoty spolehlivosti a nejistoty předpovědi. Pak můžete buď potvrdit nebo změnit návrhy, a urychlit tak proces ručního tagování imagí pro školení.

## <a name="when-to-use-smart-labeler"></a>Kdy použít inteligentní popisek

Mějte na paměti následující omezení:

* Měli byste vyžadovat pouze navrhované značky pro obrázky, jejichž obsah již byl vyškolený. Nezískejte návrhy na novou značku, kterou právě začínáte naučit.

> [!IMPORTANT]
> Funkce inteligentního popisku používá stejný [cenový model](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) jako běžný předpovědi. Při prvním spuštění navrhovaných značek pro sadu imagí se vám bude účtovat stejná jako u prediktivních volání. Potom služba uloží výsledky pro vybrané image v databázi po dobu 30 dnů a v tomto období je můžete kdykoli získat. Po 30 dnech se vám bude účtovat, pokud znovu vyžádáte navrhované značky.

## <a name="smart-labeler-workflow"></a>Pracovní postup inteligentního popisku

Následující kroky ukazují, jak používat inteligentní popisky:

1. Nahrajte všechny školicí snímky do projektu Custom Vision.
1. Označte část sady dat a vyberte stejný počet imagí pro každou značku.
    > [!TIP]
    > Ujistěte se, že používáte všechny značky, pro které chcete návrhy později.
1. Spusťte školicí proces.
1. Po dokončení školení přejděte do **neoznačeného** zobrazení a v levém podokně vyberte tlačítko **získat navrhované značky** .
    > [!div class="mx-imgBorder"]
    > ![Tlačítko navrhované značky se zobrazí na kartě netagované bitové kopie.](./media/suggested-tags/suggested-tags-button.png)
1. V automaticky otevíraném okně, které se zobrazí, nastavte počet imagí, pro které chcete vytvořit návrhy. Měli byste získat pouze prvotní návrhy značek pro část netagovaných imagí. Při iterování tohoto procesu získáte lepší návrhy značek.
1. Potvrďte navrhované značky a opravte všechny, které nejsou správné.
    > [!TIP]
    > Obrázky s navrhovanými značkami jsou seřazené podle jejich předpovědi nejistoty (nižší hodnoty znamenají vyšší spolehlivost). Pořadí řazení můžete změnit pomocí možnosti **Seřadit podle nejistoty** . Pokud nastavíte pořadí na **vysoké**, můžete nejprve opravit vysoce nejistotu předpovědi a pak rychle potvrdit nejistotu.
    * V projektech klasifikace obrázků můžete vybrat a potvrdit značky v dávkách. Filtrovat zobrazení podle dané navrhované značky, zrušit výběr obrázků, které jsou označeny nesprávně, a pak potvrďte zbytek v dávce.
        > [!div class="mx-imgBorder"]
        > ![Navrhované značky se zobrazí v dávkovém režimu pro mezipodniková s filtry.](./media/suggested-tags/ic-batch-mode.png)

        Navrhované značky můžete použít také v jednotlivých režimech obrázku výběrem obrázku z galerie.

        ![Navrhované značky se zobrazí v samostatném režimu obrázků pro mezipodnikové.](./media/suggested-tags/ic-individual-image-mode.png)
    * V projektech detekce objektů se nepodporují služby Batch, ale můžete je dál filtrovat a řadit podle navrhovaných značek pro komplexnější možnosti označování. Miniatury netagovaných obrázků budou zobrazovat překryv ohraničujících rámečků, které označují umístění navrhovaných značek. Pokud nevyberete navrhovaný filtr značek, zobrazí se všechny neoznačené obrázky bez překrytí ohraničovacích rámečků.
        > [!div class="mx-imgBorder"]
        > ![Navrhované značky se zobrazí v dávkovém režimu pro filtry OD z.](./media/suggested-tags/od-batch-mode.png)

        Chcete-li potvrdit značky detekce objektu, je nutné je použít pro každou jednotlivou image v galerii.

        ![Navrhované značky se zobrazí v samostatném režimu obrázku pro OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Spusťte školicí proces znovu.
1. Předchozí kroky opakujte, dokud nebudete spokojeni s kvalitou návrhů.

## <a name="next-steps"></a>Další kroky

Postupujte podle pokynů k rychlému zprovoznění a začněte s vytvářením a školením Custom Vision projektu.

* [Sestavení klasifikátoru](getting-started-build-a-classifier.md)
* [Sestavení detektoru objektů](get-started-build-detector.md)