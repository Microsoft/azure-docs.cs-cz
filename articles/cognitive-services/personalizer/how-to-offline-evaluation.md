---
title: Přizpůsobování offline vyhodnocení
titleSuffix: Azure Cognitive Services
description: Naučte se analyzovat výukovou smyčku pomocí offline vyhodnocení.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: diberry
ms.openlocfilehash: a157b36ad2b4f5a7f2e50a9609d8acd621efd5a8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884470"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analýza výukové smyčky pomocí offline vyhodnocení

Naučte se, jak dokončit offline testování a porozumět výsledkům.

Vyhodnocení v režimu offline umožňuje měřit, jak efektivní přizpůsobení přispěje k výchozímu chování vaší aplikace, informace o tom, jaké funkce přispěje nejvíc k přizpůsobení a že se automaticky zjistí nové nastavení strojového učení.

Další informace najdete v článku o [hodnoceních offline](concepts-offline-evaluation.md) .


## <a name="prerequisites"></a>Požadavky

* Nakonfigurované smyčka přizpůsobeného přizpůsobování
* Smyčka přizpůsobování musí mít reprezentativní množství dat – jako jen ve svých protokolech doporučujeme minimálně 50 000 událostí pro smysluplné výsledky vyhodnocení. Volitelně můžete také dříve exportovat soubory _zásad učení_ , které můžete porovnat a testovat ve stejném vyhodnocení.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Postup spuštění nového offline vyhodnocení

1. V [Azure Portal](https://azure.microsoft.com/free/)vyhledejte prostředek přizpůsobení.
1. V Azure Portal otevřete část **vyhodnocení** a vyberte **vytvořit vyhodnocení**.
    ![V Azure Portal přejít na oddíl * * vyhodnocení * * a vyberte * * vytvořit vyhodnocení * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Vyberte následující nastavení:

    * Název vyhodnocení
    * Počáteční a koncové datum – jedná se o kalendářní data v minulosti, která určují rozsah dat, která se mají použít při vyhodnocování. Tato data musí být k dispozici v protokolech, jak je uvedeno v nastavení [uchovávání dat](how-to-settings.md) .
    * Zjišťování optimalizace je nastavené na **Ano** .

    ![Zvolit nastavení online vyhodnocení](./media/offline-evaluation/create-an-evaluation-form.png)

1. Kliknutím na **tlačítko OK**spusťte hodnocení. 

## <a name="results"></a>Výsledky

Spuštění hodnocení může trvat dlouhou dobu, v závislosti na množství dat, která se mají zpracovat, počtu porovnávacích zásad a na tom, jestli byla požadovaná optimalizace.

Po dokončení můžete vybrat vyhodnocení ze seznamu hodnocení. 

Mezi porovnávacími zásadami učení patří:

* **Online zásady**: Aktuální zásady učení použité v přizpůsobeném nástroji
* **Standardní hodnoty**: Výchozí hodnota aplikace (určená první akcí odeslanou při voláních pořadí),
* **Náhodné zásady**: Chování imaginárního pořadí, které vždy vrátí náhodný výběr akcí ze zadaných.
* **Vlastní zásady**: Při spuštění vyhodnocení se nahrály další zásady učení.
* **Optimalizované zásady**: Pokud bylo vyhodnocení spuštěno s možností zjišťování optimalizované zásady, bude porovnána a bude možné ji stáhnout, nebo ji můžete nastavit jako zásady učení online, přičemž nahradíte tu aktuální.

![Graf výsledků nastavení offline vyhodnocení](./media/offline-evaluation/evaluation-results.png)

Efektivita [funkcí](concepts-features.md) pro akce a kontext.

## <a name="next-steps"></a>Další postup

* Přečtěte si, [Jak funguje hodnocení offline](concepts-offline-evaluation.md).
