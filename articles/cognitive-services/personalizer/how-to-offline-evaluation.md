---
title: Jak provádět přizpůsobování offline vyhodnocení
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak používat offline testování k měření efektivity vaší aplikace a k analýze studijních smyček.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622791"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analýza výukové smyčky pomocí offline vyhodnocení

Naučte se, jak dokončit offline testování a porozumět výsledkům.

Vyhodnocení v režimu offline umožňuje měřit, jak efektivní přizpůsobení přispěje k výchozímu chování vaší aplikace, informace o tom, jaké funkce přispěje nejvíc k přizpůsobení a že se automaticky objevují nové hodnoty strojového učení.

Další informace najdete v článku o [hodnoceních offline](concepts-offline-evaluation.md) .

## <a name="prerequisites"></a>Požadavky

* Nakonfigurované smyčka přizpůsobeného přizpůsobování
* Smyčka přizpůsobování musí mít reprezentativní množství dat – jako jen ve svých protokolech doporučujeme minimálně 50 000 událostí pro smysluplné výsledky vyhodnocení. Volitelně můžete také dříve exportovat soubory _zásad učení_ , které můžete porovnat a testovat ve stejném vyhodnocení.

## <a name="run-an-offline-evaluation"></a>Spustit zkušební verzi offline

1. V [Azure Portal](https://azure.microsoft.com/free/)vyhledejte prostředek pro přizpůsobení.
1. V Azure Portal otevřete část **vyhodnocení** a vyberte **vytvořit vyhodnocení**.
    ![v Azure Portal, přejít na oddíl * * vyhodnocení * * a vybrat * * vytvořit vyhodnocení * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Nakonfigurujte následující hodnoty:

    * Název vyhodnocení.
    * Počáteční a koncové datum – jedná se o data, která určují rozsah dat, která se mají použít při vyhodnocování. Tato data musí být k dispozici v protokolech, jak je uvedeno v hodnotě [uchovávání dat](how-to-settings.md) .
    * Zjišťování optimalizace je nastaveno na **Ano**.

    > [!div class="mx-imgBorder"]
    > ![zvolit nastavení online vyhodnocení](./media/offline-evaluation/create-an-evaluation-form.png)

1. Kliknutím na **tlačítko OK**spusťte hodnocení.

## <a name="review-the-evaluation-results"></a>Kontrola výsledků hodnocení

Spuštění hodnocení může trvat dlouhou dobu, v závislosti na množství dat, která se mají zpracovat, počtu porovnávacích zásad a na tom, jestli byla požadovaná optimalizace.

Po dokončení můžete vybrat vyhodnocení ze seznamu hodnocení a pak vybrat **porovnat skóre aplikace s dalšími možnými nastaveními učení**. Tuto funkci vyberte, pokud chcete zjistit, jak vaše aktuální zásada učení funguje v porovnání s novou zásadou.

1. Zkontrolujte výkon [zásad učení](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy).

    > [!div class="mx-imgBorder"]
    > [![kontrola výsledků hodnocení](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Výběrem **použít použijete** zásadu, která vylepšuje model nejlépe pro vaše data.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [tom, jak funguje hodnocení offline](concepts-offline-evaluation.md).
