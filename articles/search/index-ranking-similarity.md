---
title: Algoritmus podobnosti pořadí
titleSuffix: Azure Cognitive Search
description: Jak nastavit algoritmus podobnosti vyzkoušet nový algoritmus podobnosti pro pořadí
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409970"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Algoritmus hodnocení v Azure Cognitive Search

> [!IMPORTANT]
> července 2020 budou nově vytvořené vyhledávací služby využívat funkci hodnocení BM25, která se ve většině případů ukázala jako hodnocení vyhledávání, která lépe odpovídá očekáváním uživatelů než současné výchozí hodnocení.  Kromě vynikajícího hodnocení umožňuje BM25 také možnosti konfigurace pro ladění výsledků na základě faktorů, jako je velikost dokumentu.  
>
> S touto změnou se s největší pravděpodobností zobrazí mírné změny v pořadí výsledků vyhledávání.   Pro ty, kteří chtějí otestovat dopad této změny, jsme zpřístupnili v rozhraní API 2019-05-06-Preview možnost povolit vyhodnocování BM25 na nové indexy.  

Tento článek popisuje, jak můžete aktualizovat službu vytvořenou před 15.

Azure Cognitive Search bude používat oficiální Lucene implementace algoritmu Okapi BM25, *BM25 Podobnost*, který nahradí dříve používané *ClassicSimilarity* implementace. Stejně jako starší algoritmus ClassicSimilarity je BM25Similarity funkce načítání podobná TF-IDF, která používá termín frekvence (TF) a inverzní frekvenci dokumentu (IDF) jako proměnné pro výpočet skóre relevance pro každou dvojici dotazů dokumentu, která je pak používá pro hodnocení. Zatímco koncepčně podobný starší algoritmus classic podobnosti, BM25 má kořen v pravděpodobnostní načítání informací zlepšit na to. BM25 také nabízí pokročilé možnosti přizpůsobení, jako je například umožňuje uživateli rozhodnout, jak relevance skóre měřítko s termínem frekvence spárovaných termínů.

## <a name="how-to-test-bm25-today"></a>Jak testovat BM25 dnes

Při vytváření nového indexu můžete nastavit vlastnost "podobnosti". Budete muset použít verzi *2019-05-06-Preview,* jak je znázorněno níže.

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

Pro služby vytvořené před 15. červencem 2020: Pokud je podobnost vynechána nebo nastavena na hodnotu null, index použije algoritmus staré klasické podobnosti.

Pro služby vytvořené po 15.

Můžete také explicitně nastavit hodnotu podobnosti jako jednu z následujících dvou hodnot: *"#Microsoft.Azure.Search.ClassicSimilarity"* nebo *"#Microsoft.Azure.Search.BM25Similarity"*.


## <a name="bm25-similarity-parameters"></a>BM25 parametry podobnosti

Podobnost BM25 přidává dva uživatele přizpůsobitelné parametry pro kontrolu vypočteného skóre relevance:

### <a name="k1"></a>k1

Parametr *k1* řídí funkci měřítka mezi četností termínů jednotlivých odpovídajících termínů a konečným skóre relevance dvojice document-query.

Hodnota nula představuje "binární model", kde je příspěvek jednoho odpovídajícího termínu stejný pro všechny odpovídající dokumenty, bez ohledu na to, kolikrát se tento termín objeví v textu, zatímco větší hodnota k1 umožňuje, aby se skóre nadále zvyšovalo jako více instance stejného termínu je v dokumentu. Ve výchozím nastavení Azure Cognitive Search používá hodnotu 1.2 pro parametr k1. Použití vyšší hodnoty k1 může být důležité v případech, kdy očekáváme, že součástí vyhledávacího dotazu bude více termínů. V těchto případech můžeme chtít upřednostnit dokumenty, které odpovídají mnoha různým dotazům prohledávaných přes dokumenty, které se shodují pouze s jedním, vícekrát. Například při dotazování na index pro dokumenty obsahující termíny "Apollo Spaceflight", můžeme chtít snížit skóre článku o řecké mytologie, který obsahuje termín "Apollo" několik desítek krát, bez zmínky o "Spaceflight", ve srovnání s další článek, který výslovně zmiňuje jak "Apollo" a "Spaceflight" několikrát jen. 
 
### <a name="b"></a>b

Parametr *b* určuje, jak délka dokumentu ovlivňuje skóre relevance.

Hodnota 0,0 znamená, že délka dokumentu nebude mít vliv na skóre, zatímco hodnota 1,0 znamená, že dopad četnosti termínů na skóre relevance bude normalizován délkou dokumentu. Výchozí hodnota použitá v Azure Cognitive Search pro parametr b je 0,75. Normalizace četnosti termínů podle délky dokumentu je užitečná v případech, kdy chceme penalizovat delší dokumenty. V některých případech je pravděpodobnější, že delší dokumenty (například úplný román) budou obsahovat mnoho irelevantních termínů ve srovnání s mnohem kratšími dokumenty.

### <a name="setting-k1-and-b-parameters"></a>Nastavení parametrů k1 a b

Chcete-li přizpůsobit hodnoty b nebo k1, jednoduše je přidejte jako vlastnosti k objektu podobnosti při použití BM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

Algoritmus podobnosti lze nastavit pouze v době vytvoření indexu. To znamená, že algoritmus podobnosti, který se používá, nelze změnit pro existující indexy. Parametry *"b"* a *"k1"* lze upravit při aktualizaci existující definice indexu, která používá BM25. Změna těchto hodnot na existující index přenese index do offline po dobu nejméně několika sekund, což způsobí selhání indexování a požadavky na dotazy. Z tohoto důvodu budete muset nastavit parametr "allowIndexDowntime=true" v řetězci dotazu vašeho požadavku na aktualizaci:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>Viz také  

 [Azure Kognitivní vyhledávání REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Přidání profilů hodnocení do indexu](index-add-scoring-profiles.md)    
 [Vytvoření indexu &#40;rozhraní REST API pro kognitivní vyhledávání Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
