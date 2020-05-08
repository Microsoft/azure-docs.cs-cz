---
title: Částečné výrazy, vzory a speciální znaky
titleSuffix: Azure Cognitive Search
description: Použijte dotazy na zástupné znaky, regulární výrazy a předpony, aby odpovídaly celým nebo částečným podmínkám v žádosti o dotaz na Azure Kognitivní hledání. Vzorce, které obsahují speciální znaky, lze přeložit pomocí úplné syntaxe dotazů a vlastních analyzátorů.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 05ff56c904fc48a1041ad40f00110a8ff0fd01f1
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592039"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Částečné vyhledávání a vzory s použitím speciálních znaků (zástupné znaky, regulární výrazy, vzory)

*Částečný pojem hledání* odkazuje na dotazy skládající se z fragmentů termínů, kde místo celého období může být pouze počáteční, prostřední nebo konec slova (někdy označované jako předpony, vpony nebo dotazy na přípony). *Vzor* může být kombinací fragmentů, často se speciálními znaky, jako jsou pomlčky nebo lomítka, která jsou součástí řetězce dotazu. Běžné použití – případy zahrnují dotazování na části telefonního čísla, adresy URL, osob nebo kódů produktů nebo složených slov.

Částečné a vzorové vyhledávání můžou být problematické, pokud index nemá žádné výrazy v očekávaném formátu. Během [lexikální analytické fáze](search-lucene-query-architecture.md#stage-2-lexical-analysis) indexování (za předpokladu výchozího standardního analyzátoru) jsou ignorovány speciální znaky, jsou odděleny a složené řetězce a prázdné znaky jsou odstraněny. všechny, které můžou způsobit selhání dotazů vzorů, pokud se nenajde žádná shoda. Například telefonní `+1 (425) 703-6214` číslo (s tokeny jako `"1"`, `"425"`, `"703"`, `"6214"`) se v `"3-62"` dotazu nezobrazí, protože daný obsah v indexu skutečně neexistuje. 

Řešením je vyvolat analyzátor, který v případě potřeby zachová úplný řetězec, včetně mezer a speciálních znaků, takže se můžete porovnávat na částečné výrazy a vzory. Vytvoření dalšího pole pro nedotčený řetězec a použití analyzátoru pro zachování obsahu je základem řešení.

> [!TIP]
> Znáte rozhraní API pro post a REST? [Stáhněte si kolekci příklady dotazů](https://github.com/Azure-Samples/azure-search-postman-samples/) pro dotaz na částečné výrazy a speciální znaky popsané v tomto článku.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Co je částečné vyhledávání v Azure Kognitivní hledání

V Azure Kognitivní hledání je v těchto formulářích k dispozici částečné vyhledávání a vzor:

+ [Hledání předpon](query-simple-syntax.md#prefix-search), například `search=cap*`, odpovídá na "Waterfront DIČ" nebo "Gaccho kapitálu". Pro hledání předpon můžete použít jednoduchou syntaxi dotazu nebo úplnou syntaxi dotazu Lucene.

+ [Hledání pomocí zástupných znaků](query-lucene-syntax.md#bkmk_wildcard) nebo [regulární výrazy](query-lucene-syntax.md#bkmk_regex) , které hledají vzor nebo části vloženého řetězce. Zástupné znaky a regulární výrazy vyžadují úplnou syntaxi Lucene. Dotazy přípon a index jsou formulovány jako regulární výrazy.

  Mezi příklady částečného hledání výrazu patří následující. Pro dotaz na příponu, který je dán termínem "alfanumerický", byste měli vyhledat shodu`search=/.*numeric.*/`pomocí zástupného znaku (). V případě částečného výrazu, který obsahuje vnitřní znaky, jako je například fragment adresy URL, může být nutné přidat řídicí znaky. Ve formátu JSON `/` je lomítko uvozeno zpětným lomítkem `\`. V takovém `search=/.*microsoft.com\/azure\/.*/` případě je syntaxe FRAGMENTU adresy URL "Microsoft.com/Azure/".

Jak je uvedeno výše, vyžaduje, aby index obsahoval řetězce ve formátu, který přispívá k porovnávání vzorů, které standardní analyzátor neposkytuje. Podle kroků v tomto článku můžete zajistit, aby potřebný obsah pro podporu těchto scénářů existoval.

## <a name="solving-partialpattern-search-problems"></a>Řešení problémů s částečným nebo vzorovým hledáním

Pokud potřebujete hledat fragmenty nebo vzory nebo speciální znaky, můžete přepsat výchozí analyzátor vlastním analyzátorem, který funguje v rámci jednodušších pravidel tokenizace, a zachovat celý řetězec. Krok zpět, přístup vypadá takto:

+ Definovat pole pro uložení nepůvodní verze řetězce (za předpokladu, že chcete analyzovat a neanalyzovaný text)
+ Vyberte předdefinovaný analyzátor nebo definujte vlastní analyzátor pro výstup nezměněného řetězce bez analýzy.
+ Přiřazení vlastního analyzátoru k poli
+ Sestavení a otestování indexu

> [!TIP]
> Vyhodnocování analyzátorů je iterativní proces, který vyžaduje časté opětovné sestavení indexu. Tento krok můžete usnadnit pomocí metody post, rozhraní REST API pro [vytváření indexů](https://docs.microsoft.com/rest/api/searchservice/create-index), [odstraňování indexů](https://docs.microsoft.com/rest/api/searchservice/delete-index),[načítání dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)a [hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents). V případě načtených dokumentů musí tělo žádosti obsahovat malý reprezentativní datový soubor, který chcete otestovat (například pole s telefonními čísly nebo kódy produktů). Pomocí těchto rozhraní API ve stejné kolekci pro publikování můžete tyto kroky rychle procházet.

## <a name="duplicate-fields-for-different-scenarios"></a>Duplicitní pole pro různé scénáře

Analyzátory jsou přiřazeny pro každé pole, což znamená, že můžete v indexu vytvořit pole, která se optimalizují pro různé scénáře. Konkrétně můžete definovat "featureCode" a "featureCodeRegex" pro podporu normálního fulltextového vyhledávání na prvním a rozšířené porovnávání vzorů na druhé straně.

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
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>Zvolit analyzátor

Při výběru analyzátoru, který vytváří úplné tokeny, jsou běžné možnosti následujících analyzátorů:

| MBSA | Chování |
|----------|-----------|
| [analyzátory jazyka](index-add-language-analyzers.md) | Zachovává spojovníky ve složených slovech nebo řetězcích, v samohláskách a na formulářích operací. Pokud schémata dotazů obsahují pomlčky, může být použití analyzátoru jazyka dostatečné. |
| [klíčové slovo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Obsah celého pole je považován za jeden výraz. |
| [typy](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Odděluje pouze prázdné znaky. Výrazy, které obsahují pomlčky nebo jiné znaky, jsou považovány za jeden token. |
| [vlastní analyzátor](index-add-custom-analyzers.md) | doporučil Vytvoření vlastního analyzátoru vám umožní zadat provádějících tokenizaci i filtr tokenů. Předchozí analyzátory se musí používat tak, jak jsou. Vlastní analyzátor vám umožní vybrat, které tokenizátory musíte nejdřív a filtry tokenů se mají použít. <br><br>Doporučenou kombinací je [klíčové slovo provádějících tokenizaci](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) s [filtrem malých případových tokenů](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Předem definovaný [analyzátor klíčových slov](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nerozlišuje malá a velká písmena, což může způsobit selhání dotazů. Vlastní analyzátor poskytuje mechanismus pro přidání filtru s nižším případem tokenu. |

Pokud používáte nástroj pro testování webového rozhraní API, jako je například post, můžete přidat [volání nástroje Test Analyzer REST](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) pro kontrolu výstupu s vydanými tokeny.

Musíte mít existující index, se kterým chcete pracovat. V případě existujícího indexu a pole obsahujícího pomlčky nebo částečné výrazy můžete vyzkoušet různé analyzátory nad konkrétními podmínkami a zjistit, jaké tokeny jsou vydávané.  

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
1. Upravte požadavek na použití analyzátoru `whitespace` nebo `keyword` :

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
> Počítejte s tím, že analyzátory dotazů často v rámci vyhledávacího výrazu při sestavování stromu dotazů často malými písmeny. Pokud používáte analyzátor, který nerozlišuje malá a velká písmena a nezískáváte očekávané výsledky, může to být důvod. Řešením je přidání filtru s malým případem tokenu, jak je popsáno níže v části "použití vlastních analyzátorů".

## <a name="configure-an-analyzer"></a>Konfigurace analyzátoru
 
Bez ohledu na to, jestli vyhodnocujete analyzátory nebo přesouváte do konkrétní konfigurace, budete muset určit analyzátor definice pole a případně nakonfigurovat samotný analyzátor, pokud nepoužíváte integrovaný analyzátor. Při výměně analyzátorů je obvykle nutné index znovu sestavit (vyřadit, znovu vytvořit a znovu načíst). 

### <a name="use-built-in-analyzers"></a>Použití integrovaných analyzátorů

Předdefinované nebo předdefinované analyzátory lze zadat podle názvu u `analyzer` vlastnosti definice pole, přičemž v indexu není vyžadována žádná další konfigurace. Následující příklad ukazuje, jak byste měli nastavit `whitespace` analyzátor v poli. 

Další scénáře a další informace o dalších integrovaných analyzátorech najdete v [seznamu předdefinovaných analyzátorů](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

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

### <a name="use-custom-analyzers"></a>Použití vlastních analyzátorů

Pokud používáte [vlastní analyzátor](index-add-custom-analyzers.md), definujte ho v indexu pomocí uživatelsky definované kombinace provádějících tokenizaci, filtru tokenu s možnými konfiguračními nastaveními. Potom na ni odkazuje definice pole, stejně jako na integrovaný analyzátor.

Pokud je cílem celočíselné tokenizace, doporučuje se vlastní analyzátor, který se skládá z **klíčového slova provádějících tokenizaci** a **Filtr tokenů nižších případů** .

+ Klíčové slovo provádějících tokenizaci vytvoří jeden token pro celý obsah pole.
+ Filtr s malými písmeny transformuje velká písmena na text s malými písmeny. Analyzátory dotazů obvykle malými písmeny obsahují textové vstupy. Malá a velká písmena homogenních vstupů s použitím s tokeny.

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
> `keyword_v2` Provádějících tokenizaci a `lowercase` filtr tokenu jsou známy v systému a používají výchozí konfigurace, což je důvod, proč je můžete na ně odkazovat podle názvu, aniž byste je museli definovat jako první.

## <a name="build-and-test"></a>Sestavení a otestování

Po definování indexu s analyzátory a definicemi polí, které podporují váš scénář, načtěte dokumenty, které mají reprezentativní řetězce, aby bylo možné testovat částečné řetězcové dotazy. 

Předchozí části vysvětlely logiku. Tato část popisuje každé rozhraní API, které byste měli zavolat při testování řešení. Jak bylo uvedeno dříve, pokud používáte interaktivní nástroj webového testu, jako je například post, můžete rychle projít tyto úlohy.

+ Možnost [odstranit index](https://docs.microsoft.com/rest/api/searchservice/delete-index) odebere existující index se stejným názvem, aby jej bylo možné znovu vytvořit.

+ Příkaz [vytvořit index](https://docs.microsoft.com/rest/api/searchservice/create-index) vytvoří strukturu indexů ve vaší vyhledávací službě, včetně definic analyzátoru a polí se specifikací analyzátoru.

+ [Načíst dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importuje dokumenty se stejnou strukturou jako index a také prohledávatelný obsah. Po provedení tohoto kroku je váš index připravený na dotazování nebo testování.

+ [Analyzátor testů](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) byl představen v [příkazu zvolit analyzátor](#choose-an-analyzer). Otestujte některé řetězce v indexu pomocí nejrůznějších analyzátorů, abyste pochopili, jak jsou výrazy s tokeny.

+ [Hledání v dokumentech](https://docs.microsoft.com/rest/api/searchservice/search-documents) vysvětluje, jak vytvořit požadavek na dotaz pomocí [jednoduché](query-simple-syntax.md) syntaxe nebo [úplné syntaxe Lucene](query-lucene-syntax.md) pro zástupné znaky a regulární výrazy.

  U částečných dotazů, jako je například dotazování "3-6214" k vyhledání shody na "+ 1 (425) 703-6214", můžete použít jednoduchou syntaxi: `search=3-6214&queryType=simple`.

  U dotazů vpony a přípon, jako je například dotazování "num" nebo "numeric" k vyhledání shody na "alfanumerické", použijte úplnou syntaxi Lucene a regulární výraz:`search=/.*num.*/&queryType=full`

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

Analyzátory jsou volány během indexování a při provádění dotazu. Je běžné použít stejný analyzátor pro obojí, ale můžete pro každou úlohu nakonfigurovat vlastní analyzátory. Přepsání analyzátoru jsou uvedena v [definici indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) v `analyzers` oddílu a následně odkazována na konkrétní pole. 

Když se vlastní analýza vyžaduje jenom při indexování, můžete použít vlastní analyzátor pro jenom indexování a pokračovat v používání standardního analyzátoru Lucene (nebo jiného analyzátoru) pro dotazy.

Chcete-li určit analýzu konkrétní role, můžete nastavit vlastnosti pro každé pole, nastavení `indexAnalyzer` a `searchAnalyzer` místo výchozí `analyzer` vlastnosti.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>Další kroky

Tento článek vysvětluje, jak analyzátory přispívat k dotazování na problémy a řešení problémů s dotazy. V dalším kroku se podíváme na podrobnější přehled o tom, jaký vliv má analýza na indexování a zpracování dotazů. Zejména zvažte použití rozhraní analyzovat text API k vrácení výstupu s vydanými tokeny, abyste viděli přesně to, co analyzátor vytváří pro váš index.

+ [Analyzátory jazyka](search-language-support.md)
+ [Analyzátory pro zpracování textu v Azure Kognitivní hledání](search-analyzers.md)
+ [Analyzovat textové rozhraní API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Jak funguje fulltextové vyhledávání (architektura dotazů)](search-lucene-query-architecture.md)