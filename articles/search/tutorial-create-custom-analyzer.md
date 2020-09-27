---
title: 'Kurz: Vytvoření vlastního analyzátoru'
titleSuffix: Azure Cognitive Search
description: Naučte se, jak vytvořit vlastní analyzátor pro zlepšení kvality výsledků hledání v Azure Kognitivní hledání.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: ac7cee2c1d72b4102fb397aa8093c2d38686fc88
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397262"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>Kurz: Vytvoření vlastního analyzátoru pro telefonní čísla

[Analyzátory](search-analyzers.md) jsou klíčovou součástí v jakémkoli řešení hledání. Pro zlepšení kvality výsledků hledání je důležité pochopit, jak analyzátory fungují a mají vliv na tyto výsledky.

V některých případech, například s bezplatným textovým polem, stačí vybrat správný [analyzátor jazyka](index-add-language-analyzers.md) , čímž dojde ke zlepšení výsledků hledání. Některé scénáře, jako je přesně hledání telefonních čísel, adres URL nebo e-mailů, ale mohou vyžadovat použití vlastních analyzátorů.

V tomto kurzu použijeme post a [rozhraní REST API](/rest/api/searchservice/) služby Azure kognitivní hledání k těmto akcím:

> [!div class="checklist"]
> * Vysvětlit, jak analyzátory fungují
> * Definování vlastního analyzátoru pro vyhledávání telefonních čísel
> * Testování způsobu, jakým vlastní text analyzátoru tokenizes
> * Vytvoření samostatných analyzátorů pro indexování a hledání k dalšímu zlepšení výsledků

## <a name="prerequisites"></a>Požadavky

V tomto kurzu jsou vyžadovány následující služby a nástroje.

+ [Desktopová aplikace Postman](https://www.getpostman.com/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

## <a name="download-files"></a>Stažení souborů

Zdrojový kód pro tento kurz je ve složce [Custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) v úložišti GitHub [Azure-Samples/Azure-Search-post-Samples](https://github.com/Azure-Samples/azure-search-postman-samples) .

## <a name="1---create-azure-cognitive-search-service"></a>1. vytvoření služby Azure Kognitivní hledání

K dokončení tohoto kurzu budete potřebovat službu Azure Kognitivní hledání, kterou můžete [vytvořit na portálu](search-create-service-portal.md). K dokončení tohoto Názorného postupu můžete použít bezplatnou úroveň.

V dalším kroku budete muset znát název vyhledávací služby a jejího klíče rozhraní API. Pokud si nejste jistí, jak tyto položky najít, Projděte si tento [rychlý Start](search-create-service-portal.md#get-a-key-and-url-endpoint).


## <a name="2---set-up-postman"></a>2 – nastavení post

Potom spusťte post a importujte kolekci, kterou jste stáhli z [Azure-Samples/Azure-Search-post-Samples](https://github.com/Azure-Samples/azure-search-postman-samples).

Pokud chcete kolekci importovat, pokračujte na **Files**  >  **Import**souborů a pak vyberte soubor kolekce, který chcete importovat.

Pro každý požadavek budete potřebovat:

1. Nahraďte `<YOUR-SEARCH-SERVICE>` názvem vaší služby Search.

1. Nahraďte `<YOUR-ADMIN-API-KEY>` primárním nebo sekundárním klíčem vaší vyhledávací služby.

  :::image type="content" source="media/search-get-started-postman/postman-url.png" alt-text="Adresa URL a záhlaví žádosti post" border="false":::

Pokud nejste obeznámeni s nástrojem post, přečtěte si téma [prozkoumání rozhraní REST API služby Azure kognitivní hledání pomocí služby post](search-get-started-postman.md).

## <a name="3---create-an-initial-index"></a>3. vytvoření počátečního indexu

V tomto kroku vytvoříme počáteční index, načteme dokumenty do indexu a pak dotaz na dokumenty, abyste zjistili, jak naše prvotní vyhledávání probíhá.

### <a name="create-index"></a>Vytvořit index

Začneme vytvořením jednoduchého indexu s názvem `tutorial-basic-index` dvěma poli: `id` a `phone_number` . Ještě jsme nedefinovali analyzátor, takže se `standard.lucene` analyzátor bude používat ve výchozím nastavení.

K vytvoření indexu pošleme následující žádost:

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>Načtení dat

Dále načteme data do indexu. V některých případech možná nebudete mít kontrolu nad formátem telefonních čísel, která se ingestují, takže budeme testovat s různými druhy formátů. V ideálním případě bude řešení hledání vracet všechna odpovídající telefonní čísla bez ohledu na jejich formát.

Data se načtou do indexu pomocí následujících požadavků: 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

S daty v indexu jsme připraveni začít hledat.

### <a name="search"></a>Search

Aby bylo hledání intuitivní, doporučujeme, abyste uživatelům neočekávali formátování dotazů konkrétním způsobem. Uživatel může hledat `(425) 555-0100` v jakémkoli z formátů, které jsme ukázali výše, a pořád očekává, že budou vráceny výsledky. V tomto kroku otestujeme několik ukázkových dotazů, abyste zjistili, jak se provádějí.

Začneme hledáním `(425) 555-0100` :

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

Tento dotaz vrátí **tři ze čtyř očekávaných výsledků,** ale vrátí také **dva neočekávané výsledky**:

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

Nyní vyhledáme číslo bez formátování. `4255550100`

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

Tento dotaz je ještě horší a vrací jenom **jednu ze čtyř správných shod**.

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

Pokud tyto výsledky zjistíte matoucí, nejste sami. V další části se dig, proč máme tyto výsledky.

## <a name="4---debug-search-results"></a>4 – výsledky hledání ladění

Pro pochopení těchto výsledků hledání je důležité nejprve pochopit, jak analyzátory fungují. Odtud můžeme otestovat výchozí analyzátor pomocí [rozhraní analyzovat text API](/rest/api/searchservice/test-analyzer) a pak vytvořit analyzátor, který splňuje naše potřeby.

### <a name="how-analyzers-work"></a>Jak analyzátory fungují

[Analyzátor](search-analyzers.md) je součást [fulltextového vyhledávacího stroje](search-lucene-query-architecture.md) zodpovědného za zpracování textu v řetězcích dotazů a indexovaných dokumentech. Různé analyzátory zpracovávají text různými způsoby v závislosti na scénáři. V tomto scénáři musíme vytvořit analyzátor přizpůsobený telefonním číslům.

Analyzátory se skládají ze tří součástí:

+ [**Filtry znaků**](#CharFilters) , které odstraňují nebo nahrazují jednotlivé znaky ze vstupního textu.
+ [**Provádějících tokenizaci**](#Tokenizers) , který přeruší vstupní text na tokeny, které se stanou klíči v indexu vyhledávání.
+ [**Filtry tokenů**](#TokenFilters) , které pracují s tokeny vytvořenými provádějících tokenizaci.

V následujícím diagramu vidíte, jak budou tyto tři komponenty spolupracovat, aby tokenizovat větu:

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Diagram procesu analyzátoru, který tokenizovat větu":::

Tyto tokeny se pak ukládají v obráceném indexu, který umožňuje rychlé fulltextové vyhledávání.  Obrácený index umožňuje fulltextové vyhledávání podle mapování všech jedinečných podmínek extrahovaných během lexikální analýzy do dokumentů, ve kterých se vyskytují. Příklad můžete vidět v následujícím diagramu:

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Příklad obráceného indexu":::

Při hledání podmínek uložených v obráceném indexu se objeví vše hledání. Když uživatel vydá dotaz:

1. Dotaz se analyzuje a analyzují se výrazy dotazu.
1. Obrácený index pak vyhledá dokumenty s vyhovujícími podmínkami.
1. Nakonec jsou načtené dokumenty seřazeny podle [algoritmu podobnosti](index-ranking-similarity.md).

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Diagram podobnosti pořadí procesů analyzátoru":::

Pokud se výrazy dotazu neshodují s podmínkami v obráceném indexu, výsledky se nevrátí. Další informace o tom, jak dotazy fungují, najdete v tomto článku pro [fulltextové vyhledávání](search-lucene-query-architecture.md).

> [!Note]
> [Částečné dotazy](search-query-partial-matching.md) jsou důležitou výjimkou tohoto pravidla. Tyto dotazy (dotaz předpony, dotaz na zástupné znaky, regulární dotaz) odcházejí lexikálním procesem analýzy na rozdíl od běžných termických dotazů. Částečné výrazy jsou jenom malé, než se shodují s podmínkami v indexu. Pokud analyzátor není nakonfigurovaný tak, aby podporoval tyto typy dotazů, často obdržíte neočekávané výsledky, protože v indexu neexistují vyhovující výrazy.

### <a name="test-analyzer-using-the-analyze-text-api"></a>Test Analyzer pomocí rozhraní API pro analýzu textu

Azure Kognitivní hledání poskytuje [rozhraní API pro analýzu textu](/rest/api/searchservice/test-analyzer) , které umožňuje testovat analyzátory, abyste porozuměli tomu, jak zpracovávají text.

Rozhraní API pro analýzu textu se nazývá pomocí následujících požadavků:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
      "text": "(425) 555-0100",
      "analyzer": "standard.lucene"
  }
```

Rozhraní API pak vrátí seznam tokenů extrahovaných z textu. Můžete vidět, že standardní nástroj pro Lucene Analyzer rozdělí telefonní číslo na tři samostatné tokeny:

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

Naopak telefonní číslo `4255550100` naformátované bez interpunkce se přihlásí do jediného tokenu.

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

Mějte na paměti, že se analyzují i výrazy dotazů a indexované dokumenty. Když se vrátíte k výsledkům hledání z předchozího kroku, můžeme začít zjistit, proč tyto výsledky byly vráceny.

V prvním dotazu se vrátila nesprávná telefonní čísla, protože jedna z jejich podmínek se `555` shodovala s jednou z vyhledávaných podmínek. Ve druhém dotazu bylo vráceno pouze jedno číslo, protože se jednalo o jediný záznam, který obsahoval termín odpovídající `4255550100` .

## <a name="5---build-a-custom-analyzer"></a>5. vytvoření vlastního analyzátoru

Teď, když rozumíte výsledkům, které vidíme, vytvoříme vlastní analyzátor, abychom vylepšili logiku tokenizace.

Cílem je poskytnout intuitivní hledání proti telefonním číslům bez ohledu na to, v jakém formátu je dotaz nebo indexovaný řetězec. Pro dosažení tohoto výsledku určíme [Filtr znaků](#CharFilters), [provádějících tokenizaci](#Tokenizers)a [Filtr tokenů](#TokenFilters).

<a name="CharFilters"></a>

### <a name="character-filters"></a>Filtry znaků

Filtry znaků slouží ke zpracování textu před jeho odesláním do provádějících tokenizaci. Běžné použití filtrů znaků zahrnuje filtrování prvků HTML nebo nahrazení speciálních znaků.

Pro telefonní čísla chceme odebrat prázdné znaky a speciální znaky, protože ne všechny formáty telefonního čísla obsahují stejné speciální znaky a mezery.

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

Výše uvedený filtr odstraní `-` `(` `)` `+` `.` a mezery ze vstupu.

|Vstup|Výstup|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Tokenizátory musíte nejdřív

Tokenizátory musíte nejdřív rozdělí text na tokeny a zahodí některé znaky, jako je například interpunkční znaménko. V mnoha případech je cílem tokenizace rozdělit větu na jednotlivá slova.

V tomto scénáři použijeme klíčové slovo provádějících tokenizaci, `keyword_v2` , protože chceme zachytit telefonní číslo jako jeden výraz. Všimněte si, že to není jediný způsob, jak tento problém vyřešit. Další informace najdete níže v části věnované [alternativním přístupům](#Alternate) .

Klíčové slovo tokenizátory musíte nejdřív vždycky vypíše stejný text, který byl zadán jako jeden výraz.

|Vstup|Výstup|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>Filtry tokenů

Filtry tokenů odfiltrují nebo upraví tokeny generované provádějících tokenizaci. Jedním z běžných použití filtru tokenů je malé písmeno všech znaků pomocí filtru s malými písmeny. Další běžné použití filtruje stopslova, například, `the` `and` nebo `is` .

I když pro tento scénář nepotřebujeme použít žádný z těchto filtrů, použijeme Filtr tokenu nGram k povolení částečného prohledávání telefonních čísel.

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

[Filtr tokenů nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html) rozdělí tokeny do n-gramů dané velikosti na základě `minGram` `maxGram` parametrů a.

Pro nástroj Phone Analyzer nastavíme `minGram` `3` , že je to nejkratší podřetězec, který očekáváte, že uživatelé budou hledat. `maxGram` je nastavená na, aby se `20` zajistilo, že všechna telefonní čísla i s rozšířeními se budou vejít do jedné n-gramu.

 Unfortunate vedlejším účinkem n-gramů je, že se vrátí některá falešně pozitivní. V kroku 7 tuto chybu vyřešíme vytvořením samostatné analyzátoru pro hledání, která nezahrnuje filtr tokenů n-gramů.

|Vstup|Výstup|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>MBSA

Díky našim filtrům znaků, provádějících tokenizaci a filtrům tokenů jsou na svém místě připravení k definování našeho analyzátoru.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|Vstup|Výstup|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

Všimněte si, že se teď dá vyhledat kterákoli z tokenů ve výstupu. Pokud náš dotaz obsahuje kteroukoli z těchto tokenů, vrátí se telefonní číslo.

Pomocí definovaného vlastního analyzátoru znovu vytvořte index, aby byl vlastní analyzátor dostupný pro testování v dalším kroku. Pro zjednodušení vytvoří kolekce post nový index s názvem `tutorial-first-analyzer` s vámi definovaným analyzátorem.

## <a name="6---test-the-custom-analyzer"></a>6. testování vlastního analyzátoru

Po vytvoření indexu teď můžete testovat analyzátor, který jsme vytvořili, pomocí následujícího požadavku:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

Pak budete moct zobrazit kolekci tokenů vyplývající z telefonního čísla:

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7. vytvoření vlastního analyzátoru pro dotazy

Po provedení některých ukázkových dotazů s indexem pomocí vlastního analyzátoru zjistíte, že se vydaná odvolání zlepšila a vrátí se všechna odpovídající telefonní čísla. Filtr tokenů n-gramů ale způsobí, že se vrátí i některé falešně pozitivních hodnot. Toto je běžné vedlejší účinky filtru tokenů n-gram.

Abychom zabránili falešně pozitivním hodnotám, vytvoříme samostatné analyzátory pro dotazování. Tento analyzátor bude stejný jako analyzátor, který jsme už vytvořili, ale **bez** `custom_ngram_filter` .

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

V definici indexu určíme `indexAnalyzer` a `searchAnalyzer` .

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

V této změně jste nastavili vše. Znovu vytvořte index, Indexujte data a znovu otestujte dotazy, abyste ověřili, jak hledání funguje podle očekávání. Pokud používáte kolekci po, vytvoří se třetí index s názvem `tutorial-second-analyzer` .

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>Alternativní přístupy

Analyzátor výše byl navržený tak, aby maximalizoval flexibilitu při hledání. Nicméně to vede na náklady na uložení mnoha potenciálně neimportovaných podmínek v indexu.

Následující příklad ukazuje jiný analyzátor, který lze použít také pro tento úkol. 

Analyzátor funguje dobře, s výjimkou vstupních dat, jako je například `14255550100` to, že je obtížné logickému bloku telefonního čísla. Analyzátor by například nemohl oddělit kód země, `1` , z kódu oblasti, `425` . Tato nesrovnalost by vedla k tomu, že se výše uvedené číslo nevrátí, pokud uživatel do svého hledání nezahrnul kód země.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

V následujícím příkladu vidíte, že telefonní číslo je rozdělené do bloků dat, které obvykle očekáváte, že uživatel bude hledat.

|Vstup|Výstup|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

V závislosti na vašich požadavcích může být to efektivnější přístup k problému.

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V zájmu jednoduchosti vám tento kurz měl vytvořit tři nové indexy. V počátečních fázích vývoje je však běžné odstraňovat a znovu vytvářet indexy. Index můžete odstranit v Azure Portal nebo pomocí následujícího volání rozhraní API:

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>Shrnutí

V tomto kurzu se ukázal proces vytváření a testování vlastního analyzátoru. Vytvořili jste index, indexovaná data a následně jste dotazováni na index, abyste viděli, jaké výsledky hledání byly vráceny. Odtud jste použili rozhraní analyzovat text API pro zobrazení procesu lexikální analýzy v akci.

I když analyzátor definovaný v tomto kurzu nabízí jednoduché řešení pro vyhledávání na telefonních číslech, můžete stejný postup použít k vytvoření vlastního programu pro telefonický analyzátor pro libovolný scénář, který máte možná.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když pracujete ve vlastním předplatném, je vhodné odebrat prostředky, které už nepotřebujete na konci projektu. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s postupem vytvoření vlastního analyzátoru, se podíváme na všechny různé filtry, tokenizátory musíte nejdřív a analyzátory, které vám budou k dispozici pro vytváření bohatých možností vyhledávání.

> [!div class="nextstepaction"]
> [Vlastní analyzátory v Azure Kognitivní hledání](index-add-custom-analyzers.md)