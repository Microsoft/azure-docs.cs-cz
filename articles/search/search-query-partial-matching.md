---
title: Částečné termíny, vzory a speciální znaky
titleSuffix: Azure Cognitive Search
description: Pomocí dotazů se zástupnými kódy, regulárními výrazy a předčíslími se můžete shodovat s celými nebo částečnými termíny v požadavku na dotaz Azure Cognitive Search. Těžko shodovat vzory, které obsahují speciální znaky lze vyřešit pomocí syntaxe úplného dotazu a vlastní analyzátory.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 7f001a0d443e4ec668aedaabb7505884163bf37e
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666786"
---
# <a name="partial-term-search-and-patterns-with-special-characters---azure-cognitive-search-wildcard-regex-patterns"></a>Částečné vyhledávání termínů a vzory se speciálními znaky – Azure Cognitive Search (zástupný znak, regulární výraz, vzory)

*Částečné hledání termínu* odkazuje na dotazy skládající se z fragmentů termínu, jako je například první, poslední nebo vnitřní části řetězce. *Vzorek* může být kombinací fragmentů, někdy se speciálními znaky, jako jsou pomlčky nebo lomítka, které jsou součástí dotazu. Běžné případy použití zahrnují dotazování na části telefonního čísla, adresy URL, kódy osob nebo kódů produktů nebo složená slova.

Částečné hledání může být problematické, pokud index nemá termíny ve formátu požadovaném pro porovnávání vzorů. Během fáze analýzy textu indexování pomocí výchozího standardního analyzátoru jsou speciální znaky zahozeny, složené a složené řetězce jsou rozděleny, což způsobuje selhání dotazů na vzorky, když není nalezena žádná shoda. Například telefonní číslo `+1 (425) 703-6214`jako (tokenizované `"425"` `"703"`jako `"6214"` `"1"`, , ) se `"3-62"` v dotazu nezobrazí, protože tento obsah ve skutečnosti v indexu neexistuje. 

Řešením je vyvolat analyzátor, který zachová úplný řetězec, včetně mezer a speciálních znaků v případě potřeby, takže můžete podporovat částečné termíny a vzorky. Vytvoření dalšího pole pro neporušený řetězec a pomocí analyzátoru pro zachování obsahu je základem řešení.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Co je částečné vyhledávání v Azure Cognitive Search

V Azure Cognitive Search částečné vyhledávání a vzor je k dispozici v těchto formulářích:

+ [Prefix vyhledávání](query-simple-syntax.md#prefix-search), `search=cap*`například , odpovídající na "Cap'n Jack waterfront Inn" nebo "Gacc Capital". Pro vyhledávání předpon můžete použít syntaxi jednoduchého dotazu.

+ [Hledání se zástupnými znaky](query-lucene-syntax.md#bkmk_wildcard) nebo [regulární výrazy,](query-lucene-syntax.md#bkmk_regex) které vyhledávají vzorek nebo části vloženého řetězce, včetně přípony. Zástupný znak a regulární výrazy vyžadují úplnou syntaxi Lucene. 

  Některé příklady částečného hledání termínů zahrnují následující. Pro dotaz přípony, vzhledem k termínu "alfanumerické",`search=/.*numeric.*/`byste použít zástupné hledání ( ) najít shodu. Pro částečný termín, který obsahuje znaky, jako je například fragment adresy URL, může být nutné přidat řídicí znaky. V JSON lomítko lomítko `/` `\`je uvozena s zpětné lomítko . Jako takový `search=/.*microsoft.com\/azure\/.*/` je syntaxe fragmentu adresy URL "microsoft.com/azure/".

Jak již bylo uvedeno, všechny výše uvedené vyžadují, aby index obsahuje řetězce ve formátu příznivém pro porovnávání vzorů, které standardní analyzátor neposkytuje. Podle kroků v tomto článku můžete zajistit, že existuje potřebný obsah pro podporu těchto scénářů.

## <a name="solving-partial-search-problems"></a>Řešení problémů s částečným vyhledáváním

Když potřebujete hledat vzorky nebo speciální znaky, můžete přepsat výchozí analyzátor pomocí vlastního analyzátoru, který pracuje podle jednodušších pravidel tokenizace a zachová celý řetězec. Vezmeme-li krok zpět, přístup vypadá takto:

+ Definujte pole pro uložení neporušené verze řetězce (za předpokladu, že chcete analyzovat a neanalyzovaný text)
+ Zvolte předdefinovaný analyzátor nebo definujte vlastní analyzátor pro výstup neporušeného řetězce
+ Přiřazení analyzátoru k poli
+ Sestavení a testování indexu

> [!TIP]
> Vyhodnocení analyzátory je iterativní proces, který vyžaduje časté index znovu. Tento krok můžete usnadnit pomocí Postman, REST API pro [vytvoření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index), [Odstranit index](https://docs.microsoft.com/rest/api/searchservice/delete-index),[Načíst dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)a Hledat [dokumenty](https://docs.microsoft.com/rest/api/searchservice/search-documents). U načíst dokumenty by měl text požadavku obsahovat malou reprezentativní datovou sadu, kterou chcete otestovat (například pole s telefonními čísly nebo kódy produktů). S těmito api ve stejné kolekci Postman, můžete cykonoběh ovat tyto kroky rychle.

## <a name="duplicate-fields-for-different-scenarios"></a>Duplicitní pole pro různé scénáře

Analyzátory jsou přiřazovány pro pole, což znamená, že můžete vytvořit pole v indexu pro optimalizaci pro různé scénáře. Konkrétně můžete definovat "featureCode" a "featureCodeRegex" pro podporu pravidelné fulltextové vyhledávání na první a pokročilé porovnávání vzorů na druhé.

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

## <a name="choose-an-analyzer"></a>Výběr analyzátoru

Při výběru analyzátoru, který vytváří tokeny po celou dobu, jsou běžné volby následující analyzátory:

| Analyzer | Chování |
|----------|-----------|
| [klíčové slovo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Obsah celého pole je tokenizován jako jeden termín. |
| [Mezery](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Odděluje pouze na prázdné matné znaky. Termíny, které obsahují pomlčky nebo jiné znaky jsou považovány za jeden token. |
| [vlastní analyzátor](index-add-custom-analyzers.md) | (doporučeno) Vytvoření vlastního analyzátoru umožňuje zadat tokenizer a token filtr. Předchozí analyzátory musí být použity tak, jak jsou. Vlastní analyzátor umožňuje vybrat, které tokenizers a token filtry použít. <br><br>Doporučená kombinace je [tokenizer klíčového slova](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) s [filtrem tokenů malých písmen](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Předdefinovaný [analyzátor klíčových slov](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) sám o sobě nepředstavuje malá písmena žádného textu s velkými písmeny, což může způsobit selhání dotazů. Vlastní analyzátor poskytuje mechanismus pro přidání filtru tokenu malých písmen. |

Pokud používáte testovací nástroj webového rozhraní API, jako je Postman, můžete přidat [volání REST analyzátoru testu](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) ke kontrole tokenizovaného výstupu.

Musíte mít existující index pro práci s. Vzhledem k existující index a pole obsahující pomlčky nebo částečné termíny, můžete vyzkoušet různé analyzátory přes konkrétní termíny zjistit, jaké tokeny jsou vydávány.  

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
> Uvědomte si, že analyzátory dotazů často malá písmena ve výrazu hledání při vytváření stromu dotazů. Pokud používáte analyzátor, který nemá vstupy textu s nižšími písmeny a nedostáváte očekávané výsledky, může to být důvod. Řešením je přidat filtr tokenu malých písmen, jak je popsáno v části "Použít vlastní analyzátory" níže.

## <a name="configure-an-analyzer"></a>Konfigurace analyzátoru
 
Bez ohledu na to, zda vyhodnocujete analyzátor nebo se posouváte vpřed s určitou konfigurací, budete muset určit analyzátor v definici pole a případně nakonfigurovat samotný analyzátor, pokud nepoužíváte vestavěný analyzátor. Při výměně analyzátory, obvykle je třeba znovu vytvořit index (přetažení, znovu vytvořit a znovu načíst). 

### <a name="use-built-in-analyzers"></a>Použití vestavěných analyzátorů

Vestavěné nebo předdefinované analyzátory lze `analyzer` zadat podle názvu na vlastnost definice pole, bez další konfigurace požadované v indexu. Následující příklad ukazuje, jak byste `whitespace` nastavili analyzátor v poli. Další informace o dostupných vestavěných analyzátorech naleznete v [seznamu předdefinovaných analyzátorů](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

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

Pokud používáte [vlastní analyzátor](index-add-custom-analyzers.md), definujte jej v indexu s uživatelem definovanou kombinací tokenizeru, tokenového filtru s možným nastavením konfigurace. Dále odkaz na definici pole, stejně jako byste vestavěný analyzátor.

Pokud je cílem tokenizace celého období, doporučuje se vlastní analyzátor, který se skládá z **tokenizeru klíčového slova** a **filtru malých tokenů.**

+ Tokenizer klíčového slova vytvoří jeden token pro celý obsah pole.
+ Filtr malých písmen tokenu transformuje velká písmena na text s velkými písmeny. Analyzátory dotazů obvykle malá písmena jakékoli vstupy textu velkých písmen. Dolní velikost pouzdra homogenizuje vstupy s tokenizovanými podmínkami.

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

## <a name="build-and-test"></a>Sestavení a otestování

Jakmile definujete index s analyzátory a definicemi polí, které podporují váš scénář, načtěte dokumenty, které mají reprezentativní řetězce, abyste mohli testovat dotazy s částečným řetězcem. 

V předchozích částech vysvětlil logiku. Tato část prochází každé rozhraní API, které byste měli volat při testování řešení. Jak již bylo uvedeno, pokud používáte interaktivní webový testovací nástroj, jako je Postman, můžete tyto úkoly rychle procházet.

+ [Odstranit index](https://docs.microsoft.com/rest/api/searchservice/delete-index) odebere existující index se stejným názvem, takže jej můžete znovu vytvořit.

+ [Vytvořit index](https://docs.microsoft.com/rest/api/searchservice/create-index) vytvoří strukturu indexu ve vyhledávací službě, včetně definic analyzátoru a polí se specifikací analyzátoru.

+ [Načíst dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importuje dokumenty se stejnou strukturou jako index a také prohledávatelný obsah. Po tomto kroku je index připraven k dotazování nebo testování.

+ [Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) byl představen v [vyberte analyzátor](#choose-an-analyzer). Otestujte některé řetězce v indexu pomocí různých analyzátorů pochopit, jak jsou tokenizovány termíny.

+ [Hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents) vysvětluje, jak vytvořit požadavek na dotaz pomocí [jednoduché syntaxe](query-simple-syntax.md) nebo [úplné syntaxe Lucene](query-lucene-syntax.md) pro zástupné symboly a regulární výrazy.

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

## <a name="next-steps"></a>Další kroky

Tento článek vysvětluje, jak analyzátory přispívají k problémům s dotazy a řeší problémy s dotazy. Jako další krok se blíže podívejte na dopad analyzátoru na indexování a zpracování dotazů. Zejména zvažte použití analyzovat textové rozhraní API vrátit tokenizovaný výstup, takže můžete přesně vidět, co analyzátor vytváří pro váš index.

+ [Analyzátory jazyka](search-language-support.md)
+ [Analyzátory pro zpracování textu v Azure Cognitive Search](search-analyzers.md)
+ [Analyzovat textové rozhraní API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Jak funguje fulltextové vyhledávání (architektura dotazů)](search-lucene-query-architecture.md)