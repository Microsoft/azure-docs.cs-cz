---
title: Co je slovník? – Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Slovník je zarovnaný dokument, který určuje seznam frází nebo vět (a jejich překlady), který má aplikace Microsoft Translator vždycky přeložit stejným způsobem. Slovníky se někdy také označují jako Glossaries nebo pojem základ.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 160aa372cec2d018d5a3558f99e079624d965098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895556"
---
# <a name="what-is-a-dictionary"></a>Co je slovník?

Slovník je zarovnaný pár dokumentů, které určují seznam frází nebo vět a jejich odpovídající překlady. Použijte slovník ve školicím programu, pokud chcete, aby Microsoft Translator vždy přeložil všechny instance zdrojové fráze nebo věty pomocí překladu, který jste zadali ve slovníku. Slovníky jsou někdy označovány jako Glossaries nebo pojem základ. Slovník si můžete představit jako hrubou silou "zkopírovat a nahradit" pro všechny uvedené výrazy. Kromě toho služba vlastní překladatele Microsoftu sestaví a využívá vlastní slovníky pro obecné účely ke zlepšení kvality jeho překladu. Slovník poskytnutý zákazníkem má ale stejný předchůdce a bude prohledán jako první hledaná slova nebo věty.

Slovníky fungují jenom pro projekty ve dvojicích jazyků, které mají plně podporovaný model Microsoft General neuronové Network. [Zobrazte úplný seznam jazyků](../language-support.md#customization).

## <a name="phrase-dictionary"></a>Slovník frází
Slovník frází rozlišuje velká a malá písmena. Jedná se o přesné operace Find a nahrazování. Když zahrnete Frázový slovník do školicího modelu, všechna slova nebo fráze uvedená v seznamu se budou přeložit způsobem, který jste zadali. Zbytek věty je přeložen běžným způsobem. Pomocí slovníku frází můžete určit fráze, které by se neměly překládat, zadáním stejné nepřeložené fráze ve zdrojovém a cílovém souboru ve slovníku.

## <a name="sentence-dictionary"></a>Slovník vět
Ve slovníku vět se nerozlišují malá a velká písmena. Slovník vět umožňuje zadat přesný cílový překlad pro zdrojovou větu. Aby došlo ke shodě slovníku vět, musí celá odeslaná věta odpovídat zdrojové položce slovníku. Pokud položka zdrojového slovníku končí interpunkcí, je ignorována během porovnávání. Pokud se shoduje jenom část věty, položka se neshoduje.  Při zjištění shody se vrátí cílová položka slovníku vět.

## <a name="dictionary-only-trainings"></a>Jenom kurzy pro slovník
Model můžete vytvořit pouze pomocí dat ze slovníku. Pokud to chcete provést, vyberte jenom dokument slovníku (nebo několik dokumentů slovníku), který chcete zahrnout, a klepněte na vytvořit model. Vzhledem k tomu, že se jedná o školení jenom pro slovník, není potřeba žádný minimální počet vět pro školení. Vaše modely obvykle dokončí školení mnohem rychleji než standardní školení.  Výsledné modely budou používat základní modely Microsoft pro překlad s přidáním slovníků, které jste přidali.  Nedostanete se k testovací sestavě.

>[!Note]
>Vlastní Překladatel nemění větu soubory slovníku, takže je důležité, aby se v dokumentech slovníku rovnal stejný počet zdrojových a cílových frází a vět a aby byly přesně zarovnané.

## <a name="recommendations"></a>Doporučení

- Slovníky nejsou náhradou za školení modelu pomocí školicích dat. Doporučuje se, abyste se vyhnuli a aby se systém dozvěděl od vašich školicích dat. Nicméně pokud je nutné vykreslit věty nebo složená podstatná jména, použijte slovník.
- Slovník frází by měl být používán zřídka. Počítejte s tím, že při nahrazení fráze ve větě je kontext v této větě ztracený nebo omezený pro překlad zbytku věty. Výsledkem je, že zatímco fráze nebo slovo v rámci věty budou přeloženy podle poskytnutého slovníku, bude celková kvalita překladu věty často zadarmo.
- Slovník frází dobře funguje pro složená podstatná jména, jako jsou názvy produktů ("Microsoft SQL Server"), správné názvy ("město Hamburg") nebo funkce produktu ("kontingenční tabulka"). Nefunguje stejně dobře pro příkazy nebo přídavné jména, protože jsou obvykle vysoce inflected ve zdroji nebo v cílovém jazyce. Osvědčenými postupy je vyhnout se záznamům slovníku frází pro cokoli, ale pro složená podstatná jména.
- Při použití slovníku frází jsou důležité kapitalizace a interpunkční znaménka. Položky slovníku budou odpovídat pouze slovům a frázím ve vstupní větě, které používají přesně stejnou velikost písmen a interpunkční znaménka, jak je uvedeno v souboru zdrojového slovníku. Také překlady budou odrážet velká a malá písmena, která jsou uvedena v souboru cílového slovníku. Například pokud jste si vyškole systém z angličtiny do španělštiny, který používá slovník frází, který určuje "US" ve zdrojovém souboru a "EE". UU. " v cílovém souboru. Když vyžádáte překlad věty, která obsahuje slovo "US" (bez velkých písmen), neshoduje se s slovníkem. Nicméně pokud požadujete překlad věty, která obsahuje slovo "US" (velkými písmeny), bude odpovídat slovníku a překlad by obsahoval "EE". UU. " Všimněte si, že velká a interpunkční znaménka v překladu se mohou lišit od zadání v souboru cílového slovníku a může se lišit od velkých a malých písmen ve zdroji. Řídí se pravidly cílového jazyka.
- Při použití slovníku vět se konec interpunkce věty ignoruje. Pokud váš zdrojový slovník obsahuje například "Tato věta končí interpunkčním znaménkem!", pak všechny požadavky na překlad obsahující "tuto větu končí interpunkcí" by odpovídaly.
- Pokud se slovo v souboru slovníku vyskytuje více než jednou, bude systém vždycky používat poslední poskytnutou položku. Proto by váš slovník neměl obsahovat více překladů stejného slova.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pokynech pro formáty dokumentů](document-formats-naming-convention.md).