---
title: Částečné výrazy, vzory a speciální znaky
titleSuffix: Azure Cognitive Search
description: Použijte dotazy na zástupné znaky, regulární výrazy a předpony, aby odpovídaly celým nebo částečným podmínkám v žádosti o dotaz na Azure Kognitivní hledání. Vzorce, které obsahují speciální znaky, lze přeložit pomocí úplné syntaxe dotazů a vlastních analyzátorů.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 2e2625fff802e71f797bf6970e763f2bf11c393e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584172"
---
# <a name="partial-term-search-and-patterns-with-special-characters-hyphens-wildcard-regex-patterns"></a>Částečné vyhledávání a vzory s použitím speciálních znaků (spojovníky, zástupné znaky, regulární výrazy, vzory)

*Částečný pojem hledání* odkazuje na dotazy skládající se z fragmentů termínů, kde místo celého období může být pouze počáteční, prostřední nebo konec slova (někdy označované jako předpony, vpony nebo dotazy na přípony). Částečné hledání může zahrnovat kombinaci fragmentů, často se speciálními znaky, jako jsou pomlčky, pomlčky nebo lomítka, která jsou součástí řetězce dotazu. Běžné použití – případy zahrnují části telefonního čísla, adresy URL, kódy nebo složená slova se slovem.

Částečné hledání a řetězce dotazů, které obsahují speciální znaky, mohou být problematické, pokud index nemá tokeny v očekávaném formátu. Během [lexikální analytické fáze](search-lucene-query-architecture.md#stage-2-lexical-analysis) indexování (za předpokladu výchozího analyzátoru Standard) se speciální znaky zahodí, jsou rozdělená i složená slova a prázdné znaky. všechny, které můžou způsobit neúspěch dotazů, pokud se nenajde shoda. Například telefonní číslo `+1 (425) 703-6214` (s tokeny jako `"1"` , `"425"` , `"703"` ,) se v dotazu nezobrazí, `"6214"` `"3-62"` protože daný obsah v indexu skutečně neexistuje. 

Řešením je vyvolat analyzátor během indexování, který zachová úplný řetězec, včetně mezer a speciálních znaků v případě potřeby, abyste mohli do řetězce dotazu vložit mezery a znaky. Podobně s úplným řetězcem, který není založen na menších částech, umožňuje porovnávání vzorů pro "začíná na" nebo "končí" dotazy, kde vzorek, který zadáte, lze vyhodnotit proti termínu, který není transformován lexikální analýzou. Vytvoření dalšího pole pro nepoškozený řetězec a použití analyzátoru pro zachování obsahu, který vygeneruje úplné tokeny, je řešení pro porovnávání vzorů i pro porovnávání řetězců dotazů, které obsahují speciální znaky.

> [!TIP]
> Pokud jste obeznámeni s rozhraními API pro odesílání a REST, [Stáhněte si kolekci příklady dotazů](https://github.com/Azure-Samples/azure-search-postman-samples/) pro dotaz na částečné výrazy a speciální znaky popsané v tomto článku.

## <a name="about-partial-term-search"></a>Hledání částečného termínu

Azure Kognitivní hledání hledá v indexu celé tokeny a nenalezne shodu s částečným termínem, pokud nezadáte zástupné symboly zástupných znaků ( `*` a `?` ), nebo tento dotaz naformátujete jako regulární výraz. Částečné výrazy jsou určeny pomocí těchto technik:

+ [Dotazy regulárních výrazů](query-lucene-syntax.md#bkmk_regex) můžou být libovolný regulární výraz, který je platný v rámci Apache Lucene. 

+ [Operátory zástupných znaků s porovnáváním předpon](query-simple-syntax.md#prefix-search) odkazují na všeobecně rozpoznaný vzor, který zahrnuje začátek období, následované `*` `?` operátory nebo příponou přípony, jako je například `search=cap*` porovnávání "Waterfront DIČ" nebo "Gaccho kapitálu". Shoda předpony je podporována v jednoduché i úplné syntaxi dotazů Lucene.

+ [Zástupný znak s vpony a příponou](query-lucene-syntax.md#bkmk_wildcard) , které jsou `*` `?` uvnitř nebo na začátku výrazu, a vyžaduje syntaxi regulárního výrazu (kde výraz je uzavřený s lomítky). Například řetězec dotazu ( `search=/.*numeric*./` ) vrátí výsledky typu "alfanumerický" a "alfanumerické" jako přípona a vpony shody.

V případě částečného nebo vzorového vyhledávání a několika dalších formulářů dotazů, jako je hledání přibližných výsledků, se analyzátory nepoužívají v době dotazu. Pro tyto formuláře dotazů, které analyzátor detekuje přítomnost operátorů a oddělovačů, je řetězec dotazu předán modulu bez lexikální analýzy. Pro tyto formuláře dotazů se analyzátor zadaný v poli ignoruje.

> [!NOTE]
> Pokud částečný řetězec dotazu obsahuje znaky, například lomítka v fragmentu adresy URL, může být nutné přidat řídicí znaky. Ve formátu JSON je lomítko `/` uvozeno zpětným lomítkem `\` . V takovém případě `search=/.*microsoft.com\/azure\/.*/` je syntaxe fragmentu adresy URL "Microsoft.com/Azure/".

## <a name="solving-partialpattern-search-problems"></a>Řešení problémů s částečným nebo vzorovým hledáním

Pokud potřebujete hledat fragmenty nebo vzory nebo speciální znaky, můžete přepsat výchozí analyzátor vlastním analyzátorem, který funguje v rámci jednodušších pravidel pro tokenizace, a zachovat celý řetězec v indexu. Krok zpět, přístup vypadá takto:

1. Definovat pole pro uložení nepůvodní verze řetězce (za předpokladu, že chcete analyzovat a neanalyzovaný text v době dotazu)
1. Vyhodnotit a vybrat mezi různými analyzátory, které generují tokeny na správné úrovni členitosti
1. Přiřaďte analyzátor k poli.
1. Sestavení a otestování indexu

> [!TIP]
> Vyhodnocování analyzátorů je iterativní proces, který vyžaduje časté opětovné sestavení indexu. Tento krok můžete usnadnit pomocí metody post, rozhraní REST API pro [vytváření indexů](/rest/api/searchservice/create-index), [odstraňování indexů](/rest/api/searchservice/delete-index),[načítání dokumentů](/rest/api/searchservice/addupdate-or-delete-documents)a [hledání dokumentů](/rest/api/searchservice/search-documents). V případě načtených dokumentů musí tělo žádosti obsahovat malý reprezentativní datový soubor, který chcete otestovat (například pole s telefonními čísly nebo kódy produktů). Pomocí těchto rozhraní API ve stejné kolekci pro publikování můžete tyto kroky rychle procházet.

## <a name="1---create-a-dedicated-field"></a>1. vytvoření vyhrazeného pole

Analyzátory určují, jak jsou v indexu vyraženy výrazy. Vzhledem k tomu, že analyzátory jsou přiřazeny pro každé pole, můžete v indexu vytvořit pole pro optimalizaci pro různé scénáře. Můžete například definovat "featureCode" a "featureCodeRegex" pro podporu normálního fulltextového vyhledávání na prvním a rozšířené porovnávání vzorů na druhé straně. Analyzátory přiřazené ke každému poli určují, jak se obsah každého pole v indexu používá jako token.  

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

<a name="set-an-analyzer"></a>

## <a name="2---set-an-analyzer"></a>2. nastavení analyzátoru

Při výběru analyzátoru, který vytváří úplné tokeny, jsou běžné možnosti následujících analyzátorů:

| MBSA | Chování |
|----------|-----------|
| [analyzátory jazyka](index-add-language-analyzers.md) | Zachovává spojovníky ve složených slovech nebo řetězcích, v samohláskách a na formulářích operací. Pokud schémata dotazů obsahují pomlčky, může být použití analyzátoru jazyka dostatečné. |
| [klíčové slovo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Obsah celého pole je považován za jeden výraz. |
| [typy](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Odděluje pouze prázdné znaky. Výrazy, které obsahují pomlčky nebo jiné znaky, jsou považovány za jeden token. |
| [vlastní analyzátor](index-add-custom-analyzers.md) | doporučil Vytvoření vlastního analyzátoru vám umožní zadat provádějících tokenizaci i filtr tokenů. Předchozí analyzátory se musí používat tak, jak jsou. Vlastní analyzátor vám umožní vybrat, které tokenizátory musíte nejdřív a filtry tokenů se mají použít. <br><br>Doporučenou kombinací je [klíčové slovo provádějících tokenizaci](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) s [filtrem malých případových tokenů](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Integrovaná [analyzátor klíčových slov](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) sám o sobě nerozlišuje malá a velká písmena, což může způsobit selhání dotazů. Vlastní analyzátor poskytuje mechanismus pro přidání filtru s nižším případem tokenu. |

Pokud používáte nástroj pro testování webového rozhraní API, jako je například post, můžete přidat [volání nástroje Test Analyzer REST](/rest/api/searchservice/test-analyzer) pro kontrolu výstupu s vydanými tokeny.

Abyste mohli pracovat s, musíte mít naplněný index. V případě existujícího indexu a pole obsahujícího pomlčky nebo částečné výrazy můžete vyzkoušet různé analyzátory nad konkrétními podmínkami a zjistit, jaké tokeny jsou vydávané.  

1. Nejdřív zkontrolujte standardní analyzátor a podívejte se, jak se ve výchozím nastavení používají výrazy.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Vyhodnoťte odpověď, abyste viděli, jak je text v indexu vyhodnocený jako token. Všimněte si, jak je každý výraz nižší – použita, odeberou se pomlčky a podřetězce se rozdělují na jednotlivé tokeny. Tento dokument ve výsledcích vrátí jenom dotazy, které se shodují s těmito tokeny. Dotaz, který obsahuje "10-a", se nezdaří.

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
1. Nyní upravte žádost o použití `whitespace` `keyword` analyzátoru nebo:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Nyní se odpověď skládá z jediného tokenu, horních použita, s pomlčkami zachované jako součást řetězce. Pokud potřebujete vyhledat vzor nebo částečný výraz, jako je "10-ani", stroj dotazů nyní obsahuje základ pro nalezení shody.


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
> Počítejte s tím, že analyzátory dotazů často v rámci vyhledávacího výrazu při sestavování stromu dotazů často malými písmeny. Pokud používáte analyzátor, který při indexování nerozlišuje malá a velká písmena, nezískáváte očekávané výsledky, může to být důvod. Řešením je přidání filtru s malým případem tokenu, jak je popsáno níže v části "použití vlastních analyzátorů".

## <a name="3---configure-an-analyzer"></a>3. konfigurace analyzátoru
 
Bez ohledu na to, jestli vyhodnocujete analyzátory nebo přesouváte do konkrétní konfigurace, budete muset určit analyzátor definice pole a případně nakonfigurovat samotný analyzátor, pokud nepoužíváte integrovaný analyzátor. Při výměně analyzátorů je obvykle nutné index znovu sestavit (vyřadit, znovu vytvořit a znovu načíst). 

### <a name="use-built-in-analyzers"></a>Použití integrovaných analyzátorů

Předdefinované analyzátory lze zadat podle názvu u `analyzer` vlastnosti definice pole, přičemž v indexu není vyžadována žádná další konfigurace. Následující příklad ukazuje, jak byste měli nastavit `whitespace` analyzátor v poli. 

Další scénáře a další informace o dalších integrovaných analyzátorech najdete v tématu [integrované analyzátory](./index-add-custom-analyzers.md#built-in-analyzers). 

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
> `keyword_v2`Provádějících tokenizaci a `lowercase` Filtr tokenu jsou známy v systému a používají výchozí konfigurace, což je důvod, proč je můžete na ně odkazovat podle názvu, aniž byste je museli definovat jako první.

## <a name="4---build-and-test"></a>4 – sestavení a testování

Po definování indexu s analyzátory a definicemi polí, které podporují váš scénář, načtěte dokumenty, které mají reprezentativní řetězce, aby bylo možné testovat částečné řetězcové dotazy. 

Předchozí části vysvětlely logiku. Tato část popisuje každé rozhraní API, které byste měli zavolat při testování řešení. Jak bylo uvedeno dříve, pokud používáte interaktivní nástroj webového testu, jako je například post, můžete rychle projít tyto úlohy.

+ Možnost [odstranit index](/rest/api/searchservice/delete-index) odebere existující index se stejným názvem, aby jej bylo možné znovu vytvořit.

+ Příkaz [vytvořit index](/rest/api/searchservice/create-index) vytvoří strukturu indexů ve vaší vyhledávací službě, včetně definic analyzátoru a polí se specifikací analyzátoru.

+ [Načíst dokumenty](/rest/api/searchservice/addupdate-or-delete-documents) importuje dokumenty se stejnou strukturou jako index a také prohledávatelný obsah. Po provedení tohoto kroku je váš index připravený na dotazování nebo testování.

+ [Test Analyzer](/rest/api/searchservice/test-analyzer) byl představen v [Nastavení analyzátoru](#set-an-analyzer). Otestujte některé řetězce v indexu pomocí nejrůznějších analyzátorů, abyste pochopili, jak jsou výrazy s tokeny.

+ [Hledání v dokumentech](/rest/api/searchservice/search-documents) vysvětluje, jak vytvořit požadavek na dotaz pomocí [jednoduché](query-simple-syntax.md) syntaxe nebo [úplné syntaxe Lucene](query-lucene-syntax.md) pro zástupné znaky a regulární výrazy.

  U částečných dotazů, jako je například dotazování "3-6214" k vyhledání shody na "+ 1 (425) 703-6214", můžete použít jednoduchou syntaxi: `search=3-6214&queryType=simple` .

  U dotazů vpony a přípon, jako je například dotazování "num" nebo "numeric" k vyhledání shody na "alfanumerické", použijte úplnou syntaxi Lucene a regulární výraz: `search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>Ladění výkonu dotazů

Pokud implementujete doporučenou konfiguraci, která zahrnuje keyword_v2 provádějících tokenizaci a filtr tokenů menšího případu, můžete si všimnout snížení výkonu dotazů kvůli dalšímu zpracování filtru tokenů u stávajících tokenů ve vašem indexu. 

Následující příklad přidá [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) , aby bylo možné porovnávat předpony rychleji. Pro kombinace znaků 2-25, které obsahují znaky, jsou vygenerovány další tokeny: (nikoli pouze MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/Čt, MSFT/SQL). 

Jak si představím, další tokenizace je výsledkem většího indexu. Pokud máte dostatečnou kapacitu pro přizpůsobení většího indexu, může se jednat o lepší řešení tohoto přístupu s jeho rychlejší dobou odezvy.

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

## <a name="next-steps"></a>Další kroky

Tento článek vysvětluje, jak analyzátory přispívat k dotazování na problémy a řešení problémů s dotazy. V dalším kroku se podíváme na podrobnější přehled o tom, jaký vliv má analýza na indexování a zpracování dotazů. Zejména zvažte použití rozhraní analyzovat text API k vrácení výstupu s vydanými tokeny, abyste viděli přesně to, co analyzátor vytváří pro váš index.

+ [Analyzátory jazyka](search-language-support.md)
+ [Analyzátory pro zpracování textu v Azure Kognitivní hledání](search-analyzers.md)
+ [Analyzovat textové rozhraní API (REST)](/rest/api/searchservice/test-analyzer)
+ [Jak funguje fulltextové vyhledávání (architektura dotazů)](search-lucene-query-architecture.md)