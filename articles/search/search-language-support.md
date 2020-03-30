---
title: Vícejazyčné indexování pro neanglické vyhledávací dotazy
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search podporuje 56 jazyků a využívá analyzátory jazyků od společnosti Lucene a technologie zpracování přirozeného jazyka od Microsoftu.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793592"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Jak vytvořit index pro více jazyků v Azure Cognitive Search

Indexy mohou obsahovat pole obsahující obsah z více jazyků, například vytváření jednotlivých polí pro řetězce specifické pro jazyk. Nejlepších výsledků během indexování a dotazování dosáhnete, pokud chcete, přiřaďte analyzátor jazyka, který poskytuje příslušná jazyková pravidla. 

Azure Cognitive Search nabízí velký výběr analyzátorů jazyků od Lucene i Microsoftu, které lze přiřadit k jednotlivým polím pomocí vlastnosti Analyzer. Můžete také zadat analyzátor jazyka na portálu, jak je popsáno v tomto článku.

## <a name="add-analyzers-to-fields"></a>Přidání analyzátorů do polí

Při vytvoření pole je určen analyzátor jazyka. Přidání analyzátoru do existující definice pole vyžaduje přepsání (a opětovné načtení) indexu nebo vytvoření nového pole identického s originálem, ale s přiřazením analyzátoru. Nevyužité pole pak můžete podle potřeby odstranit.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a najděte svou vyhledávací službu.
1. Kliknutím na **Přidat rejstřík** v panelu příkazů v horní části řídicího panelu služby spusťte nový index nebo otevřete existující index a nastavte analyzátor na nová pole, která přidáváte do existujícího indexu.
1. Začněte definici pole zadáním názvu.
1. Zvolte datový typ Edm.String. Fulltextové prohledávatelné lze pouze pole řetězce.
1. Nastavte atribut **Prohledávatelné** povolit vlastnost Analyzer. Pole musí být založeno na textu, aby bylo možné použít analyzátor jazyka.
1. Vyberte si jeden z dostupných analyzátorů. 

![Přiřazení analyzátorů jazyků během definice pole](media/search-language-support/select-analyzer.png "Přiřazení analyzátorů jazyků během definice pole")

Ve výchozím nastavení používají všechna prohledávatelná pole [analyzátor Standard Lucene,](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) který je jazykově nezávislá. Úplný seznam podporovaných analyzátorů najdete v tématu [Přidání analyzátorů jazyka do indexu Azure Cognitive Search](index-add-language-analyzers.md).

Na portálu jsou analyzátory určeny k použití tak, jak jsou. Pokud požadujete vlastní nastavení nebo určitou konfiguraci filtrů a tokenizerů, měli byste [vytvořit vlastní analyzátor](index-add-custom-analyzers.md) v kódu. Portál nepodporuje výběr nebo konfiguraci vlastních analyzátorů.

## <a name="query-language-specific-fields"></a>Pole specifická pro jazyk dotazu

Jakmile je pro pole vybrán analyzátor jazyka, bude použit s každým indexováním a požadavkem na vyhledávání pro toto pole. Pokud je dotaz vydán proti více polím používajícím různé analyzátory, bude dotaz zpracován nezávisle přiřazenými analyzátory pro každé pole.

Pokud je znám jazyk agenta vydávajícího dotaz, může být požadavek hledání vymezen na určité pole pomocí parametru dotazu **searchFields.** Následující dotaz bude vydán pouze proti popisu v polštině:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Můžete dotaz ovat index z portálu, pomocí [**Průzkumníka vyhledávání**](search-explorer.md) vložit do dotazu podobného tomu, který je uveden výše.

## <a name="boost-language-specific-fields"></a>Zvýšení polí specifických pro daný jazyk

Někdy jazyk agenta vydávajícího dotaz není znám, v takovém případě může být dotaz vydán proti všem polím současně. V případě potřeby lze pomocí [profilů hodnocení](index-add-scoring-profiles.md)definovat předvolbu výsledků v určitém jazyce . V níže uvedeném příkladu budou shody nalezené v popisu v angličtině hodnoceny vyšší vzhledem k zápasům v polštině a francouzštině:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Další kroky

Pokud jste vývojář rozhraní .NET, všimněte si, že můžete nakonfigurovat analyzátory jazyků pomocí [Azure Cognitive Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) a [vlastnost Analyzátor.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) 