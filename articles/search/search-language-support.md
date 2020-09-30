---
title: Indexování ve více jazycích pro jiné než anglické vyhledávací dotazy
titleSuffix: Azure Cognitive Search
description: Azure Kognitivní hledání podporuje jazyky 56, které využívají analyzátory jazyka z aplikace Lucene a technologie pro zpracování přirozeného jazyka od Microsoftu.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: a121751e71bffdb76341f6a7dc2a01a22240019b
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534464"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Vytvoření indexu pro více jazyků v Azure Kognitivní hledání

Indexy mohou zahrnovat pole obsahující obsah z více jazyků, například vytváření jednotlivých polí pro řetězce specifické pro jazyk. Pro dosažení nejlepších výsledků při indexování a dotazování přiřaďte analyzátor jazyka, který poskytuje příslušná jazyková pravidla. 

Azure Kognitivní hledání nabízí velký výběr jazykových analyzátorů z aplikací Lucene a Microsoft, které je možné přiřadit k jednotlivým polím pomocí vlastnosti Analyzer. Můžete také zadat analyzátor jazyka na portálu, jak je popsáno v tomto článku.

## <a name="add-analyzers-to-fields"></a>Přidat analyzátory do polí

Analyzátor jazyka je určen při vytvoření pole. Přidáním analyzátoru do existující definice pole je třeba přepsat (a znovu načíst) index nebo vytvořit nové pole stejné jako původní, ale s přiřazením analyzátoru. Nepoužívané pole pak můžete odstranit na pohodlí.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyhledejte vyhledávací službu.
1. Kliknutím na **Přidat index** na panelu příkazů v horní části řídicího panelu služby spustíte nový index nebo otevřete existující index pro nastavení analyzátoru pro nová pole, která přidáváte do existujícího indexu.
1. Zahajte definici pole zadáním názvu.
1. Vyberte datový typ EDM. String. Pouze řetězcová pole jsou fulltextově prohledávatelné.
1. Nastavením atributu s **možností prohledávání** Povolte vlastnost Analyzer. Pole musí být založené na textu, aby bylo možné používat analyzátor jazyka.
1. Vyberte jeden z dostupných analyzátorů. 

![Přiřazovat analyzátory jazyka během definice pole](media/search-language-support/select-analyzer.png "Přiřazovat analyzátory jazyka během definice pole")

Ve výchozím nastavení používají všechna hledaná pole [standardní analyzátor Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) , který je Language-nezávislá. Pokud chcete zobrazit úplný seznam podporovaných analyzátorů, přečtěte si téma [Přidání analyzátorů jazyka do indexu služby Azure kognitivní hledání](index-add-language-analyzers.md).

V portálu se analyzátory mají použít tak, jak jsou. Pokud budete vyžadovat přizpůsobení nebo konkrétní konfiguraci filtrů a tokenizátory musíte nejdřív, měli byste v kódu [vytvořit vlastní analyzátor](index-add-custom-analyzers.md) . Portál nepodporuje výběr a konfiguraci vlastních analyzátorů.

## <a name="query-language-specific-fields"></a>Konkrétní dotazovací jazyk – pole

Jakmile je analyzátor jazyka vybrán pro pole, bude použit spolu s každou vydanou žádostí o indexování a hledání daného pole. Když je dotaz vydán pro více polí pomocí různých analyzátorů, dotaz se zpracuje nezávisle přiřazenými analyzátory pro každé pole.

Pokud je znám jazyk agenta, který vydává dotaz, může být požadavek na hledání vymezen na konkrétní pole pomocí parametru dotazu **searchFields** . Následující dotaz se vydá jenom s popisem v polštině:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2020-06-30`

Dotaz na index můžete z portálu pomocí [**Průzkumníka služby Search**](search-explorer.md) vložit do dotazu, který se podobá výše uvedenému.

## <a name="boost-language-specific-fields"></a>Posílit pole specifická pro konkrétní jazyk

Někdy není známý jazyk agenta, který vydává dotaz, a v takovém případě lze dotaz vydat pro všechna pole současně. V případě potřeby je možné pomocí [profilů vyhodnocování](index-add-scoring-profiles.md)definovat preference pro výsledky v určitém jazyce. V následujícím příkladu se shody zjištěné v popisu v angličtině budou v porovnání s výsledky v polštině a francouzštině vyhodnoceny jako vyšší:

```http
    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]
```

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2020-06-30`

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem rozhraní .NET, Všimněte si, že můžete nakonfigurovat jazykové analyzátory pomocí [sady Azure kognitivní hledání .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) a vlastnosti [Analyzer](/dotnet/api/microsoft.azure.search.models.analyzer) .