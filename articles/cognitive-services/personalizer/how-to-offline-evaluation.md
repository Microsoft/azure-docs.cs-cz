---
title: Jak provádět přizpůsobování offline vyhodnocení
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
ms.openlocfilehash: bd57880b11f56b13b4225652071593d29dcc6280
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515231"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analýza výukové smyčky pomocí offline vyhodnocení

Naučte se, jak dokončit offline testování a porozumět výsledkům.

Vyhodnocení v režimu offline umožňuje měřit, jak efektivní přizpůsobení přispěje k výchozímu chování vaší aplikace, informace o tom, jaké funkce přispěje nejvíc k přizpůsobení a že se automaticky zjistí nové nastavení strojového učení.

Další informace najdete v článku o [hodnoceních offline](concepts-offline-evaluation.md) .


## <a name="prerequisites"></a>Předpoklady

* Nakonfigurované smyčka přizpůsobeného přizpůsobování
* Smyčka přizpůsobování musí mít reprezentativní množství dat – jako jen ve svých protokolech doporučujeme minimálně 50 000 událostí pro smysluplné výsledky vyhodnocení. Volitelně můžete také dříve exportovat soubory _zásad učení_ , které můžete porovnat a testovat ve stejném vyhodnocení.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Postup spuštění nového offline vyhodnocení

1. V [Azure Portal](https://azure.microsoft.com/free/)vyhledejte prostředek přizpůsobení.
1. V Azure Portal otevřete část **vyhodnocení** a vyberte **vytvořit vyhodnocení**.
    ![In Azure Portal, přejít na oddíl * * vyhodnocení * * a vybrat * * vytvořit vyhodnocení * *. ](./media/offline-evaluation/create-new-offline-evaluation.png)
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

* **Online zásady**: aktuální zásady učení použité v přizpůsobeném nástroji
* **Směrný plán**: výchozí nastavení aplikace (podle první akce odeslané v rámci pořadí volání),
* **Random Policy: náhodné**pořadí řazení, které vždy vrátí náhodný výběr akcí ze zadaných.
* **Vlastní zásady**: další zásady učení nahrané při spuštění vyhodnocení.
* **Optimalizované zásady**: Pokud bylo vyhodnocení spuštěno s možností zjišťování optimalizované zásady, bude porovnána a bude možné ji stáhnout, nebo ji můžete nastavit jako zásady učení online, přičemž nahradíte tu aktuální.

![Graf výsledků nastavení offline vyhodnocení](./media/offline-evaluation/evaluation-results.png)

Efektivita [funkcí](concepts-features.md) pro akce a kontext.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, [Jak funguje hodnocení offline](concepts-offline-evaluation.md).
