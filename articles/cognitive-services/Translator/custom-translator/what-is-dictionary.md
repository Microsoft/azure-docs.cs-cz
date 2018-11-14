---
title: Co je slovník? – Vlastní Translator
titleSuffix: Azure Cognitive Services
description: Slovník je zarovnaný dokumentu, který určuje seznam fráze nebo věty (a jejich překlady), který chcete, aby Microsoft Translatoru pro převod stejným způsobem. Slovníky se někdy také označují jako glosáře pro nebo pojem základních tříd.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 3a92e41ad9611981777012cd231845587a6f947b
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627301"
---
# <a name="what-is-a-dictionary"></a>Co je slovník?

Slovník je zarovnaný páru dokumentů, která určuje seznam fráze nebo věty a jejich odpovídající překlady. Použití slovníku v trénování, pokud chcete Microsoft Translatoru vždy přeložit všechny instance zdroje fráze nebo větu pomocí překladu, který jste zadali ve slovníku. Slovníky jsou někdy označovány jako glosáře pro nebo pojem základních tříd. Slovník můžete představit jako hrubou silou "Kopírovat a nahradit" pro všemi podmínkami v seznamu.

Slovníky fungovat pouze pro projekty v párech jazyka, které mají plně podporovaný systém Neurální strojový překlad (sítí NMT) společnosti Microsoft za nimi stojí. Zobrazit http://www.aka.ms/translatorlanguages úplný seznam jazyků.

## <a name="phrase-dictionary"></a>Fráze slovníku 
Při cvičení modelu zahrnete fráze slovníku, je přeložen libovolné slovo nebo frázi, které jsou uvedeny v způsob, jakým jste zadali. Zbývající věty je přeložen jako obvykle. Fráze slovníku můžete použít k určení frázích, které nemá být přeložen tím, že poskytuje stejnou nepřeloženém frázi v souboru zdroj a cíl ve slovníku.

## <a name="sentence-dictionary"></a>Slovník větu
Slovník věty můžete zadat přesný cíl překlad pro zdroj věty. Věty slovníku pro vyhledání shody dojít se musí shodovat celý odeslané věty zdroj položky slovníku.  Pokud pouze odpovídající část na konec věty, položka nebude odpovídat.  Když se zjistí shoda, vrátí se cílovou položkou slovníku věty.

## <a name="dictionary-only-trainings"></a>Slovník jen pro školení
Můžete trénování modelu s použitím pouze data slovníku. Provedete to tak, vyberte pouze slovníku dokument (nebo více dokumentů slovník), které chcete zahrnout a klepněte na tlačítko Vytvořit model. Protože jde čistě slovníku školení, neexistuje žádná minimální počet školení věty vyžaduje. Model se obvykle dokončí školení mnohem rychlejší než standardní školení.  Výsledný modely použije Microsoft základní modely pro překlad a uveďte slovníky, které jste přidali.  Nezískáte sestava testu.

>[!Note]
>Vlastní Translator nemá věty zarovnat soubory slovníku, proto je důležité, aby se stejný počet zdroj a cíl fráze/věty v dokumentech slovníku a že jsou přesně zarovnaná.

## <a name="recommendations"></a>Doporučení

- Slovníky nejsou náhradou za trénovaného modelu s trénovací data.  Slovníky sloučených v podstatě najít a nahradit slova nebo věty.  Umožněte systému, Učte se od vaše školicí materiály v celé věty je obecně vhodnější než použití slovníku. 
- Fráze slovníku měly používat střídmě. Když se nahradí frázi v rámci věty, kontextu v rámci této věty dojde ke ztrátě nebo omezené pro převod rest věty. Výsledkem je, že při fráze nebo slov v rámci věty přeloží podle fráze slovníku, často sníží celkovou kvalitu překladu věty.
- Fráze slovníku dobře funguje pro složená, jako jsou názvy produktů ("Microsoft SQL Server"), správné názvy ("City Hamburk") nebo funkce produktů ("kontingenční tabulky"). To nebude fungovat stejně dobře pro příkazy nebo přídavných jmen, protože ty jsou obvykle vysoce Gramatický tvar ve zdrojovém nebo cílovém jazyce. Vyhněte se položky fráze slovníku pro jakoukoliv složená. 
- Při použití slovníku, bude odrážet malá a velká písmena a interpunkce v překlady malá a velká písmena a interpunkční znaménka, které jsou k dispozici v cílovém souboru. Malá a velká písmena a interpunkční znaménka jsou ignorovány při pokusu o identifikaci shody mezi vstupní věty a věty zdroje v souboru slovníku. Řekněme například, že jsme školili anglické Španělština systému, který se používá slovník, že zadané "City Hamburk" ve zdrojovém souboru a "Ciudad de univerzitní nemocnice hamburg" v cílovém souboru. Pokud jsem si vyžádali překladu věty zahrnující frázi "city Hamburg", pak "city Hamburg" odpovídá souboru slovníku pro položku "City Hamburg" a by mapování na "Ciudad de univerzitní nemocnice hamburg" v mé konečný překlad.
- Pokud u slov velká uveden více než jednou v souboru slovníku, systém bude vždy používat poslední položky, které jsou k dispozici. Slovník, nesmí obsahovat více překlady téhož slova.

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o [pokyny v dokumentu formáty](document-formats-naming-convention.md).