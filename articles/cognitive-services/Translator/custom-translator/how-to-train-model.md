---
title: Trénování modelu – vlastní Translator
titleSuffix: Azure Cognitive Services
description: Trénování modelu je důležitým krokem při vytváření modelu překladu. Školení se stane založené na dokumenty, které jste vybrali pro tohoto školení.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 2d9c6a9636629d3ad05d50320e00ed4c4d7f0b83
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389286"
---
# <a name="train-a-model"></a>Učení modelu

Trénování modelu je důležitým krokem při vytváření modelu překladu, protože bez školení, nejde sestavit model. Školení se stane založené na dokumenty, které vyberete pro školení.

K natrénování modelu:

1.  Vyberte projekt, ve které chcete sestavit model.

2.  Na kartě Data pro projekt se zobrazí všechny dokumenty, které jsou relevantní pro dvojici jazyk projektu. Ručně vyberte dokumenty, které chcete použít k natrénování modelu. Můžete vybrat trénování, ladění a testování dokumenty z této obrazovky. Také vám stačí vybrat cvičnou sadou a vlastní Translator vytvořit ladění a testování sad.

    -  Název dokumentu: Název dokumentu.

    -  Párování: Pokud tento dokument je dokument paralelní nebo jeden jazyk. Jeden jazyk dokumenty nejsou aktuálně podporovány pro vzdělávání.

    -  Typ dokumentu: Může být školení, ladění, testování nebo slovníku.

    -  Dvojice jazyka: Tím se zobrazí zdrojový a cílový jazyk pro projekt.

    -  Věty zdroje: Zobrazuje počet věty extrahovaná ze zdrojového souboru.

    -  Věty cíl: Zobrazuje počet věty extrahují z cílového souboru.

    ![Trénování modelu](media/how-to/how-to-train-model.png)

3.  Klikněte na tlačítko trénování.

4.  V dialogovém okně zadejte název pro váš model.

5.  Klikněte na tlačítko trénování modelu.

    ![Dialogové okno Train model](media/how-to/how-to-train-model-2.png)

6.  Vlastní Translator předloží školení a zobrazit stav školení v kartě modely.

    ![Trénování modelu stránky](media/how-to/how-to-train-model-3.png)

>[!Note]
>Vlastní Translator podporuje 10 souběžných školení v rámci pracovního prostoru v libovolném bodě v čase.


## <a name="edit-a-model"></a>Upravit model

Můžete upravit model s použitím odkazu pro úpravy na stránce s podrobnostmi modelu.

1.  Klikněte na ikonu tužky.

    ![Upravit model](media/how-to/how-to-edit-model.png)

2.  Změnu dialogového okna

    1.  Název modelu (povinné): Dejte smysluplný název modelu.

        ![Další dialogové okno Upravit](media/how-to/how-to-edit-model-dialog.png)

3.  Klikněte na Uložit.


## <a name="next-steps"></a>Další postup

- Přečtěte si [jak chcete-li zobrazit podrobné informace o modelu](how-to-view-model-details.md).
