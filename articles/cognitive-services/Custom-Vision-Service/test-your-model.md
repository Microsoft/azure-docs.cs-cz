---
title: Testování a přeškolování modelu – Služba Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak otestovat obrázek a potom ho použít k přeškolení modelu ve službě Custom Vision.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: c6ed8869f6d33609381a42fd22d728e9e5542802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73721202"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testování a přeškolení modelu pomocí služby Custom Vision Service

Po trénování modelu jej můžete rychle otestovat pomocí místně uloženého obrázku nebo online obrázku. Test používá naposledy vyškolené iterace.

## <a name="test-your-model"></a>Testování modelu

1. Na [webové stránce Vlastní vize](https://customvision.ai)vyberte svůj projekt. Vpravo od horního řádku nabídek vyberte **Rychlý test.** Tato akce otevře okno s popiskem **Rychlý test**.

    ![Tlačítko Rychlý test se zobrazí v pravém horním rohu okna.](./media/test-your-model/quick-test-button.png)

2. V okně **Rychlý test** klikněte do pole **Odeslat obrázek** a zadejte adresu URL obrázku, který chcete pro test použít. Pokud chcete místo toho použít místně uložený obrázek, klepněte na tlačítko **Procházet místní soubory** a vyberte místní obrazový soubor.

    ![Obrázek stránky odeslat obrázek](./media/test-your-model/submit-image.png)

Vybraný obrázek se zobrazí uprostřed stránky. Pak se výsledky zobrazí pod obrázkem ve formě tabulky se dvěma sloupci, označené **tagy** a **důvěra**. Po zobrazení výsledků můžete zavřít okno **rychlý test.**

Nyní můžete přidat tento testovací obrázek do modelu a potom přeškolit model.

## <a name="use-the-predicted-image-for-training"></a>Použití předpovídaného obrázku pro školení

Chcete-li použít obrázek odeslaný dříve pro školení, použijte následující kroky:

1. Chcete-li zobrazit obrázky odeslané klasifikátoru, otevřete [webovou stránku Vlastní vize](https://customvision.ai) a vyberte kartu __Předpovědi.__

    ![Obrázek karty předpovědi](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Výchozí zobrazení zobrazuje obrázky z aktuální iterace. Rozevírací pole __Iterace__ můžete použít k zobrazení bitových kopií odeslaných během předchozích iterací.

2. Najeďte nad obrázek zobrazíte značky, které byly předpovězeny třídění.

    > [!TIP]
    > Obrázky jsou seřazeny tak, aby obrázky, které mohou přinést nejvíce zisků klasifikátoru jsou nahoře. Chcete-li vybrat jiné řazení, použijte oddíl __Řazení.__

    Pokud chcete do trénovacích dat přidat obrázek, vyberte ho, vyberte značku a pak vyberte __Uložit a zavřít__. Obrázek je odebrán z __předpovědi__ a přidány do obrázky školení. Můžete ji zobrazit výběrem karty __Trénující obrázky.__

    ![Obrázek stránky tagování](./media/test-your-model/tag-image.png)

3. Pomocí tlačítka __Vlak__ přeškolit třídění.

## <a name="next-steps"></a>Další kroky

[Vylepšení klasifikátoru](getting-started-improving-your-classifier.md)
