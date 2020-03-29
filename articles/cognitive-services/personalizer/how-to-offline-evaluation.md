---
title: Jak provádět offline hodnocení - Personalizace
titleSuffix: Azure Cognitive Services
description: V tomto článku se ukáže, jak pomocí offline hodnocení měřit efektivitu aplikace a analyzovat vaše učení smyčky.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622791"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analyzujte svou učební smyčku pomocí offline hodnocení

Přečtěte si, jak dokončit offline hodnocení a porozumět výsledkům.

Offline hodnocení umožňují měřit, jak efektivní personalizace je ve srovnání s výchozí chování vaší aplikace, zjistit, jaké funkce přispívají nejvíce k personalizaci a objevovat nové hodnoty strojového učení automaticky.

Další informace najdete v informacích o [offline hodnoceních.](concepts-offline-evaluation.md)

## <a name="prerequisites"></a>Požadavky

* Nakonfigurovaná smyčka personalizátoru
* Personalizace smyčky musí mít reprezentativní množství dat - jako ballpark doporučujeme alespoň 50.000 událostí v protokolech pro smysluplné výsledky hodnocení. Volitelně můžete mít také dříve exportované soubory _zásad učení,_ které můžete porovnat a otestovat ve stejném hodnocení.

## <a name="run-an-offline-evaluation"></a>Spuštění offline hodnocení

1. Na [webu Azure Portal](https://azure.microsoft.com/free/)vyhledejte prostředek personalisty.
1. Na webu Azure Portal přejděte do části **Hodnocení** a vyberte **Vytvořit vyhodnocení**.
    ![Na webu Azure Portal přejděte do části **Hodnocení** a vyberte **Create Evaluation**.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Nakonfigurujte následující hodnoty:

    * Název hodnocení.
    * Počáteční a koncové datum – jedná se o data, která určují rozsah dat, která mají být v hodnocení uvedena. Tato data musí být k dispozici v protokolech, jak je uvedeno v hodnotě [uchovávání dat.](how-to-settings.md)
    * Zjišťování optimalizace nastaveno na **ano**.

    > [!div class="mx-imgBorder"]
    > ![Volba nastavení offline hodnocení](./media/offline-evaluation/create-an-evaluation-form.png)

1. Hodnocení můžete spustit tak, že vyberete **ok**.

## <a name="review-the-evaluation-results"></a>Zkontrolujte výsledky hodnocení

Spuštění vyhodnocení může trvat dlouhou dobu, v závislosti na množství dat ke zpracování, počtu zásad učení k porovnání a na tom, zda byla požadována optimalizace.

Po dokončení můžete vybrat hodnocení ze seznamu hodnocení a pak vybrat **Možnost Porovnat skóre aplikace s dalšími potenciálními nastaveními učení**. Tuto funkci vyberte, pokud chcete zobrazit, jak si vede aktuální zásady učení ve srovnání s novou zásadou.

1. Zkontrolujte výkon [zásad učení](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy).

    > [!div class="mx-imgBorder"]
    > [![Přezkoumat výsledky hodnocení](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Vyberte **Použít,** chcete-li použít zásadu, která vylepšuje model nejlépe pro vaše data.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [tom, jak offline hodnocení fungují](concepts-offline-evaluation.md).
