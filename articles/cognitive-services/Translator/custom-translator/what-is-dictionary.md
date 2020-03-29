---
title: Co je slovník? - Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Slovník je zarovnaný dokument, který určuje seznam frází nebo vět (a jejich překladů), které vždy chcete, aby aplikace Microsoft Translator překlápěla stejným způsobem. Slovníky se někdy také nazývají glosáře nebo termínové základy.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 36b449c4c4ca30eb658c9519ce8e870a4f1fab32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71970727"
---
# <a name="what-is-a-dictionary"></a>Co je slovník?

Slovník je zarovnaná dvojice dokumentů, která určuje seznam frází nebo vět a jejich odpovídající překlady. Pokud chcete, aby microsoft translator vždy překládal všechny instance zdrojové fráze nebo věty pomocí překladu, který jste zadali ve slovníku, použijte slovník, který jste zadali. Slovníky se někdy nazývají glosáře nebo termínové základy. Slovník si můžete myslet jako hrubou sílu "kopírovat a nahrazovat" pro všechny termíny, které uvádíte. Služba Microsoft Custom Translator navíc vytváří a využívá své vlastní slovníky pro obecné účely ke zlepšení kvality svého překladu. Zákazník však za předpokladu, slovník má precedens a bude prohledán jako první vyhledat slova nebo věty.

Slovníky fungují pouze pro projekty v párech jazyků, které mají plně podporovaný model obecné neuronové sítě společnosti Microsoft za nimi. [Prohlédněte si úplný seznam jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Slovník frází
Pokud do trénování modelu zahrnete slovník frází, každé uvedené slovo nebo fráze se přeloží způsobem, který jste zadali. Zbytek věty je přeložen jako obvykle. Slovník frází můžete použít k určení frází, které by neměly být přeloženy, poskytnutím stejné nepřeložené fráze ve zdrojovém a cílovém souboru ve slovníku.

## <a name="sentence-dictionary"></a>Větný slovník
Slovník vět umožňuje zadat přesný cílový překlad zdrojové věty. Aby došlo ke shodě slovníku věty, musí se celá odeslaná věta shodovat se položkou zdrojového slovníku.  Pokud se shoduje pouze část věty, položka se neshoduje.  Po zjištění shody bude vrácena cílová položka slovníku vět.

## <a name="dictionary-only-trainings"></a>Školení pouze pro slovník
Model můžete trénovat pouze pomocí dat slovníku. Chcete-li to provést, vyberte pouze slovníkový dokument (nebo více dokumentů slovníku), který chcete zahrnout, a klepněte na Vytvořit model. Vzhledem k tomu, že se jedná o školení pouze pro slovník, není vyžadován žádný minimální počet výcvikových trestů. Váš model obvykle dokončí školení mnohem rychleji než standardní školení.  Výsledné modely budou používat základní modely společnosti Microsoft pro překlad s přidáním slovníků, které jste přidali.  Nedostanete zkušební protokol.

>[!Note]
>Vlastní překladač nezarovná soubory slovníku, takže je důležité, aby ve vašich dokumentech slovníku byl stejný počet zdrojových a cílových frází/vět a aby byly přesně zarovnány.

## <a name="recommendations"></a>Doporučení

- Slovníky nejsou náhradou za trénování modelu pomocí trénovacích dat. Doporučuje se, aby se jim vyhnout a nechat systém učit se z vašich trénovacích dat. Pokud však musí být věty nebo složená nosná slova vykreslena tak, jak jsou, použijte slovník.
- Slovník frází by měl být používán střídmě. Takže mějte na paměti, že když je fráze ve větě nahrazena, kontext v rámci této věty je ztracen nebo omezen pro překlad zbytku věty. Výsledkem je, že zatímco fráze nebo slovo ve větě se přeloží podle poskytnutého slovníku, celková kvalita překladu věty často trpí.
- Slovník frází funguje dobře pro složená vlastní jména, jako jsou názvy produktů ("Microsoft SQL Server"), vlastní názvy ("město Hamburk") nebo funkce produktu ("kontingenční tabulka"). Nefunguje stejně dobře pro slovesa nebo přídavná jména, protože tyto jsou obvykle vysoce skloňovány ve zdroji nebo v cílovém jazyce. Osvědčeným postupem je vyhnout se frázové slovníkové položky pro cokolijiné, ale složené prakticky.
- Při použití slovníku frází jsou důležitá velká písmena a interpunkce. Položky slovníku budou shodovat pouze slova a fráze ve vstupní větě, které používají přesně stejné velká písmena a interpunkci, jak je uvedeno ve zdrojovém slovníku. Také překlady budou odrážet velká písmena a interpunkce uvedené v cílovém souboru slovníku. Například pokud jste trénovali systém angličtiny na španělštinu, který používá slovník frází, který určuje "USA" ve zdrojovém souboru a "EE. UU." v cílovém souboru. Pokud požadujete překlad věty, která obsahuje slovo "nás" (není velkými písmeny), nebude to odpovídat slovníku. Pokud však požadujete překlad věty, která obsahuje slovo "USA" (velkými písmeny), pak by odpovídalslovníku a překlad by obsahoval "EE. UU." Všimněte si, že velká písmena a interpunkce v překladu se mohou lišit od zadané v cílovém souboru slovníku a mohou se lišit od velkých písmen a interpunkce ve zdroji. Řídí se pravidly cílového jazyka.
- Pokud se slovo v souboru slovníku zobrazí více než jednou, systém vždy použije poslední zadaný záznam. Proto váš slovník by neměl obsahovat více překladů stejného slova.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [pokyny týkající se formátů dokumentů](document-formats-naming-convention.md).
