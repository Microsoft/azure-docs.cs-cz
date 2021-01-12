---
title: Vytvoření vyhledávacího indexu
titleSuffix: Azure Cognitive Search
description: Zavádí koncepty a nástroje indexování v Azure Kognitivní hledání, včetně definic schémat a fyzické struktury dat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 3d5663177bb087e936a49dd7289659b684d85860
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116190"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>Vytvoření základního vyhledávacího indexu v Azure Kognitivní hledání

V Azure Kognitivní hledání *vyhledávací rejstřík* ukládá prohledávatelný obsah, který se používá pro fulltextové a filtrované dotazy. Index je definován schématem a uložený do služby, přičemž import dat je uvedený jako druhý krok. 

Indexy obsahují *dokumenty*. V koncepčním dokumentu je dokument jediná jednotka prohledávatelných dat v indexu. Prodejce může mít dokument pro každý produkt, organizace zpráv může mít dokument pro každý článek a tak dále. Mapování těchto konceptů na podrobnější ekvivalenty databáze: *Index vyhledávání* se rovná *tabulce* a *dokumenty* zhruba odpovídají *řádkům* v tabulce.

Fyzická struktura indexu je určena schématem s poli, která jsou označena jako "prohledávatelné", což má za následek obrácený index vytvořený pro toto pole. 

Index můžete vytvořit pomocí následujících nástrojů a rozhraní API:

* V Azure Portal použijte průvodce **přidáním indexu** nebo **importovat data** .
* Použití [indexu Create (REST API)](/rest/api/searchservice/create-index)
* Používání [sady .NET SDK](./search-get-started-dotnet.md)

Seznamte se s portálem, který se snadno učí. Portál vynutil požadavky a pravidla schématu pro konkrétní datové typy, jako je například zákaz funkcí fulltextového vyhledávání u číselných polí. Jakmile budete mít index s příznakem, můžete přejít ke kódu načtením definice JSON ze služby pomocí [Get index (REST API)](/rest/api/searchservice/get-index) a jeho přidáním do vašeho řešení.

## <a name="recommended-workflow"></a>Doporučený pracovní postup

Doručení v konečném návrhu indexu je iterativní proces. Je běžné začít s portálem pro vytvoření počátečního indexu a pak přepnout na kód, který umístí index do správy zdrojových kódů.

1. Určete, jestli můžete použít [**importovaná data**](search-import-data-portal.md). Průvodce provede indexování na základě indexeru, pokud zdrojová data pochází z [podporovaného typu zdroje dat v Azure](search-indexer-overview.md#supported-data-sources).

1. Pokud nemůžete použít **importovaná data**, začněte s **přidáním indexu** pro definování schématu.

   ![Přidat index – příkaz](media/search-what-is-an-index/add-index.png "Přidat index – příkaz")

1. Zadejte název a klíč, který slouží k jednoznačné identifikaci každého hledaného dokumentu v indexu. Klíč je povinný a musí být typu EDM. String. Během importu byste měli naplánovat mapování jedinečného pole ve zdrojových datech do tohoto pole. 

   Portál poskytuje `id` pole pro klíč. Pokud chcete výchozí hodnotu přepsat `id` , vytvořte nové pole (například definice nové pole nazvané `HotelId` ) a pak ho vyberte v **klíči**.

   ![Vyplnit požadované vlastnosti](media/search-what-is-an-index//field-attributes.png "Vyplnit požadované vlastnosti")

1. Přidejte další pole. Portál zobrazuje, které [atributy pole](#index-attributes) jsou k dispozici pro různé datové typy. Pokud s návrhem indexu začínáte, je to užitečné.

   Pokud jsou příchozí data hierarchicky v podstatě, přiřaďte datový typ [komplexní typ](search-howto-complex-data-types.md) , který bude představovat vnořené struktury. Integrovaná Ukázková sada dat, hotely, znázorňuje komplexní typy pomocí adresy (obsahuje několik dílčích polí), které mají vztah 1:1 s každým hotelem, a prostorově komplexní kolekci, kde je k jednotlivým hotelům přidruženo více místností. 

1. Před vytvořením indexu přiřaďte všechny [analyzátory](#analyzers) k polím řetězců. To samé proveďte pro moduly pro [návrhy](#suggesters) , pokud chcete povolit automatické dokončování u konkrétních polí.

1. Kliknutím na **vytvořit** Sestavte fyzické struktury ve vyhledávací službě.

1. Po vytvoření indexu můžete pomocí dalších příkazů zkontrolovat definice nebo přidat další prvky.

   ![Přidat stránku indexu zobrazující atributy podle datového typu](media/search-what-is-an-index//field-definitions.png "Přidat stránku indexu zobrazující atributy podle datového typu")

1. Stáhněte schéma indexu pomocí [Get index (REST API)](/rest/api/searchservice/get-index) a nástroje pro testování webu, jako je například [post](search-get-started-rest.md). Nyní máte reprezentaci JSON indexu, kterou můžete přizpůsobit pro kód.

1. [Načtěte index s daty](search-what-is-data-import.md). Azure Kognitivní hledání přijímá dokumenty JSON. Chcete-li načíst data prostřednictvím kódu programu, můžete použít příkaz post s dokumenty JSON v datové části požadavku. Pokud vaše data nejsou snadno vyjádřena jako JSON, bude tento krok nejvíce náročný na práci. 

    Po načtení indexu s daty většina úprav existujících polí bude vyžadovat, abyste index vyřadíte a znovu sestavili.

1. Dotazujte svůj index, Prohlédněte si výsledky a další iteraci schématu indexu, dokud nezačnete zobrazovat očekávané výsledky. K dotazování indexu můžete použít [**Průzkumníka služby Search**](search-explorer.md) nebo metodu post.

Během vývoje si naplánujte časté časté sestavování. Vzhledem k tomu, že se ve službě vytvářejí fyzické struktury, je nutné vyřadit [a znovu vytvořit indexy](search-howto-reindex.md) pro většinu úprav existující definice pole. Můžete uvažovat o práci s podmnožinou vašich dat, aby bylo možné znovu sestavit nové sestavení. 

> [!Tip]
> Pro práci na návrhu indexu a importu dat se doporučuje kód spíše než přístup k portálu. Jako alternativu jsou nástroje, jako je například [post](search-get-started-rest.md) nebo [Visual Studio Code](search-get-started-vs-code.md) , užitečné pro testování zkušebního prostředí, když jsou vývojové projekty stále v rané fázi. V těle žádosti můžete provádět přírůstkové změny definice indexu a potom odeslat požadavek službě, aby se index znovu vytvořil pomocí aktualizovaného schématu.

## <a name="index-schema"></a>Schéma indexu

Index musí mít název a jedno označené pole klíče (typu EDM. String) v kolekci Fields. [*Kolekce polí*](#fields-collection) je obvykle největší částí indexu, kde jsou pojmenovány, zadány a označeny s povoleným chováním, které určují, jak se používají. 

[Mezi další prvky patří moduly](#suggesters)pro [vyhodnocování, profily vyhodnocování](#scoringprofiles) [, používané ke](#analyzers) zpracování řetězců do tokenů podle jazykových pravidel nebo jiných vlastností podporovaných analyzátorem a nastavení [vzdáleného skriptování (CORS) mezi zdroji](#corsoptions) .

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
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Atributy kolekce polí a polí

Pole mají název, typ, který klasifikuje uložená data a atributy, které určují, jak se pole používá.

### <a name="data-types"></a>Typy dat

| Typ | Description |
|------|-------------|
| Edm.String |Text, který lze volitelně použít pro fulltextové vyhledávání (dělení slov, odvozování a tak dále). |
| Collection(Edm.String) |Seznam řetězců, které jde volitelně tokenizovat k fulltextovému hledání. Ačkoli neexistuje žádné teoretické omezení počtu položek v kolekci, na kolekce se vztahuje 16MB omezení velikosti datové části. |
| Edm.Boolean |Obsahuje hodnoty true nebo false. |
| Edm.Int32 |32bitové celočíselné hodnoty. |
| Edm.Int64 |64bitové celočíselné hodnoty. |
| Edm.Double |Číselné údaje s dvojitou přesností. |
| Edm.DateTimeOffset |Hodnoty data a času reprezentované ve formátu OData v4 (například `yyyy-MM-ddTHH:mm:ss.fffZ` nebo `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm` ). |
| Edm.GeographyPoint |Bod představující geografické umístění na zeměkouli. |

Další informace najdete v tématu [podporované datové typy](/rest/api/searchservice/Supported-data-types).

<a name="index-attributes"></a>

### <a name="attributes"></a>Atributy

Atributy polí určují, jak se pole používá, například jestli se používá ve fulltextovém vyhledávání, fasetové navigaci, operacích řazení a tak dále. 

Pole řetězců jsou často označena jako "prohledávatelné" a "získatelné". Mezi pole, která se používají k zúžení výsledků hledání, patří "seřaditelné", "filtrovatelné" a "FACET".

|Atribut|Popis|  
|---------------|-----------------|  
|prohledávatelná |Fulltextově prohledávatelné, lze provést lexikální analýzu, jako je dělení slov během indexování. Pokud nastavíte prohledávatelné pole na hodnotu jako „slunečný den“, interně se rozdělí na jednotlivé tokeny „slunečný“ a „den“. Podrobnosti najdete v článku [Jak funguje fulltextové vyhledávání](search-lucene-query-architecture.md).|  
|Filterable |Odkazované v dotazech $filter. Ve filtrovatelných polích typu `Edm.String` nebo `Collection(Edm.String)` nejdou dělit slova, takže se dají porovnávat jenom na přesné shody. Pokud například nastavíte takové pole f na „sunny day“, `$filter=f eq 'sunny'` nenajde žádné shody, ale `$filter=f eq 'sunny day'` ano. |  
|seřaditelné |Ve výchozím nastavení systém řadí výsledky podle skóre (bodů), můžete ale nakonfigurovat řazení na základě polí v dokumentech. Pole typu `Collection(Edm.String)` nelze seřadit. |  
|kategorizovatelné |Obvykle se používá v prezentaci výsledků hledání, která obsahuje počet nalezených položek podle kategorie (například hotely v konkrétním městě). Tuto možnost nejde použít s poli typu `Edm.GeographyPoint`. Pole typu `Edm.String` , která jsou filtrovatelné, "seřaditelné" nebo "FACET" mohou mít délku maximálně 32 kilobajtů. Podrobnosti najdete v článku [Vytvoření indexu (REST API)](/rest/api/searchservice/create-index).|  
|zkrat |Jedinečný identifikátor pro dokumenty v indexu. Jako pole key se musí zvolit právě jedno pole a musí být typu `Edm.String`.|  
|Retrievable |Určuje, jestli může být pole vrácené ve výsledku hledání. To je užitečné, když chcete použít pole (například *zisková marže*) jako filtrovací, řadicí a bodovací mechanismus, ale nechcete, aby pole bylo viditelné pro koncového uživatele. Tento atribut musí být `true` pro pole typu `key`.|  

I když můžete nová pole přidat kdykoliv, jsou existující definice polí zamknuté v indexu po dobu jeho existence. Z tohoto důvodu vývojáři obvykle používají portál k vytváření jednoduchých indexů, testování nápadů nebo k vyhledání nastavení pomocí stránek portálu. Časté změny návrhu indexu jsou efektivnější, pokud budete postupovat pomocí kódu, aby bylo možné index snadno znovu sestavit.

> [!NOTE]
> Rozhraní API, která použijete k vytvoření indexu, mají proměnlivé výchozí chování. Pro [rozhraní REST API](/rest/api/searchservice/Create-Index)je ve výchozím nastavení povolená většina atributů (například "prohledávatelné" a "získatelné" jsou pro řetězcová pole pravdivá) a často je stačí nastavit, jenom pokud je chcete vypnout. Pro sadu .NET SDK má opak hodnotu true. U jakékoli vlastnosti, kterou explicitně nenastavíte, je ve výchozím nastavení zakázáno odpovídající chování hledání, pokud ho výslovně nepovolíte.

## `analyzers`

Element analyzers nastaví název analyzátoru jazyka, který se má použít pro pole. Další informace o rozsahu analyzátorů, které máte k dispozici, najdete v tématu [Přidání analyzátorů do indexu služby Azure kognitivní hledání](search-analyzers.md). Analyzátory lze použít pouze s prohledávatelnými poli. Jakmile je analyzátor přiřazen k poli, nedá se změnit, dokud index znovu sestavíte.

## `suggesters`

Modul pro návrhy je oddíl schématu, který definuje, která pole v indexu se používají k podpoře automatického dokončování nebo dotazování typu dopředu v hledáních. V případě, že uživatel zadává vyhledávací dotaz a rozhraní API vrací sadu navrhovaných dokumentů nebo frází, jsou obvykle odesílány do [návrhů (REST API)](/rest/api/searchservice/suggestions) částečné vyhledávací řetězce. 

Pole přidaná do modulu pro návrhy se používají k sestavení podmínek vyhledávání typu dopředu. Všechny hledané výrazy se vytvoří při indexování a ukládají se samostatně. Další informace o vytváření struktury návrhů najdete v tématu [Přidání návrhů](index-add-suggesters.md).

## `corsOptions`

JavaScript na straně klienta nemůže ve výchozím nastavení volat žádná rozhraní API, protože v prohlížeči se zabrání všechny požadavky mezi zdroji. Pokud chcete pro svůj index povolit dotazy mezi zdroji, povolte CORS (sdílení prostředků mezi zdroji) nastavením atributu **corsOptions** . Z bezpečnostních důvodů podporují CORS jenom rozhraní API pro dotazy. 

Pro CORS se dají nastavit tyto možnosti:

+ **allowedOrigins** (povinné): Jedná se o seznam původních zdrojů, kterým bude udělen přístup k vašemu indexu. To znamená, že jakýkoli JavaScriptový kód, který je obsluhován z těchto původu, bude moci dotazovat váš index (za předpokladu, že poskytuje správný klíč rozhraní API). Každý původ má obvykle tvar, `protocol://<fully-qualified-domain-name>:<port>` i když `<port>` je často vynechán. Další podrobnosti najdete v tématu [sdílení prostředků mezi zdroji (Wikipedii)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

  Pokud chcete povolený přístup ke všem počátkům, zahrňte do `*` pole **allowedOrigins** jako jednu položku. *Toto není doporučený postup pro služby vyhledávání v provozu* , ale je často užitečný pro vývoj a ladění.

+ **maxAgeInSeconds** (volitelné): v prohlížečích se tato hodnota používá k určení doby (v sekundách), po kterou se budou odpovědi na kontrolu mezipaměti CORS ukládat do mezipaměti. Toto musí být nezáporné celé číslo. Čím větší je tato hodnota, tím vyšší bude výkon, tím déle bude trvat, než se změny zásad CORS projeví. Pokud není nastavená, použije se výchozí doba trvání 5 minut.

## `scoringProfiles`

[Profil vyhodnocování](index-add-scoring-profiles.md) je oddíl schématu, který definuje vlastní bodování chování, které vám umožní ovlivnit, které položky se ve výsledcích hledání objeví výše. Profily vyhodnocování se skládají z vah a funkcí polí. Pokud je chcete použít, zadejte v řetězci dotazu profil podle názvu.

Výchozí profil vyhodnocování funguje na pozadí a vypočítá skóre vyhledávání pro každou položku v sadě výsledků dotazu. Můžete použít interní a nepojmenovaný profil vyhodnocování. Případně můžete nastavit **defaultScoringProfile** na použití vlastního profilu jako výchozí, vyvoláno pokaždé, když se v řetězci dotazu nezadá vlastní profil.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Atributy a velikost indexu (důsledky úložiště)

Velikost indexu je určena velikostí dokumentů, které nahráváte, a konfigurací indexu, například bez ohledu na to, zda jste zahrnuli moduly pro návrhy, a jak nastavíte atributy na jednotlivá pole. 

Následující snímek obrazovky znázorňuje vzory úložiště indexů, které jsou výsledkem různých kombinací atributů. Index je založený na **indexu ukázek reálného majetku**, který můžete snadno vytvořit pomocí Průvodce importem dat. I když nejsou uvedena schémata indexu, můžete odvodit atributy na základě názvu indexu. Index *s možností prohledávání realestate* má například vybraný atribut "prohledávatelné" a nic jiného, index *realestate* má vybraný atribut "získatelné" a nic jiného a tak dále.

![Velikost indexu na základě výběru atributu](./media/search-what-is-an-index/realestate-index-size.png "Velikost indexu na základě výběru atributu")

I když jsou tyto varianty indexu umělé, můžeme na ně odkazovat, aby bylo možné využít široké porovnání atributů úložiště. Nastavím možnost "získatelné" zvýšit velikost indexu? No. Přidávají se pole do **přizpůsobitelné** velikosti indexu? Ano.

Indexy, které podporují filtrování a řazení, jsou proporcionálně větší než indexy podporující pouze fulltextové vyhledávání. Důvodem je to, že operace filtrování a řazení prohledají přesné shody a vyžadují přítomnost doslovnéch textových řetězců. Naopak vyhledávací pole podporující fulltextové dotazy používají obrácené indexy, které jsou vyplněny pomocí tokenů, které spotřebovávají méně místa než celé dokumenty. 

> [!Note]
> Architektura úložiště se považuje za podrobné informace o implementaci Azure Kognitivní hledání a může se změnit bez předchozího upozornění. V budoucnu není zaručeno, že aktuální chování bude zachováno.

## <a name="next-steps"></a>Další kroky

S porozuměním sestavování indexu můžete na portálu pokračovat a vytvořit svůj první index. Doporučujeme, abyste začali s průvodcem **importem dat** , a to tak, že zvolíte buď zdroje dat v *realestate-US-Sample* nebo *hotely-Sample* .

> [!div class="nextstepaction"]
> [Průvodce importem dat (portál)](search-get-started-portal.md)

V obou datových sadách může průvodce odvodit schéma indexu, importovat data a výstupem prohledávatelné indexu, který můžete dotazovat pomocí Průzkumníka služby Search. Tyto zdroje dat najdete na stránce **připojit k datům** v průvodci **importem dat** .

   ![Vytvoření ukázkového indexu](media/search-what-is-an-index//import-wizard-sample-data.png "Vytvoření ukázkového indexu")