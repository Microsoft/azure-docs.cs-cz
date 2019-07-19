---
title: Indexování s více jazyky pro vyhledávací dotazy jiné než anglické verze – Azure Search
description: Azure Search podporuje jazyky 56, což využívá analyzátory jazyka z aplikace Lucene a technologie pro zpracování přirozeného jazyka od Microsoftu.
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: 403febfcb54194602051aaebe2952265c0675e9d
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854390"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-search"></a>Vytvoření indexu pro více jazyků v Azure Search

Indexy mohou zahrnovat pole obsahující obsah z více jazyků, například vytváření jednotlivých polí pro řetězce specifické pro jazyk. Pro dosažení nejlepších výsledků při indexování a dotazování přiřaďte analyzátor jazyka, který poskytuje příslušná jazyková pravidla. 

Azure Search nabízí velký výběr jazykových analyzátorů z aplikace Lucene i Microsoftu, které je možné přiřadit k jednotlivým polím pomocí vlastnosti Analyzer. Můžete také zadat analyzátor jazyka na portálu, jak je popsáno v tomto článku.

## <a name="add-analyzers-to-fields"></a>Přidat analyzátory do polí

Analyzátor jazyka je určen při vytvoření pole. Přidáním analyzátoru do existující definice pole je třeba přepsat (a znovu načíst) index nebo vytvořit nové pole stejné jako původní, ale s přiřazením analyzátoru. Nepoužívané pole pak můžete odstranit na pohodlí.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyhledejte vyhledávací službu.
1. Kliknutím na **Přidat index** na panelu příkazů v horní části řídicího panelu služby spustíte nový index nebo otevřete existující index pro nastavení analyzátoru pro nová pole, která přidáváte do existujícího indexu.
1. Zahajte definici pole zadáním názvu.
1. Vyberte datový typ EDM. String. Pouze řetězcová pole jsou fulltextově prohledávatelné.
1. Nastavením atributu s **možností prohledávání** Povolte vlastnost Analyzer. Pole musí být založené na textu, aby bylo možné používat analyzátor jazyka.
1. Vyberte jeden z dostupných analyzátorů. 

![Přiřazovat analyzátory jazyka během definice pole](media/search-language-support/select-analyzer.png "Přiřazovat analyzátory jazyka během definice pole")

Ve výchozím nastavení používají všechna hledaná pole [standardní analyzátor Lucene](https://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) , který je Language-nezávislá. Úplný seznam podporovaných analyzátorů najdete v tématu [Přidání analyzátorů jazyka do indexu Azure Search](index-add-language-analyzers.md).

V portálu se analyzátory mají použít tak, jak jsou. Pokud budete vyžadovat přizpůsobení nebo konkrétní konfiguraci filtrů a tokenizátory musíte nejdřív, měli byste v kódu [vytvořit vlastní analyzátor](index-add-custom-analyzers.md) . Portál nepodporuje výběr a konfiguraci vlastních analyzátorů.

## <a name="query-language-specific-fields"></a>Konkrétní dotazovací jazyk – pole

Jakmile je analyzátor jazyka vybrán pro pole, bude použit spolu s každou vydanou žádostí o indexování a hledání daného pole. Když je dotaz vydán pro více polí pomocí různých analyzátorů, dotaz se zpracuje nezávisle přiřazenými analyzátory pro každé pole.

Pokud je znám jazyk agenta, který vydává dotaz, může být požadavek na hledání vymezen na konkrétní pole pomocí parametru dotazu **searchFields** . Následující dotaz se vydá jenom s popisem v polštině:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Dotaz na index můžete z portálu pomocí [**Průzkumníka služby Search**](search-explorer.md) vložit do dotazu, který se podobá výše uvedenému.

## <a name="boost-language-specific-fields"></a>Posílit pole specifická pro konkrétní jazyk

Někdy není známý jazyk agenta, který vydává dotaz, a v takovém případě lze dotaz vydat pro všechna pole současně. V případě potřeby je možné pomocí [profilů vyhodnocování](index-add-scoring-profiles.md)definovat preference pro výsledky v určitém jazyce. V následujícím příkladu se shody zjištěné v popisu v angličtině budou v porovnání s výsledky v polštině a francouzštině vyhodnoceny jako vyšší:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Další postup

Pokud jste vývojářem rozhraní .NET, Všimněte si, že můžete nakonfigurovat jazykové analyzátory pomocí [sady Azure Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) a vlastnosti [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) . 