---
title: Testování a přeučování modelu – vlastní vize služba – Azure kognitivní Services | Microsoft Docs
description: Informace o testování bitovou kopii a použít jej k přeučování modelu.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 1933b1a45844ac99308baebe59b49687a957abfa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342997"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testování a přeučování model se vlastní vize služby

Poté, co jste natrénování modelu, můžete ji pomocí bitové kopie uložené místně nebo online image rychle otestovat. Test používá nedávno vyškolení iterací.

## <a name="test-your-model"></a>Testování modelu

1. Z [vize vlastní webové stránky](https://customvision.ai), vyberte projektu. Vyberte **rychlý Test** na pravé straně panelu horní nabídce. Tato akce otevře okno s názvem bez přípony **rychlý Test**.

    ![Tlačítko Testovat rychlé se zobrazí v pravém horním rohu okna.](./media/test-your-model/quick-test-button.png)

2. V **rychlý Test** , klikněte v okně **odeslání Image** pole a zadejte adresu URL bitové kopie, kterou chcete použít pro svůj test. Pokud chcete místo toho použít místně uložené bitovou kopii, klikněte **procházet místních souborů** tlačítko a vyberte obrázek místní soubor.

    ![Obrázek stránky image odeslání](./media/test-your-model/submit-image.png)

Bitovou kopii, kterou vyberete, objeví se uprostřed stránky. Pak výsledky se zobrazí pod obrázkem ve formě tabulky se dvěma sloupci, s názvem bez přípony **značky** a **spolehlivosti**. Po zobrazení výsledků můžete zavřít **rychlý Test** okno.

Můžete teď přidejte tuto bitovou kopii testovací modelu a potom přeučování modelu.

## <a name="use-the-predicted-image-for-training"></a>Použijte bitovou kopii předpokládaných pro školení.

Chcete-li použít bitovou kopii odeslání dříve školení, použijte následující kroky:

1. Chcete-li zobrazit obrázky odeslané do třídění, otevřete [vize vlastní webové stránky](https://customvision.ai) a vyberte __předpovědi__ kartě.

    ![Obrázek na kartě předpovědi](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Výchozí zobrazení ukazuje bitové kopie z na aktuální iteraci. Můžete použít __iterace__ rozevírací pole, které chcete zobrazit obrázky odeslaných během předchozí iterací.

2. Podržte ukazatel nad najdete v části značky, které byly předpovědět třídění bitovou kopii.

    > [!TIP]
    > Bitové kopie, jsou seřazeny, tak, aby bitové kopie, které můžete zahrnout dostane většině k třídění v horní části. Pokud chcete vybrat jiný řazení, použijte __řazení__ části.

    Chcete-li přidat bitovou kopii do Cvičná data, vyberte bitovou kopii, vyberte značky a pak vyberte __uložte a zavřete__. Obrázek se odebere z __předpovědi__ a přidat do bitové kopie školení. Můžete ji zobrazit výběrem __školení image__ kartě.

    ![Obrázek označování stránky](./media/test-your-model/tag-image.png)

3. Použití __Train__ tlačítko přeučování třídění.

## <a name="next-steps"></a>Další postup

[Zlepšení vaší třídění](getting-started-improving-your-classifier.md)