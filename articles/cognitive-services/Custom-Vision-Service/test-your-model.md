---
title: Testování a přeučování modelů – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak otestovat bitovou kopii a pak přes ni přeučování modelu.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 21d8f6a3b10de17172ff59463cca4162585fe40f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858983"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testování a přeučování modelů pomocí služby Custom Vision Service

Po tréninku modelu, můžete rychle otestovat pomocí bitové kopie uložené místně nebo online bitové kopie. Test používá nedávno trénovaného iterace.

## <a name="test-your-model"></a>Testování modelu

1. Z [vizi vlastní webovou stránku](https://customvision.ai), vyberte svůj projekt. Vyberte **rychle otestovat spuštěním** v pravé horní nabídce. Tato akce otevře okno s popiskem **rychle otestovat spuštěním**.

    ![Rychlé otestování tlačítko se zobrazí v pravém horním rohu okna.](./media/test-your-model/quick-test-button.png)

2. V **rychlého testu** okno, kliknutím na webu **odešlete Image** pole a zadejte adresu URL image, kterou chcete použít pro váš test. Pokud chcete místo toho použít místně uložený obrázek, klikněte na tlačítko **procházet místní soubory** tlačítko a vyberte soubor místní image.

    ![Obrázek stránky odeslat image](./media/test-your-model/submit-image.png)

Uprostřed stránky se zobrazí obrázek, který jste vybrali. Potom výsledky se zobrazí pod obrázkem ve formě tabulky se dvěma sloupci, s popiskem **značky** a **spolehlivosti**. Po zobrazení výsledků můžete zavřít **rychle otestovat spuštěním** okna.

Můžete teď do modelu přidat tento testovací obrázek a potom přeučování modelu.

## <a name="use-the-predicted-image-for-training"></a>Použijte předpokládané obrázků k trénování.

Pokud chcete použít image dříve odešle ke školení, postupujte následovně:

1. Chcete-li zobrazit Image odeslat do třídění, otevřete [vizi vlastní webovou stránku](https://customvision.ai) a vyberte __předpovědi__ kartu.

    ![Obrázek karty predikcí](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Výchozí zobrazení zobrazuje obrázky z aktuální iteraci. Můžete použít __iterace__ rozevírací nabídka pole k zobrazení obrázků odeslaných během předchozími iteracemi.

2. Najeďte myší na obrázek zobrazíte značky, které byly předpovídané pomocí třídění.

    > [!TIP]
    > Bitové kopie jsou hodnoceny, tak, že jsou obrázky, které přinést většina zisky třídění v horní části. Chcete-li vybrat jinou řazení, použijte __řazení__ oddílu.

    Přidání obrázku na trénovací data, vyberte bitovou kopii, vyberte značku a potom vyberte __uložte a zavřete__. Na obrázku je odebrán z __předpovědi__ a přidán do trénovacích obrázků. Můžete ho zobrazit výběrem __Trénovacích obrázků__ kartu.

    ![Obrázek označení stránky](./media/test-your-model/tag-image.png)

3. Použití __trénování__ tlačítko přeučování třídění.

## <a name="next-steps"></a>Další postup

[Vylepšení klasifikátoru](getting-started-improving-your-classifier.md)
