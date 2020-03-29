---
title: Sladění vzorů a speciálních znaků
titleSuffix: Azure Cognitive Search
description: Pomocí dotazů se zástupnými kódy a předčíslími se shodují na celé nebo částečné termíny v žádosti o dotaz Azure Cognitive Search. Těžko shodovat vzory, které obsahují speciální znaky lze vyřešit pomocí syntaxe úplného dotazu a vlastní analyzátory.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: f78ba5b351a3da46d7b8b3780cf00772c4f3b2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289307"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Shoda na vzorech a speciálních znacích (pomlčky)

Pro dotazy, které obsahují`-, *, (, ), /, \, =`speciální znaky ( ), nebo pro vzorky dotazů založené na částečných termínech v rámci většího termínu jsou obvykle potřeba další kroky konfigurace, aby bylo zajištěno, že index obsahuje očekávaný obsah ve správném formátu. 

Ve výchozím nastavení je `+1 (425) 703-6214` telefonní číslo `"1"` `"425"`tokenizováno jako , , `"703"`. `"6214"` Jak si dokážete `"3-62"`představit, hledání na , částečné termíny, které obsahují pomlčka, se nezdaří, protože tento obsah ve skutečnosti neexistuje v indexu. 

Pokud potřebujete hledat na částečných řetězcích nebo speciálních znacích, můžete přepsat výchozí analyzátor vlastním analyzátorem, který pracuje podle jednodušších pravidel tokenizace, zachování celých termínů, které jsou nezbytné, pokud řetězce dotazu obsahují části termínu nebo speciální Znaky. Vezmeme-li krok zpět, přístup vypadá takto:

+ Zvolte předdefinovaný analyzátor nebo definujte vlastní analyzátor, který vytváří požadovaný výstup
+ Přiřazení analyzátoru k poli
+ Sestavení indexu a testování

Tento článek vás provede těmito úkoly. Zde popsaný přístup je užitečný v jiných scénářích: dotazy se zástupnými znaky a regulárními výrazy také potřebují celé termíny jako základ pro porovnávání vzorů. 

> [!TIP]
> Vyhodnocení analyerů je iterativní proces, který vyžaduje časté obnovení indexu. Tento krok můžete usnadnit pomocí Postman, REST API pro [vytvoření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index), [Odstranit index](https://docs.microsoft.com/rest/api/searchservice/delete-index),[Načíst dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)a Hledat [dokumenty](https://docs.microsoft.com/rest/api/searchservice/search-documents). U načíst dokumenty by měl text požadavku obsahovat malou reprezentativní datovou sadu, kterou chcete otestovat (například pole s telefonními čísly nebo kódy produktů). S těmito api ve stejné kolekci Postman, můžete cykonoběh ovat tyto kroky rychle.

## <a name="choosing-an-analyzer"></a>Výběr analyzátoru

Při výběru analyzátoru, který vytváří tokeny po celou dobu, jsou běžné volby následující analyzátory:

| Analyzer | Chování |
|----------|-----------|
| [klíčové slovo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Obsah celého pole je tokenizován jako jeden termín. |
| [Mezery](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Odděluje pouze na prázdné matné znaky. Termíny, které obsahují pomlčky nebo jiné znaky jsou považovány za jeden token. |
| [vlastní analyzátor](index-add-custom-analyzers.md) | (doporučeno) Vytvoření vlastního analyzátoru umožňuje zadat tokenizer a token filtr. Předchozí analyzátory musí být použity tak, jak jsou. Vlastní analyzátor umožňuje vybrat, které tokenizers a token filtry použít. <br><br>Doporučená kombinace je [tokenizer klíčového slova](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) s [filtrem tokenů malých písmen](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Předdefinovaný [analyzátor klíčových slov](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) sám o sobě nepředstavuje malá písmena žádného textu s velkými písmeny, což může způsobit selhání dotazů. Vlastní analyzátor poskytuje mechanismus pro přidání filtru tokenu malých písmen. |

Pokud používáte testovací nástroj webového rozhraní API, jako je Postman, můžete přidat [volání REST analyzátoru testu](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) ke kontrole tokenizovaného výstupu. Vzhledem k existující index a pole obsahující pomlčky nebo částečné termíny, můžete vyzkoušet různé analyzátory přes konkrétní termíny zjistit, jaké tokeny jsou vydávány.  

1. Zkontrolujte standardní analyzátor a zjistěte, jak jsou ve výchozím nastavení tokenizovány termíny.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Vyhodnoťte odpověď a podívejte se, jak je text tokenizován v rámci indexu. Všimněte si, jak každý termín je malá písmena a rozdělena.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Upravte požadavek `whitespace` na `keyword` použití analyzátoru nebo:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Nyní odpověď se skládá z jednoho tokenu, velká písmena, s pomlčky zachovány jako součást řetězce. Pokud potřebujete hledat na vzor nebo částečný termín, dotazovací stroj má nyní základ pro nalezení shody.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Uvědomte si, že analyzátory dotazů často malá písmena ve výrazu hledání při vytváření stromu dotazů. Pokud používáte analyzátor, který nemá vstupy textu s nižšími písmeny a nedostáváte očekávané výsledky, může to být důvod. Řešením je přidání filtru tokenů lwower-case.

## <a name="analyzer-definitions"></a>Definice analyzátoru
 
Bez ohledu na to, zda vyhodnocujete analyzátor nebo se posouváte vpřed s určitou konfigurací, budete muset určit analyzátor v definici pole a případně nakonfigurovat samotný analyzátor, pokud nepoužíváte vestavěný analyzátor. Při výměně analyzátory, obvykle je třeba znovu vytvořit index (přetažení, znovu vytvořit a znovu načíst). 

### <a name="use-built-in-analyzers"></a>Použití vestavěných analyzátorů

Vestavěné nebo předdefinované analyzátory lze `analyzer` zadat podle názvu na vlastnost definice pole, bez další konfigurace požadované v indexu. Následující příklad ukazuje, jak byste `whitespace` nastavili analyzátor v poli.

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```
Další informace o všech dostupných vestavěných analyzátorech naleznete v [seznamu předdefinovaných analyzátorů](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Použití vlastních analyzátorů

Pokud používáte [vlastní analyzátor](index-add-custom-analyzers.md), definujte jej v indexu s uživatelem definovanou kombinací tokenizeru, tokenfilteru s možným nastavením konfigurace. Dále odkaz na definici pole, stejně jako byste vestavěný analyzátor.

Pokud je cílem tokenizace celého období, doporučuje se vlastní analyzátor, který se skládá z **tokenizeru klíčového slova** a **filtru malých tokenů.**

+ Tokenizer klíčového slova vytvoří jeden token pro celý obsah pole.
+ Filtr malých písmen tokenu transformuje velká písmena na text s velkými písmeny. Analyzátory dotazů obvykle malá písmena jakékoli vstupy textu velkých písmen. Snížení homogenizuje vstupy s tokenizované podmínky.

Následující příklad ilustruje vlastní analyzátor, který poskytuje tokenizer klíčového slova a filtr tokenů s malou písmena.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> Tokenizer `keyword_v2` a `lowercase` token filtr jsou známy systému a pomocí jejich výchozí konfigurace, což je důvod, proč můžete odkazovat podle názvu, aniž by bylo třeba je definovat jako první.

## <a name="tips-and-best-practices"></a>Tipy a osvědčené postupy

### <a name="tune-query-performance"></a>Ladění výkonu dotazů

Pokud implementujete doporučenou konfiguraci, která zahrnuje keyword_v2 tokenizer a filtr tokenů malých písmen, můžete zaznamenat snížení výkonu dotazu z důvodu zpracování filtru dalších tokenů přes existující tokeny v indexu. 

Následující příklad přidá [EdgeNGramTokenFilter,](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) aby se rychleji shody předpony. Další tokeny jsou generovány pro v kombinacích 2-25 znaků, které obsahují znaky: (nejen MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). Jak si dokážete představit, další tokenizace má za následek větší index.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Použití různých analyzátorů pro indexování a zpracování dotazů

Analyzátory jsou volány během indexování a během provádění dotazu. Je běžné používat stejný analyzátor pro oba, ale můžete nakonfigurovat vlastní analyzátory pro každou úlohu. Přepsání analyzátoru jsou zadána `analyzers` v [definici indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) v oddílu a poté odkazována na konkrétní pole. 

Pokud je vlastní analýza vyžadována pouze během indexování, můžete použít vlastní analyzátor pouze indexování a nadále používat standardní analyzátor Lucene (nebo jiný analyzátor) pro dotazy.

Chcete-li určit analýzu specifickou pro roli, můžete `indexAnalyzer` nastavit `searchAnalyzer` vlastnosti `analyzer` pole pro každou z nich nastavení a místo výchozí vlastnosti.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>Duplicitní pole pro různé scénáře

Další možností využívá přiřazení analyzátoru pro pole k optimalizaci pro různé scénáře. Konkrétně můžete definovat "featureCode" a "featureCodeRegex" pro podporu pravidelné fulltextové vyhledávání na první a pokročilé porovnávání vzorů na druhé.

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>Další kroky

Tento článek vysvětluje, jak analyzátory přispívají k problémům s dotazy a řeší problémy s dotazy. Jako další krok se blíže podívejte na dopad analyzátoru na indexování a zpracování dotazů. Zejména zvažte použití analyzovat textové rozhraní API vrátit tokenizovaný výstup, takže můžete přesně vidět, co analyzátor vytváří pro váš index.

+ [Analyzátory jazyka](search-language-support.md)
+ [Analyzátory pro zpracování textu v Azure Cognitive Search](search-analyzers.md)
+ [Analyzovat textové rozhraní API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Jak funguje fulltextové vyhledávání (architektura dotazů)](search-lucene-query-architecture.md)
