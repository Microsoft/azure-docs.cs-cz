---
title: Co je slovník? – Vlastní Translator
titleSuffix: Azure Cognitive Services
description: Slovník je zarovnaný dokument, který určuje seznam frází nebo vět (a jejich překlady), který má aplikace Microsoft Translator vždycky přeložit stejným způsobem. Slovníky se někdy také označují jako Glossaries nebo pojem základ.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5103526956b5041771a1d8e4abb5e8800b971059
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595382"
---
# <a name="what-is-a-dictionary"></a>Co je slovník?

Slovník je zarovnaný pár dokumentů, které určují seznam frází nebo vět a jejich odpovídající překlady. Použijte slovník ve školicím programu, pokud chcete, aby Microsoft Translator vždy přeložil všechny instance zdrojové fráze nebo věty pomocí překladu, který jste zadali ve slovníku. Slovníky jsou někdy označovány jako Glossaries nebo pojem základ. Slovník si můžete představit jako hrubou silou "zkopírovat a nahradit" pro všechny uvedené výrazy.

Slovníky fungují jenom pro projekty ve dvojicích jazyků, které mají plně podporovaný systém Microsoft neuronové Machine Translation (NMT). [Zobrazte úplný seznam jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Slovník frází
Když zahrnete Frázový slovník do školicího modelu, všechna slova nebo fráze uvedená v seznamu se budou přeložit způsobem, který jste zadali. Zbytek věty je přeložen běžným způsobem. Pomocí slovníku frází můžete určit fráze, které by se neměly překládat, zadáním stejné nepřeložené fráze ve zdrojovém a cílovém souboru ve slovníku.

## <a name="sentence-dictionary"></a>Slovník vět
Slovník vět umožňuje zadat přesný cílový překlad pro zdrojovou větu. Aby došlo ke shodě slovníku vět, musí celá odeslaná věta odpovídat zdrojové položce slovníku.  Pokud se shoduje jenom část věty, položka se neshoduje.  Při zjištění shody se vrátí cílová položka slovníku vět.

## <a name="dictionary-only-trainings"></a>Jenom kurzy pro slovník
Model můžete vytvořit pouze pomocí dat ze slovníku. Pokud to chcete provést, vyberte jenom dokument slovníku (nebo několik dokumentů slovníku), který chcete zahrnout, a klepněte na vytvořit model. Vzhledem k tomu, že se jedná o školení jenom pro slovník, není potřeba žádný minimální počet vět pro školení. Vaše modely obvykle dokončí školení mnohem rychleji než standardní školení.  Výsledné modely budou používat základní modely Microsoft pro překlad s přidáním slovníků, které jste přidali.  Nedostanete se k testovací sestavě.

>[!Note]
>Vlastní Překladatel nemění větu soubory slovníku, takže je důležité, aby se v dokumentech slovníku rovnal stejný počet zdrojových a cílových frází a vět a aby byly přesně zarovnané.

## <a name="recommendations"></a>Doporučení

- Slovníky nejsou náhradou za školený model se školicími daty.  Slovníky v podstatě hledají a nahrazují slova nebo věty.  Díky tomu, aby se systém seznámil z školicího materiálu v plných větách, je všeobecně lepší volbou než použití slovníku.
- Slovník frází by měl být používán zřídka. Při nahrazení fráze ve větě je kontext v této větě ztracený nebo omezený pro překlad zbytku věty. Výsledkem je, že zatímco fráze nebo Word ve větě budou přeloženy podle slovníku frází, bude celková kvalita překladu ve větě často poškozena.
- Slovník frází dobře funguje pro složená podstatná jména, jako jsou názvy produktů ("Microsoft SQL Server"), správné názvy ("město Hamburg") nebo funkce produktu ("kontingenční tabulka"). Nefunguje stejně dobře pro příkazy nebo přídavné jména, protože jsou obvykle vysoce inflected ve zdroji nebo v cílovém jazyce. Nepoužívejte položky slovníku frází pro cokoli, ale pro složená podstatná jména.
- Při použití slovníku jsou velká a interpunkční znaménka v rámci vašich překladů odrážet velká a malá písmena, která jsou k dispozici v cílovém souboru. Při pokusu o identifikaci shody mezi vstupní větou a zdrojovými větami v souboru slovníku jsou malá a velká písmena a interpunkční znaménka ignorována. Řekněme například, že jsme provedli angličtinu k španělštině systému, který použil slovník, který ve zdrojovém souboru určil město Hamburg, a v cílovém souboru "Ciudad de Hamburg". Pokud jsem požadoval překlad věty, která obsahovala frázi "City of Hamburg", pak se "město Hamburg" shoduje se souborem slovníku pro položku "City of Hamburg" a má v konečném překladu mapovat na "Ciudad de Hamburg".
- Pokud se slovo v souboru slovníku vyskytuje více než jednou, bude systém vždycky používat poslední poskytnutou položku. Slovník by neměl obsahovat více překladů stejného slova.

## <a name="next-steps"></a>Další postup

- Přečtěte si o [pokynech pro formáty dokumentů](document-formats-naming-convention.md).
