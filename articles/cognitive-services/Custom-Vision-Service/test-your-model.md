---
title: Testování a přeučení modelu – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak otestovat image a pak ji použít k reorganizaci modelu ve službě Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 736d135323e2a2df38a13dc46189e2a243fbe381
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097530"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testování a přeškolování modelu pomocí Custom Vision Service

Po vytvoření vašeho modelu ho můžete rychle otestovat pomocí místně uložené Image nebo online image. Test používá naposledy vyškolenou iteraci.

## <a name="test-your-model"></a>Testování modelu

1. Z [webové stránky Custom Vision](https://customvision.ai)vyberte svůj projekt. Na pravé straně horního panelu nabídek vyberte **rychlý test** . Tato akce otevře okno s popisem **rychlého testu**.

    ![Tlačítko rychlý test se zobrazí v pravém horním rohu okna.](./media/test-your-model/quick-test-button.png)

2. V okně **rychlé testování** klikněte na pole **Odeslat obrázek** a zadejte adresu URL obrázku, který chcete použít pro test. Pokud místo toho chcete použít místně uloženou bitovou kopii, klikněte na tlačítko **Procházet místní soubory** a vyberte místní soubor bitové kopie.

    ![Obrázek stránky odeslat obrázek](./media/test-your-model/submit-image.png)

Obrázek, který vyberete, se zobrazí uprostřed stránky. Výsledky se pak zobrazí pod obrázkem ve formě tabulky se dvěma sloupci, **popisky a** **spolehlivost**. Po zobrazení výsledků můžete zavřít okno s **rychlým testem** .

Teď můžete do modelu přidat tuto testovací image a pak svůj model přeškolit.

## <a name="use-the-predicted-image-for-training"></a>Použití předpovězené image pro školení

Pokud chcete použít image odeslanou dříve pro školení, použijte následující postup:

1. Chcete-li zobrazit obrázky odeslané do třídění, otevřete [webovou stránku Custom Vision](https://customvision.ai) a vyberte kartu __předpovědi__ .

    ![Obrázek karty předpovědi](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Výchozí zobrazení zobrazuje obrázky z aktuální iterace. Pomocí rozevíracího pole __iterace__ můžete zobrazit obrázky odeslané během předchozích iterací.

2. Najeďte ukazatelem myši na obrázek a zobrazí se značky, které třídění vypoví.

    > [!TIP]
    > Obrázky jsou seřazené tak, aby v horní části byly obrázky, které mohou přenést největší zisky do třídění. Chcete-li vybrat jiné řazení, použijte oddíl __řazení__ .

    Chcete-li přidat obrázek do školicích dat, vyberte obrázek, vyberte značku a pak vyberte __Uložit a zavřít__. Obrázek se odebere z __předpovědi__ a přidá se do školicích imagí. Můžete ji zobrazit výběrem karty __školicích imagí__ .

    ![Obrázek stránky označování](./media/test-your-model/tag-image.png)

3. Pro přeučení třídění použijte tlačítko __výuka__ .

## <a name="next-steps"></a>Další kroky

[Vylepšení modelu](getting-started-improving-your-classifier.md)
