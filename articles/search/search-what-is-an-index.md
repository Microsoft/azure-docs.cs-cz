---
title: Vytvořit definici indexu a principy – Azure Search
description: Úvod do indexu terminologie a koncepty ve službě Azure Search, včetně součásti a fyzickou strukturu.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.custom: seodec2018
ms.openlocfilehash: addc1a0d7356cf1ba536c7ab47e376a48621e2d9
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342486"
---
# <a name="create-a-basic-index-in-azure-search"></a>Vytvoření základní indexu ve službě Azure Search

Ve službě Azure Search *index* je trvalé úložiště *dokumenty* a jiných objektů, které používá pro vyhledávání filtrované a úplný text na službu Azure Search. Koncepčně dokument je jednou jednotkou prohledávatelných dat v indexu. Například internetový prodejce může mít dokument pro každou prodanou položku, zatímco tisková agentura může mít dokument pro každý článek atd. Mapování těchto položek do známějších databázových ekvivalentů: *indexů* se koncepčně podobá *tabulce*, kdy *dokumenty* přibližně odpovídají *řádkům* tabulky.

Při přidání nebo nahrání indexu Azure Search vytvoří fyzické struktury na základě schématu, které zadáte. Například pokud pole v indexu je označena jako prohledávatelné, vytvoří se pro toto pole obrácenou indexu. Později při přidání nebo odeslat dokumenty nebo odesíláte vyhledávací dotazy do služby Azure Search, jsou odesílání požadavků do konkrétního indexu ve vyhledávací službě. Načítají se pole s hodnotami dokumentu se nazývá *indexování* nebo přijímat data.

Na portálu, můžete vytvořit index [rozhraní REST API](search-create-index-rest-api.md), nebo [sady .NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Doporučený pracovní postup

Při návrhu správné indexu se obvykle dosahuje prostřednictvím několika iterací. Pomocí kombinace nástroje a rozhraní API vám může pomoct rychle dokončete návrh.

1. Zjistěte, jestli můžete použít [indexer](search-indexer-overview.md#supported-data-sources). Pokud vaše externí data je jedním z podporovaných zdrojů dat, můžete si prototypu a načtení indexu pomocí [ **importovat data** ](search-import-data-portal.md) průvodce.

2. Pokud nemůžete použít **importovat data**, můžete ji [vytvoření počátečního indexu na portálu](search-create-index-portal.md), přidávání polí, datových typů a přiřazování atributů s použitím ovládacích prvků na **přidat Index** stránka. Na portálu zobrazuje atributy, které jsou k dispozici pro různé datové typy. Pokud začínáte návrhu indexu, to je užitečné.

   ![Přidat index stránky zobrazující atributy podle datového typu](media/search-create-index-portal/field-attributes.png "přidat index stránky zobrazující atributy podle datového typu")
  
   Po kliknutí na **vytvořit**, všechny fyzické struktury podporuje indexu jsou vytvořeny ve vyhledávací službě.

3. Stáhněte si pomocí schématu indexu [získat Index rozhraní REST API služby](https://docs.microsoft.com/rest/api/searchservice/get-index) a nástroje, jako je testování webového [Postman](search-fiddler.md). Teď máte JSON s reprezentací provedených index, který jste vytvořili na portálu. 

   Přepnutí na přístup založený na kódu v tomto okamžiku. Na portálu není vhodné pro iteraci, protože nelze upravit index, který už je vytvořená. Ale můžete použít nástroje Postman a REST pro zbývající úkoly.

4. [Načtení indexu s daty](search-what-is-data-import.md). Služba Azure Search přijímá dokumentů JSON. Načtení dat prostřednictvím kódu programu, můžete použít Postman s dokumenty JSON v datové části požadavku. Pokud je vaše data se snadno vyjádřená jako dokumenty JSON, bude tento krok nejvíce práce náročné.

5. Dotazování indexu, zkontrolujte výsledky a další iterovat schéma indexu, až začnete zobrazíte přinést očekávané výsledky. Můžete použít [ **Průzkumníka služby Search** ](search-explorer.md) nebo Postman k dotazování indexu.

6. Pokračujte v používání kódu a iterovat návrh.  

Protože fyzické struktury jsou vytvořeny ve službě [vyřadit a znovu vytvořit indexy](search-howto-reindex.md) je nutné pokaždé, když provedete změny materiálu ab stávající definice pole. To znamená, že během vývoje, měli byste počítat s častými znovu sestavovat. Můžete zvážit, práci s použitím podmnožiny data tak, aby znovu sestaví go rychleji. 

Kód, nikoli portálu přístup se doporučuje pro iterativní návrhu. Pokud se spoléháte na portálu pro definici indexu, budete muset vyplnit definice indexu na každý znovu sestavit. Jako alternativu, nástroje, jako je [Postman a rozhraní REST API](search-fiddler.md) jsou užitečné pro testování konceptu testování vývojové projekty jsou nadále v počátečních fázích. Můžete provádět přírůstkové změny definici indexu do textu žádosti a pak odeslat žádost službě pro opětovné vytvoření indexu pomocí aktualizovaného schématu.

## <a name="components-of-an-index"></a>Součásti indexu

Schéma index Azure Search se skládá z následujících elementů. 

[ *Kolekci pole* ](#fields-collection) je obvykle největší část indexu, kde každé pole má název, typu a s atributy s povolenou chování, které určují, jak se používají. Mezi další prvky patří [moduly pro návrhy](#suggesters), [profily skórování](#scoring-profiles), [analyzátory](#analyzers) pomocí součásti podporující přizpůsobení, a [CORS](#cors) Možnosti.

```json
{  
  "name": (optional on PUT; required on POST) "name_of_index",  
  "fields": [  
    {  
      "name": "name_of_field",  
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",  
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),  
      "filterable": true (default) | false,  
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),  
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),  
      "key": true | false (default, only Edm.String fields can be keys),  
      "retrievable": true (default) | false,  
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }  
  ],  
  "suggesters": [  
    {  
      "name": "name of suggester",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": ["field1", "field2", ...]  
    }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "name of scoring profile",  
      "text": (optional, only applies to searchable fields) {  
        "weights": {  
          "searchable_field_name": relative_weight_value (positive #'s),  
          ...  
        }  
      },  
      "functions": (optional) [  
        {  
          "type": "magnitude | freshness | distance | tag",  
          "boost": # (positive number used as multiplier for raw score != 1),  
          "fieldName": "...",  
          "interpolation": "constant | linear (default) | quadratic | logarithmic",  
          "magnitude": {  
            "boostingRangeStart": #,  
            "boostingRangeEnd": #,  
            "constantBoostBeyondRange": true | false (default)  
          },  
          "freshness": {  
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)  
          },  
          "distance": {  
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)  
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)  
          },  
          "tag": {  
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)  
          }  
        }  
      ],  
      "functionAggregation": (optional, applies only when functions are specified)   
        "sum (default) | average | minimum | maximum | firstMatching"  
    }  
  ],  
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) {  
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],  
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)  
  }  
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Atributy pole kolekce a pole

Při definování schématu musíte zadat název, typ a atributy každého pole v indexu. Typ pole klasifikuje data, která jsou v tomto poli uložená. Atributy se nastavují u jednotlivých polí a určují, jak se příslušné pole použije. Následující tabulky poskytují výčet typů a atributů, které můžete zadat.

### <a name="data-types"></a>Typy dat
| Type | Popis |
| --- | --- |
| *Edm.String* |Text, který jde volitelně tokenizovat k fulltextové hledání (dělení slov, rozklad atd). |
| *Collection(Edm.String)* |Seznam řetězců, které jde volitelně tokenizovat k fulltextovému hledání. Ačkoli neexistuje žádné teoretické omezení počtu položek v kolekci, na kolekce se vztahuje 16MB omezení velikosti datové části. |
| *Edm.Boolean* |Obsahuje hodnoty true nebo false. |
| *Edm.Int32* |32bitové celočíselné hodnoty. |
| *Edm.Int64* |64bitové celočíselné hodnoty. |
| *Edm.Double* |Číselné údaje s dvojitou přesností. |
| *Edm.DateTimeOffset* |Hodnoty data a času ve formátu OData V4 (například `yyyy-MM-ddTHH:mm:ss.fffZ` nebo `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Bod představující geografické umístění na zeměkouli. |

Podrobnější informace o [datových typech podporovaných službou Azure Search najdete tady](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="index-attributes"></a>Atributy indexu
| Atribut | Popis |
| --- | --- |
| *Klíč* |Řetězec obsahující jedinečné ID jednotlivých dokumentů, které slouží k vyhledávání dokumentů. Každý index musí mít jeden klíč. Jenom jedno pole může být klíč a jeho typ musí být nastavený na Edm.String. |
| *Retrievable* |Určuje, jestli může být pole vrácené ve výsledku hledání. |
| *Filterable* |Umožňuje použít pole ve filtrovacích dotazech. |
| *Sortable* |Umožňuje dotazu seřadit výsledky hledání podle tohoto pole. |
| *Facetable* |Umožňuje použití pole ve struktuře [fasetové navigace](search-faceted-navigation.md) k filtrování, které je řízené samotným uživatelem. Jako fasety obvykle nejlépe fungují pole, která obsahují opakované hodnoty použitelné k seskupení více dokumentů (například více dokumentů, které spadají pod jednu značku nebo kategorii služeb). |
| *Searchable* |Označí pole jako fulltextově prohledávatelné. |

Podrobnější informace o [atributech indexu služby Azure Search najdete tady](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="storage-implications"></a>Důsledky úložiště

Atributy, které jste vybrali mít vliv na úložiště. Následující snímek obrazovky ukazuje index úložiště vzory vyplývající z různých kombinací atributů.

Index je založen na [ukázkové vestavěné realestate](search-get-started-portal.md) zdroj dat, který může indexování a dotazování na portálu. I když nejsou zobrazeny schémata index, lze odvodit atributů na základě názvu indexu. Například *realestate prohledávatelné* indexu je **prohledávatelné** vybraný atribut a nic jiného, *realestate-retrievable* indexu je  **retrievable** vybraný atribut a nic jiného a podobně.

![Index velikost na základě výběru atributu](./media/search-what-is-an-index/realestate-index-size.png "indexu velikost na základě výběru atributu")

I když tyto varianty index umělé, jsme mohou odkazovat na ně pro široké porovnání vliv úložiště atributů. Nepodporuje nastavení **retrievable** zvýšit velikost indexu? Ne. Přidání pole, která nemá **modulu pro návrhy** zvýšit velikost indexu? Ano.

Indexy, které podporují filtrování a řazení jsou poměrně větší než indexy, které podporují právě fulltextové vyhledávání. Důvodem je filtrování a řazení dotazu na přesné shody, dokumenty se ukládají Internet. Naproti tomu prohledávatelná pole podporují fulltextové a přibližné vyhledávání pomocí obrácený indexy, které plní tokenizovaná podmínky, které spotřebovávají méně místa než celý dokumenty.

> [!Note]
> Architektura úložiště se považuje za podrobnosti implementace služby Azure Search a může změnit bez předchozího upozornění. Není zaručeno, že se v budoucnu zachová aktuální chování.

## <a name="suggesters"></a>Moduly pro návrhy
Modulu pro návrhy je část schéma definující, která pole v indexu se používá pro podporu automatického dokončování nebo našeptávání dotazů při hledání. Obvykle se posílají částečné řetězce [návrhy (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions) uživatele je zadání vyhledávacího dotazu a vrátí sadu navrhovaných sousloví rozhraní API. 

Pole přidá modulu pro návrhy slouží k vytvoření s automatickým dokončováním hledané termíny. Všechny hledané výrazy jsou vytvořeny během indexování a ukládáte odděleně. Další informace o vytvoření struktury modulu pro návrhy najdete v tématu [přidat moduly pro návrhy](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Profily skórování

A [bodovací profil](index-add-scoring-profiles.md) je oddíl, který definuje vlastní schéma vyhodnocování chování, které umožňují můžete ovlivnit položky, které se zobrazovat na vyšších ve výsledcích hledání. Profily vyhodnocování jsou tvořené váhu pole a funkce. K jejich použití, můžete zadat profil podle názvu v řetězci dotazu.

Výchozí bodovací profil pracuje na pozadí pro výpočet skóre vyhledávání pro každou položku v sadě výsledků. Můžete použít interní, nepojmenované bodovací profil. Můžete také nastavit **defaultScoringProfile** použít vlastní profil jako výchozí, vyvolána pokaždé, když vlastní profil, který není zadaná v řetězci dotazu.

## <a name="analyzers"></a>Analyzátory

Element analyzátory nastaví název analyzátoru jazyka pro pole. Další informace o rozsahu analyzátory, které jsou k dispozici, najdete v tématu [přidání analyzátory do indexu Azure Search](search-analyzers.md). Analyzátory jde použít jenom s prohledávatelná pole. Až Analyzátor je přiřazený poli, nelze je změnit, dokud je znovu sestavit index.

## <a name="cors"></a>CORS

JavaScript na straně klienta nelze volat libovolné rozhraní API ve výchozím nastavení, protože prohlížeče, nebude moct všechny požadavky cross-origin. Mezi zdroji dotazy na index, povolit CORS (sdílení prostředků různého původu) tak, že nastavíte **corsOptions** atribut. Z bezpečnostních důvodů se podporují pouze dotaz rozhraní API CORS. 

Následující možnosti můžete nastavit pro CORS:

+ **allowedOrigins** (povinné): Toto je seznam původů, které bude udělen přístup k indexu. To znamená, že jakýkoli kód jazyka JavaScript obsluhovat z těchto zdrojů se bude moct dotazování indexu (za předpokladu, že poskytuje správný klíč api-key). Každý původ je obvykle ve formátu `protocol://<fully-qualified-domain-name>:<port>` i když `<port>` je často vynechán. Zobrazit [(Wikipedia) sdílení prostředků různého původu](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) další podrobnosti.

  Pokud chcete povolit přístup pro všechny zdroje, zahrnují `*` jako jedna položka v **allowedOrigins** pole. *Toto nastavení nedoporučujeme normy pro produkční vyhledávací služby* ale často je užitečné pro vývoj a ladění.

+ **maxAgeInSeconds** (optional): Prohlížeče tato hodnota slouží k určení doby trvání (v sekundách) pro předběžných odpovědí CORS mezipaměti. Musí se jednat o nezáporné celé číslo. Čím větší je tato hodnota, bude lepší výkon, ale tím déle bude taky trvat změny zásad CORS se projeví. Pokud není nastavená, použije se výchozí hodnota 5 minut.

## <a name="next-steps"></a>Další postup

Potom co porozumíte kompozice indexu, můžete pokračovat v portálu k vytvoření prvního indexu služby.

> [!div class="nextstepaction"]
> [Přidat indexu (portál)](search-create-index-portal.md)