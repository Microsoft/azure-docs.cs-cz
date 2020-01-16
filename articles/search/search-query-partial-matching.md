---
title: Porovnávání vzorů a speciálních znaků
titleSuffix: Azure Cognitive Search
description: Použijte zástupné znaky a dotazy předpony, které se budou shodovat s celými nebo částečnými výrazy v žádosti o dotaz na Azure Kognitivní hledání Vzorce, které obsahují speciální znaky, lze přeložit pomocí úplné syntaxe dotazů a vlastních analyzátorů.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989615"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Porovnávání vzorů a speciálních znaků (pomlčky)

Pro dotazy, které zahrnují speciální znaky (`-, *, (, ), /, \, =`) nebo pro vzorce dotazů na základě částečných podmínek v rámci většího výrazu, jsou obvykle potřeba další konfigurační kroky, aby se zajistilo, že index obsahuje očekávaný obsah ve správném formátu. 

Ve výchozím nastavení je telefonní číslo jako `+1 (425) 703-6214` vyraženo jako `"1"`, `"425"``"703"``"6214"`. Jak je možné si představit, hledání `"3-62"`, částečné výrazy, které zahrnují pomlčku, selžou, protože daný obsah v indexu skutečně neexistuje. 

Pokud potřebujete hledat na částečných řetězcích nebo speciální znaky, můžete výchozí analyzátor přepsat vlastním analyzátorem, který pracuje v rámci jednodušších pravidel pro tokenizace, přičemž se zachovává celá slova, která jsou nezbytná, když řetězce dotazů obsahují části Term nebo Special. písmena. Krok zpět, přístup vypadá takto:

+ Vyberte předdefinovaný analyzátor nebo definujte vlastní analyzátor, který vytváří požadovaný výstup.
+ Přiřaďte analyzátor k poli.
+ Sestavení indexu a testu

Tento článek vás provede těmito úkoly. Postup, který je zde popsán, je užitečný v dalších scénářích: dotazy na zástupné znaky a regulární výrazy také vyžadují jako základ pro porovnávání vzorů celé výrazy. 

> [!TIP]
> Vyhodnocování analyers je iterativní proces, který vyžaduje časté opětovné sestavení indexu. Tento krok můžete usnadnit pomocí metody post, rozhraní REST API pro [vytváření indexů](https://docs.microsoft.com/rest/api/searchservice/create-index), [odstraňování indexů](https://docs.microsoft.com/rest/api/searchservice/delete-index),[načítání dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)a [hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents). V případě načtených dokumentů musí tělo žádosti obsahovat malý reprezentativní datový soubor, který chcete otestovat (například pole s telefonními čísly nebo kódy produktů). Pomocí těchto rozhraní API ve stejné kolekci pro publikování můžete tyto kroky rychle procházet.

## <a name="choosing-an-analyzer"></a>Výběr analyzátoru

Při výběru analyzátoru, který vytváří úplné tokeny, jsou běžné možnosti následujících analyzátorů:

| MBSA | Chování |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Obsah celého pole je považován za jeden výraz. |
| [typy](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Odděluje pouze prázdné znaky. Výrazy, které obsahují pomlčky nebo jiné znaky, jsou považovány za jeden token. |
| [vlastní analyzátor](index-add-custom-analyzers.md) | doporučil Vytvoření vlastního analyzátoru vám umožní zadat provádějících tokenizaci i filtr tokenů. Předchozí analyzátory se musí používat tak, jak jsou. Vlastní analyzátor vám umožní vybrat, které tokenizátory musíte nejdřív a filtry tokenů se mají použít. <br><br>Doporučenou kombinací je [klíčové slovo provádějících tokenizaci](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) s [filtrem malých případových tokenů](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Předem definovaný [analyzátor klíčových slov](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nerozlišuje malá a velká písmena, což může způsobit selhání dotazů. Vlastní analyzátor poskytuje mechanismus pro přidání filtru s nižším případem tokenu. |

Pokud používáte nástroj pro testování webového rozhraní API, jako je například post, můžete přidat [volání nástroje Test Analyzer REST](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) pro kontrolu výstupu s vydanými tokeny. V případě existujícího indexu a pole obsahujícího pomlčky nebo částečné výrazy můžete vyzkoušet různé analyzátory nad konkrétními podmínkami a zjistit, jaké tokeny jsou vydávané.  

1. Podívejte se na standardní analyzátor a zjistěte, jak se ve výchozím nastavení používají výrazy.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Vyhodnoťte odpověď, abyste viděli, jak je text v indexu vyhodnocený jako token. Všimněte si, jak je každý výraz nižší – použita a rozčleněný.

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
1. Upravte požadavek na použití `whitespace` nebo analyzátoru `keyword`:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Nyní se odpověď skládá z jediného tokenu, horních použita, s pomlčkami zachované jako součást řetězce. Pokud potřebujete vyhledávat vzor nebo částečný termín, stroj dotazů má teď základ pro vyhledání shody.


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
> Počítejte s tím, že analyzátory dotazů často v rámci vyhledávacího výrazu při sestavování stromu dotazů často malými písmeny. Pokud používáte analyzátor, který nerozlišuje malá a velká písmena a nezískáváte očekávané výsledky, může to být důvod. Řešením je přidat filtr tokenů lwower-Case.

## <a name="analyzer-definitions"></a>Definice analyzátoru
 
Bez ohledu na to, jestli vyhodnocujete analyzátory nebo přesouváte do konkrétní konfigurace, budete muset určit analyzátor definice pole a případně nakonfigurovat samotný analyzátor, pokud nepoužíváte integrovaný analyzátor. Při výměně analyzátorů je obvykle nutné index znovu sestavit (vyřadit, znovu vytvořit a znovu načíst). 

### <a name="use-built-in-analyzers"></a>Použití integrovaných analyzátorů

Předdefinované nebo předdefinované analyzátory lze zadat podle názvu u vlastnosti `analyzer` definice pole, přičemž v indexu není vyžadována žádná další konfigurace. Následující příklad ukazuje, jak byste nastavili analyzátor `whitespace` v poli.

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
Další informace o všech dostupných analyzátorech jsou uvedeny v [seznamu předdefinované analyzátory](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Použití vlastních analyzátorů

Pokud používáte [vlastní analyzátor](index-add-custom-analyzers.md), definujte ho v indexu pomocí uživatelsky definované kombinace provádějících tokenizaci, tokenfilter, s možnými konfiguračními nastaveními. Potom na ni odkazuje definice pole, stejně jako na integrovaný analyzátor.

Pokud je cílem celočíselné tokenizace, doporučuje se vlastní analyzátor, který se skládá z **klíčového slova provádějících tokenizaci** a **Filtr tokenů nižších případů** .

+ Klíčové slovo provádějících tokenizaci vytvoří jeden token pro celý obsah pole.
+ Filtr s malými písmeny transformuje velká písmena na text s malými písmeny. Analyzátory dotazů obvykle malými písmeny obsahují textové vstupy. Lowercasing se na vstupy a s vydanými výrazy homogenní hodnoty.

Následující příklad znázorňuje vlastní analyzátor, který poskytuje klíčové slovo provádějících tokenizaci a filtr tokenu malého písmena.

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
]

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
> Filtr tokenů `keyword_v2` provádějících tokenizaci a `lowercase` jsou v systému známy a používají výchozí konfigurace, což je důvod, proč je můžete na ně odkazovat podle názvu, aniž byste je museli definovat jako první.

## <a name="tips-and-best-practices"></a>Tipy a osvědčené postupy

### <a name="tune-query-performance"></a>Ladění výkonu dotazů

Pokud implementujete doporučenou konfiguraci, která zahrnuje keyword_v2 provádějících tokenizaci a filtr tokenů menšího případu, můžete si všimnout snížení výkonu dotazů kvůli dalšímu zpracování filtru tokenů u stávajících tokenů ve vašem indexu. 

Následující příklad přidá [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) , aby bylo možné porovnávat předpony rychleji. Pro kombinace znaků 2-25, které obsahují znaky, jsou vygenerovány další tokeny: (nikoli pouze MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/Čt, MSFT/SQL). Jak si představím, další tokenizace je výsledkem většího indexu.

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
]

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

Analyzátory jsou volány během indexování a při provádění dotazu. Je běžné použít stejný analyzátor pro obojí, ale můžete pro každou úlohu nakonfigurovat vlastní analyzátory. Přepisy analyzátoru jsou zadány v [definici indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) v sekci `analyzers` a následně odkazovány na konkrétní pole. 

Když se vlastní analýza vyžaduje jenom při indexování, můžete použít vlastní analyzátor pro jenom indexování a pokračovat v používání standardního analyzátoru Lucene (nebo jiného analyzátoru) pro dotazy.

Chcete-li určit analýzu konkrétní role, můžete nastavit vlastnosti pro každé z nich, nastavení `indexAnalyzer` a `searchAnalyzer` namísto výchozí vlastnosti `analyzer`.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>Duplicitní pole pro různé scénáře

Jiná možnost využívá přiřazení analyzátoru pro každé pole k optimalizaci pro různé scénáře. Konkrétně můžete definovat "featureCode" a "featureCodeRegex" pro podporu normálního fulltextového vyhledávání na prvním a rozšířené porovnávání vzorů na druhé straně.

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

Tento článek vysvětluje, jak analyzátory přispívat k dotazování na problémy a řešení problémů s dotazy. V dalším kroku se podíváme na podrobnější přehled o tom, jaký vliv má analýza na indexování a zpracování dotazů. Zejména zvažte použití rozhraní analyzovat text API k vrácení výstupu s vydanými tokeny, abyste viděli přesně to, co analyzátor vytváří pro váš index.

+ [Analyzátory jazyka](search-language-support.md)
+ [Analyzátory pro zpracování textu v Azure Kognitivní hledání](search-analyzers.md)
+ [Analyzovat textové rozhraní API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Jak funguje fulltextové vyhledávání (architektura dotazů)](search-lucene-query-architecture.md)