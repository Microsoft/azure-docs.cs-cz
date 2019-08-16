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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a8a75601daf36ca21ea56a5930219d7d467f0c85
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69557822"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Jak analyzovat výukovou smyčku pomocí offline vyhodnocení


Naučte se, jak dokončit offline testování a porozumět výsledkům.

Vyhodnocení v režimu offline umožňuje měřit, jak efektivní přizpůsobení přispěje k výchozímu chování vaší aplikace, informace o tom, jaké funkce přispěje nejvíc k přizpůsobení a že se automaticky zjistí nové nastavení strojového učení.

Další informace najdete v článku o [hodnoceních offline](concepts-offline-evaluation.md) .


## <a name="prerequisites"></a>Požadavky

1. Musíte mít nakonfigurovanou smyčku pro přizpůsobení.
1. Smyčka přizpůsobování musí mít reprezentativní množství dat – jako jen my recommmend alespoň 50 000 událostí ve svých protokolech pro smysluplné výsledky vyhodnocení.

Volitelně můžete také dříve exportovat soubory _zásad učení_ , které můžete porovnat a testovat ve stejném vyhodnocení.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Postup spuštění nového offline vyhodnocení

1. Vyhledejte prostředek smyčky individuálního nastavení v Azure Portal.
1. Přejděte do části hodnocení.
1. Klikněte na nové vyhodnocení.
1. Vyberte počáteční a koncové datum pro offline testování. Jedná se o data v minulosti, která určují rozsah dat, která se mají použít při vyhodnocování. Tato data musí být k dispozici v protokolech, jak je uvedeno v nastavení [uchovávání dat](how-to-settings.md) .
1. Volitelně můžete nahrát vlastní zásady učení. 
1. Určete, jestli má přidaný uživatel vytvořit optimalizované zásady učení na základě chování uživatele zjištěného v tomto časovém období.
1. Spustit vyhodnocení

## <a name="results"></a>Výsledky

Spuštění hodnocení může trvat dlouhou dobu, v závislosti na množství dat, která se mají zpracovat, počtu porovnávacích zásad a na tom, jestli byla požadovaná optimalizace.

Po dokončení můžete zobrazit následující výsledky:

1. Porovnání zásad učení, včetně:
    * **Online zásady**: Aktuální zásady učení použité v přizpůsobeném nástroji
    * **Standardní hodnoty**: Výchozí hodnota aplikace (určená první akcí odeslanou při voláních pořadí),
    * **Náhodné zásady**: Chování imaginárního pořadí, které vždy vrátí náhodný výběr akcí ze zadaných.
    * **Vlastní zásady**: Při spuštění vyhodnocení se nahrály další zásady učení.
    * **Optimalizované zásady**: Pokud bylo vyhodnocení spuštěno s možností zjišťování optimalizované zásady, bude porovnána a bude možné ji stáhnout, nebo ji můžete nastavit jako zásady učení online, přičemž nahradíte tu aktuální.

1. Efektivita [funkcí](concepts-features.md) pro akce a kontext.


## <a name="more-information"></a>Další informace

* Přečtěte si, [Jak funguje hodnocení offline](concepts-offline-evaluation.md).
