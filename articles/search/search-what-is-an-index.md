---
title: Vytvoření definice indexu a konceptů
titleSuffix: Azure Cognitive Search
description: Úvod k indexům a konceptům v Azure Kognitivní hledání, včetně součástí komponent a fyzické struktury.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "79282780"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Vytvoření základního indexu v Azure Kognitivní hledání

V Azure Kognitivní hledání je *index* trvalé úložiště *dokumentů* a dalších konstrukcí používaných pro filtrované a fulltextové vyhledávání ve službě Azure kognitivní hledání. V koncepčním dokumentu je dokument jediná jednotka prohledávatelných dat v indexu. Například internetový prodejce může mít dokument pro každou prodanou položku, zatímco tisková agentura může mít dokument pro každý článek atd. Mapování těchto položek do známějších databázových ekvivalentů: *indexů* se koncepčně podobá *tabulce*, kdy *dokumenty* přibližně odpovídají *řádkům* tabulky.

Když přidáte nebo nahrajete index, Azure Kognitivní hledání vytvoří fyzické struktury založené na schématu, které zadáte. Pokud je například pole v indexu označené jako prohledávatelné, vytvoří se pro toto pole obrácený index. Když později přidáte nebo odešlete dokumenty nebo odešlete vyhledávací dotazy do služby Azure Kognitivní hledání, posíláte požadavky do konkrétního indexu ve vyhledávací službě. Načítání polí s hodnotami dokumentu se nazývá *indexování* nebo ingestování dat.

Index můžete vytvořit na portálu, [REST API](search-create-index-rest-api.md)nebo [.NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Doporučený pracovní postup

Doručení do správného návrhu indexu se obvykle dosahuje pomocí několika iterací. Použití kombinace nástrojů a rozhraní API vám pomůže rychle dokončit návrh.

1. Určete, zda můžete použít [indexer](search-indexer-overview.md#supported-data-sources). Pokud jsou externí data jedním z podporovaných zdrojů dat, můžete prototypovat a načíst index pomocí průvodce [**importem dat**](search-import-data-portal.md) .

2. Pokud nemůžete použít **importovaná data**, můžete na [portálu vytvořit počáteční index](search-create-index-portal.md), přidat pole, datové typy a přiřadit atributy pomocí ovládacích prvků na stránce **Přidat index** . Portál zobrazuje, které atributy jsou k dispozici pro různé datové typy. Pokud s návrhem indexu začínáte, je to užitečné.

   ![Přidat stránku indexu zobrazující atributy podle datového typu](media/search-create-index-portal/field-attributes.png "Přidat stránku indexu zobrazující atributy podle datového typu")
  
   Po kliknutí na **vytvořit**se ve vyhledávací službě vytvoří všechny fyzické struktury, které podporují váš index.

3. Stáhněte schéma indexu pomocí [REST API získat index](https://docs.microsoft.com/rest/api/searchservice/get-index) a nástroj pro testování webu, jako je například [post](search-get-started-postman.md). Nyní máte reprezentaci JSON indexu, který jste vytvořili na portálu. 

   V tuto chvíli přecházíte na přístup založený na kódu. Portál není vhodný pro iteraci, protože nemůžete upravit index, který už je vytvořený. Pro zbývající úkoly ale můžete použít možnost post a REST.

4. [Načtěte index s daty](search-what-is-data-import.md). Azure Kognitivní hledání přijímá dokumenty JSON. Chcete-li načíst data prostřednictvím kódu programu, můžete použít příkaz post s dokumenty JSON v datové části požadavku. Pokud vaše data nejsou snadno vyjádřena jako JSON, bude tento krok nejvíce náročný na práci.

5. Dotazujte svůj index, Prohlédněte si výsledky a další iteraci schématu indexu, dokud nezačnete zobrazovat očekávané výsledky. K dotazování indexu můžete použít [**Průzkumníka služby Search**](search-explorer.md) nebo metodu post.

6. Pokračujte v používání kódu k iterování návrhu.  

Vzhledem k tomu, že se ve službě vytvářejí fyzické struktury, je potřeba vyřadit [a znovu vytvořit indexy](search-howto-reindex.md) , když provedete změny v existující definici pole. To znamená, že během vývoje byste měli naplánovat časté časté sestavování. Můžete uvažovat o práci s podmnožinou vašich dat, aby bylo možné znovu sestavit nové sestavení. 

Pro iterativní návrh se doporučuje kód spíše než přístup k portálu. Pokud na definici indexu spoléháte na portál, budete muset při každém opětovném sestavení vyplnit definici indexu. Jako alternativu jsou nástroje, jako je například [post a REST API](search-get-started-postman.md) , užitečné pro testování zkušebního prostředí, když jsou vývojové projekty stále v rané fázi. V těle žádosti můžete provádět přírůstkové změny definice indexu a potom odeslat požadavek službě, aby se index znovu vytvořil pomocí aktualizovaného schématu.

## <a name="components-of-an-index"></a>Součásti indexu

Schématem Azure Kognitivní hledání index se skládá z následujících prvků. 

[*Kolekce polí*](#fields-collection) je obvykle největší částí indexu, kde jsou pojmenovány, zadány a označeny s povoleným chováním, které určují, jak se používají. Mezi další prvky patří [návrhy](#suggesters), [profily vyhodnocování](#scoring-profiles), [analyzátory](#analyzers) s součástmi komponent pro podporu přizpůsobení, [CORS](#cors) a možnosti [šifrovacího klíče](#encryption-key) .

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

Při definování schématu musíte zadat název, typ a atributy každého pole v indexu. Typ pole klasifikuje data, která jsou v tomto poli uložená. Atributy se nastavují u jednotlivých polí a určují, jak se příslušné pole použije. Následující tabulky poskytují výčet typů a atributů, které můžete zadat.

### <a name="data-types"></a>Typy dat
| Typ | Description |
| --- | --- |
| *Edm.String* |Text, který lze volitelně použít pro fulltextové vyhledávání (dělení slov, odvozování a tak dále). |
| *Collection(Edm.String)* |Seznam řetězců, které jde volitelně tokenizovat k fulltextovému hledání. Ačkoli neexistuje žádné teoretické omezení počtu položek v kolekci, na kolekce se vztahuje 16MB omezení velikosti datové části. |
| *Edm.Boolean* |Obsahuje hodnoty true nebo false. |
| *Edm.Int32* |32bitové celočíselné hodnoty. |
| *Edm.Int64* |64bitové celočíselné hodnoty. |
| *Edm.Double* |Číselné údaje s dvojitou přesností. |
| *Edm.DateTimeOffset* |Hodnoty data a času reprezentované ve formátu OData v4 (například `yyyy-MM-ddTHH:mm:ss.fffZ` nebo `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm` ). |
| *Edm.GeographyPoint* |Bod představující geografické umístění na zeměkouli. |

Podrobnější informace o [datových typech podporovaných](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)službou Azure kognitivní hledání najdete tady.

### <a name="index-attributes"></a>Atributy indexu

Právě jedno pole v indexu musí být určené jako **klíčové** pole, které jedinečným způsobem identifikuje každý dokument.

Jiné atributy určují, jak se pole používá v aplikaci. Například atribut s **možností vyhledávání** je přiřazen každému poli, které by mělo být zahrnuto do fulltextového vyhledávání. 

Rozhraní API, která použijete k vytvoření indexu, mají proměnlivé výchozí chování. Pro [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Index)je ve výchozím nastavení povolená většina atributů (například možnost **prohledávatelné** a **retrievable** získatelné hodnoty jsou pro řetězcová pole pravdivá) a často je stačí nastavit, pokud je chcete vypnout. Pro sadu .NET SDK má opak hodnotu true. U jakékoli vlastnosti, kterou explicitně nenastavíte, je ve výchozím nastavení zakázáno odpovídající chování hledání, pokud ho výslovně nepovolíte.

| Atribut | Popis |
| --- | --- |
| `key` |Řetězec obsahující jedinečné ID jednotlivých dokumentů, které slouží k vyhledávání dokumentů. Každý index musí mít jeden klíč. Jenom jedno pole může být klíč a jeho typ musí být nastavený na Edm.String. |
| `retrievable` |Určuje, jestli může být pole vrácené ve výsledku hledání. |
| `filterable` |Umožňuje použít pole ve filtrovacích dotazech. |
| `Sortable` |Umožňuje dotazu seřadit výsledky hledání podle tohoto pole. |
| `facetable` |Umožňuje použití pole ve struktuře [fasetové navigace](search-faceted-navigation.md) k filtrování, které je řízené samotným uživatelem. Jako fasety obvykle nejlépe fungují pole, která obsahují opakované hodnoty použitelné k seskupení více dokumentů (například více dokumentů, které spadají pod jednu značku nebo kategorii služeb). |
| `searchable` |Označí pole jako fulltextově prohledávatelné. |

## <a name="index-size"></a>Velikost indexu

Velikost indexu je určena velikostí dokumentů, které nahráváte, a konfigurací indexu, například bez ohledu na to, zda jste zahrnuli moduly pro návrhy, a jak nastavíte atributy na jednotlivá pole. Následující snímek obrazovky znázorňuje vzory úložiště indexů, které jsou výsledkem různých kombinací atributů.

Index je založený na [integrovaném zdroji ukázkových dat z reálného majetku](search-get-started-portal.md) , který můžete indexovat a dotazovat na portálu. I když nejsou uvedena schémata indexu, můžete odvodit atributy na základě názvu indexu. Index s možností vyhledávání *realestate* má například vybraný atribut, který není k dispozici, a není k dispozici žádný jiný index s *realestate* , který **je v indexu** s **možností** získat a tak dále.

![Velikost indexu na základě výběru atributu](./media/search-what-is-an-index/realestate-index-size.png "Velikost indexu na základě výběru atributu")

I když jsou tyto varianty indexu umělé, můžeme na ně odkazovat, aby bylo možné využít široké porovnání atributů úložiště. Má nastavení **retrievable** načístelné zvýšení velikosti indexu? Ne. Přidávají se pole do **přizpůsobitelné** velikosti indexu? Ano.

Indexy, které podporují filtrování a řazení, jsou proporcionálně větší než ty podporující pouze fulltextové vyhledávání. Filtrování a řazení operací vyhledávání přesných shod, které vyžadují přítomnost nepoškozených dokumentů. Naproti tomu prohledávatelné pole podporující fulltextové a přibližné vyhledávání používá obrácené indexy, které jsou vyplněny pomocí tokenů, které spotřebovávají méně místa než celé dokumenty. 

> [!Note]
> Architektura úložiště se považuje za podrobné informace o implementaci Azure Kognitivní hledání a může se změnit bez předchozího upozornění. V budoucnu není zaručeno, že aktuální chování bude zachováno.

## <a name="suggesters"></a>Moduly pro návrhy
Modul pro návrhy je oddíl schématu, který definuje, která pole v indexu se používají k podpoře automatického dokončování nebo dotazování typu dopředu v hledáních. V případě, že uživatel zadává vyhledávací dotaz a rozhraní API vrací sadu navrhovaných dokumentů nebo frází, jsou obvykle odesílány do [návrhů (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions) částečné vyhledávací řetězce. 

Pole přidaná do modulu pro návrhy se používají k sestavení podmínek vyhledávání typu dopředu. Všechny hledané výrazy se vytvoří při indexování a ukládají se samostatně. Další informace o vytváření struktury návrhů najdete v tématu [Přidání návrhů](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Profily skórování

[Profil vyhodnocování](index-add-scoring-profiles.md) je oddíl schématu, který definuje vlastní bodování chování, které vám umožní ovlivnit, které položky se ve výsledcích hledání objeví výše. Profily vyhodnocování se skládají z vah a funkcí polí. Pokud je chcete použít, zadejte v řetězci dotazu profil podle názvu.

Výchozí profil vyhodnocování funguje na pozadí a vypočítá skóre vyhledávání pro každou položku v sadě výsledků dotazu. Můžete použít interní a nepojmenovaný profil vyhodnocování. Případně můžete nastavit **defaultScoringProfile** na použití vlastního profilu jako výchozí, vyvoláno pokaždé, když se v řetězci dotazu nezadá vlastní profil.

## <a name="analyzers"></a>Analyzátory

Element analyzers nastaví název analyzátoru jazyka, který se má použít pro pole. Další informace o rozsahu analyzátorů, které máte k dispozici, najdete v tématu [Přidání analyzátorů do indexu služby Azure kognitivní hledání](search-analyzers.md). Analyzátory lze použít pouze s prohledávatelnými poli. Jakmile je analyzátor přiřazen k poli, nedá se změnit, dokud index znovu sestavíte.

## <a name="cors"></a>CORS

JavaScript na straně klienta nemůže ve výchozím nastavení volat žádná rozhraní API, protože v prohlížeči se zabrání všechny požadavky mezi zdroji. Pokud chcete pro svůj index povolit dotazy mezi zdroji, povolte CORS (sdílení prostředků mezi zdroji) nastavením atributu **corsOptions** . Z bezpečnostních důvodů podporují CORS jenom rozhraní API pro dotazy. 

Pro CORS se dají nastavit tyto možnosti:

+ **allowedOrigins** (povinné): Jedná se o seznam původních zdrojů, kterým bude udělen přístup k vašemu indexu. To znamená, že jakýkoli JavaScriptový kód, který je obsluhován z těchto původu, bude moci dotazovat váš index (za předpokladu, že poskytuje správný klíč rozhraní API). Každý původ má obvykle tvar, `protocol://<fully-qualified-domain-name>:<port>` i když `<port>` je často vynechán. Další podrobnosti najdete v tématu [sdílení prostředků mezi zdroji (Wikipedii)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

  Pokud chcete povolený přístup ke všem počátkům, zahrňte do `*` pole **allowedOrigins** jako jednu položku. *Toto není doporučený postup pro služby vyhledávání v provozu* , ale je často užitečný pro vývoj a ladění.

+ **maxAgeInSeconds** (volitelné): v prohlížečích se tato hodnota používá k určení doby (v sekundách), po kterou se budou odpovědi na kontrolu mezipaměti CORS ukládat do mezipaměti. Toto musí být nezáporné celé číslo. Čím větší je tato hodnota, tím vyšší bude výkon, tím déle bude trvat, než se změny zásad CORS projeví. Pokud není nastavená, použije se výchozí doba trvání 5 minut.

## <a name="encryption-key"></a>Šifrovací klíč

I když jsou všechny indexy Azure Kognitivní hledání ve výchozím nastavení šifrované pomocí klíčů spravovaných Microsoftem, můžou být indexy nakonfigurované tak, aby se pomocí **klíčů spravovaných zákazníky** v Key Vault šifroval. Další informace najdete v tématu [Správa šifrovacích klíčů v Azure kognitivní hledání](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Další kroky

S porozuměním sestavování indexu můžete na portálu pokračovat a vytvořit svůj první index.

> [!div class="nextstepaction"]
> [Přidat index (portál)](search-create-index-portal.md)
