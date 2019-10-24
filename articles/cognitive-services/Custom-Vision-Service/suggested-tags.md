---
title: Rychlejší označování obrázků pomocí navrhovaných značek
titleSuffix: Azure Cognitive Services
description: V této příručce se dozvíte, jak pomocí navrhovaných značek rychleji označit velký počet obrázků při výuce Custom Visionch modelech.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: pafarley
ms.openlocfilehash: 06735240729fb2bfd21b87f592e143e9ceabb390
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753487"
---
# <a name="label-images-faster-with-suggested-tags"></a>Rychlejší označování obrázků pomocí navrhovaných značek

V této příručce se dozvíte, jak pomocí funkce navrhovaných značek při výuce Custom Visionho modelu pořídit velký počet imagí rychleji. 

Když označíte obrázky pro model Custom Vision, služba používá nejnovější vyučenou iteraci modelu pro předpověď popisků netagovaných obrázků. Pak zobrazuje tyto předpovědi jako navrhované značky na základě vybrané prahové hodnoty spolehlivosti a nejistoty předpovědi. Pak můžete buď potvrdit nebo změnit návrhy, a urychlit tak proces ručního tagování imagí pro školení.

## <a name="when-to-use-suggested-tags"></a>Kdy použít navrhované značky

Mějte na paměti následující omezení:

* Měli byste vyžadovat pouze navrhované značky pro obrázky, jejichž obsah již byl vyškolený. Nezískejte návrhy na novou značku, kterou právě začínáte naučit.

> [!IMPORTANT]
> Funkce Navrhované značky používá stejný [cenový model](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) jako běžný předpovědi. Při prvním spuštění navrhovaných značek pro sadu imagí se vám bude účtovat stejná jako u prediktivních volání. Potom služba uloží výsledky pro vybrané image v databázi po dobu 30 dnů a v tomto období je můžete kdykoli získat. Po 30 dnech se vám bude účtovat, pokud znovu vyžádáte navrhované značky.

## <a name="suggested-tags-workflow"></a>Pracovní postup navrhovaných značek

Následující kroky ukazují, jak používat funkci navrhovaných značek:

1. Nahrajte všechny školicí snímky do projektu Custom Vision.
1. Označte část sady dat a vyberte stejný počet imagí pro každou značku.
    > [!TIP]
    > Ujistěte se, že používáte všechny značky, pro které chcete návrhy později.
1. Spusťte školicí proces.
1. Po dokončení školení přejděte do **neoznačeného** zobrazení a v levém podokně vyberte tlačítko **získat navrhované značky** .
    > [!div class="mx-imgBorder"]
    > na kartě netagované bitové kopie se zobrazí tlačítko navrhované značky ![The. ](./media/suggested-tags/suggested-tags-button.png)
1. Vyberte sadu imagí, pro které chcete vytvořit návrhy. Měli byste získat pouze prvotní návrhy značek pro část netagovaných imagí. Při iterování tohoto procesu získáte lepší návrhy značek.
1. Potvrďte navrhované značky a opravte všechny, které nejsou správné.
    > [!TIP]
    > Obrázky s navrhovanými značkami jsou seřazené podle jejich předpovědi nejistoty (nižší hodnoty znamenají vyšší spolehlivost). Pořadí řazení můžete změnit pomocí možnosti **Seřadit podle nejistoty** . Pokud nastavíte pořadí na **vysoké**, můžete nejprve opravit vysoce nejistotu předpovědi a pak rychle potvrdit nejistotu.
    * V projektech klasifikace obrázků můžete vybrat a potvrdit značky v dávkách. Filtrovat zobrazení podle dané navrhované značky, zrušit výběr obrázků, které jsou označeny nesprávně, a pak potvrďte zbytek v dávce.
        > [!div class="mx-imgBorder"]
        > značky ![Suggested se zobrazují v dávkovém režimu pro mezipodniková s filtry. ](./media/suggested-tags/ic-batch-mode.png)

        Navrhované značky můžete použít také v jednotlivých režimech obrázku výběrem obrázku z galerie.

        ![Navrhované značky se zobrazí v samostatném režimu obrázků pro mezipodnikové.](./media/suggested-tags/ic-individual-image-mode.png)
    * V projektech detekce objektů se nepodporují služby Batch, ale můžete je dál filtrovat a řadit podle navrhovaných značek pro komplexnější možnosti označování. Miniatury netagovaných obrázků budou zobrazovat překryv ohraničujících rámečků, které označují umístění navrhovaných značek. Pokud nevyberete navrhovaný filtr značek, zobrazí se všechny neoznačené obrázky bez překrytí ohraničovacích rámečků.
        > [!div class="mx-imgBorder"]
        > značky ![Suggested se zobrazují v dávkovém režimu pro OD pro filtry. ](./media/suggested-tags/od-batch-mode.png)

        Chcete-li potvrdit značky detekce objektu, je nutné je použít pro každou jednotlivou image v galerii.

        ![Navrhované značky se zobrazí v samostatném režimu obrázku pro OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Spusťte školicí proces znovu.
1. Předchozí kroky opakujte, dokud nebudete spokojeni s kvalitou návrhů.

## <a name="next-steps"></a>Další kroky

Postupujte podle pokynů k rychlému zprovoznění a začněte s vytvářením a školením Custom Vision projektu.

* [Sestavení klasifikátoru](getting-started-build-a-classifier.md)
* [Sestavení detektoru objektu](get-started-build-detector.md)