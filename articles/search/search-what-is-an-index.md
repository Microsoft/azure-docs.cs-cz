---
title: Vytvoření definice indexu a konceptů
titleSuffix: Azure Cognitive Search
description: Úvod k indexovým termínům a konceptům v Azure Cognitive Search, včetně součástí a fyzické struktury.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282780"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Vytvoření základního indexu v Azure Cognitive Search

V Azure Cognitive Search *je index* trvalým úložištěm *dokumentů* a dalších konstrukcí používaných pro filtrované a fulltextové vyhledávání ve službě Azure Cognitive Search. Koncepčně je dokument jedinou jednotkou prohledávatelných dat v indexu. Například internetový prodejce může mít dokument pro každou prodanou položku, zatímco tisková agentura může mít dokument pro každý článek atd. Mapování těchto položek do známějších databázových ekvivalentů: *indexů* se koncepčně podobá *tabulce*, kdy *dokumenty* přibližně odpovídají *řádkům* tabulky.

Když přidáte nebo nahrajete index, Azure Cognitive Search vytvoří fyzické struktury založené na schématu, které zadáte. Pokud je například pole v indexu označeno jako prohledávatelné, vytvoří se pro toto pole obrácený index. Později při přidání nebo nahrání dokumentů nebo odeslání vyhledávacích dotazů do Azure Cognitive Search odesíláte požadavky na konkrétní index ve vyhledávací službě. Načítání polí s hodnotami dokumentu se nazývá *indexování* nebo přijímání dat.

Index můžete vytvořit na portálu, [rozhraní REST API](search-create-index-rest-api.md)nebo [.NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Doporučený pracovní postup

Příchod na správný návrh indexu je obvykle dosaženo prostřednictvím více iterací. Použití kombinace nástrojů a rozhraní API vám pomůže rychle dokončit návrh.

1. Určete, zda lze použít [indexer](search-indexer-overview.md#supported-data-sources). Pokud jsou externí data jedním z podporovaných zdrojů dat, můžete pomocí Průvodce [**importem dat**](search-import-data-portal.md) použít prototyp a načíst index.

2. Pokud nemůžete použít **import dat**, můžete na [portálu vytvořit počáteční index](search-create-index-portal.md), přidávat pole, datové typy a přiřazovat atributy pomocí ovládacích prvků na stránce Přidat **index.** Portál ukazuje, které atributy jsou k dispozici pro různé datové typy. Pokud s návrhem indexu tečujete, je to užitečné.

   ![Přidat stránku rejstříku zobrazující atributy podle typu dat](media/search-create-index-portal/field-attributes.png "Přidat stránku rejstříku zobrazující atributy podle typu dat")
  
   Po klepnutí na tlačítko **Vytvořit**se ve vyhledávací službě vytvoří všechny fyzické struktury podporující index.

3. Stáhněte si schéma indexu pomocí [rozhraní Get Index REST API](https://docs.microsoft.com/rest/api/searchservice/get-index) a webového testovacího nástroje, jako je [Postman](search-get-started-postman.md). Nyní máte json reprezentace indexu, který jste vytvořili na portálu. 

   V tomto okamžiku přecházíte na přístup založený na kódu. Portál není vhodný pro iteraci, protože nelze upravit index, který je již vytvořen. Ale můžete použít Postman a REST pro zbývající úkoly.

4. [Načtěte index s daty](search-what-is-data-import.md). Azure Cognitive Search přijímá dokumenty JSON. Chcete-li data načíst programově, můžete použít Postman s dokumenty JSON v datové části požadavku. Pokud vaše data nejsou snadno vyjádřit jako JSON, tento krok bude nejvíce náročné na práci.

5. Dotaz indexu, zkontrolujte výsledky a dále iterate na schéma indexu, dokud nezačnete vidět výsledky, které očekáváte. K [**dotazování**](search-explorer.md) indexu můžete použít Průzkumník vyhledávání nebo Pošťák.

6. Pokračujte v používání kódu k itetování nad návrhem.  

Vzhledem k tomu, že fyzické struktury jsou vytvořeny ve službě, [uvolněním a opětovné vytvoření indexy](search-howto-reindex.md) je nezbytné vždy, když provedete změny materiálu na existující definice pole. To znamená, že během vývoje byste měli plánovat časté obnovy. Můžete zvážit práci s podmnožinou dat, aby se obnovení urychlilo. 

Pro iterativní návrh se doporučuje kód, nikoli přístup portálu. Pokud se spoléháte na portál pro definici indexu, budete muset vyplnit definici indexu při každém opětovném sestavení. Alternativně nástroje jako [Postman a rozhraní REST API](search-get-started-postman.md) jsou užitečné pro testování proof-of-concept, když jsou projekty vývoje stále v počátečních fázích. Můžete provést přírůstkové změny definice indexu v těle požadavku a potom odeslat požadavek na službu znovu vytvořit index pomocí aktualizovaného schématu.

## <a name="components-of-an-index"></a>Součásti indexu

Schematicky index Azure Cognitive Search se skládá z následujících prvků. 

[*Kolekce polí*](#fields-collection) je obvykle největší část indexu, kde každé pole je pojmenováno, zadáno a přiřazeno s povoleným chováním, které určuje způsob jeho použití. Mezi další prvky patří [návrhy](#suggesters), [profily hodnocení](#scoring-profiles), [analyzátory](#analyzers) s komponentami pro podporu přizpůsobení, [CORS](#cors) a možnosti [šifrovacího klíče.](#encryption-key)

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
| Typ | Popis |
| --- | --- |
| *Edm.String* |Text, který může být volitelně tokenizován pro fulltextové vyhledávání (rozdělení slov, vyplývající a tak dále). |
| *Collection(Edm.String)* |Seznam řetězců, které jde volitelně tokenizovat k fulltextovému hledání. Ačkoli neexistuje žádné teoretické omezení počtu položek v kolekci, na kolekce se vztahuje 16MB omezení velikosti datové části. |
| *Edm.Boolean* |Obsahuje hodnoty true nebo false. |
| *Edm.Int32* |32bitové celočíselné hodnoty. |
| *Edm.Int64* |64bitové celočíselné hodnoty. |
| *Edm.Double* |Číselné údaje s dvojitou přesností. |
| *Edm.DateTimeOffset* |Hodnoty data času reprezentované ve formátu `yyyy-MM-ddTHH:mm:ss.fffZ` `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`OData V4 (například nebo ). |
| *Edm.GeographyPoint* |Bod představující geografické umístění na zeměkouli. |

Podrobnější informace o [podporovaných datových typech](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)Azure Cognitive Search najdete zde .

### <a name="index-attributes"></a>Atributy indexu

Přesně jedno pole v indexu musí být označeno jako **klíčové** pole, které jednoznačně identifikuje každý dokument.

Jiné atributy určují, jak se pole používá v aplikaci. Například **prohledávatelný** atribut je přiřazen ke každému poli, které by mělo být zahrnuto do fulltextového vyhledávání. 

Api, která používáte k vytvoření indexu, mají různé výchozí chování. Pro [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Index), většina atributů jsou povoleny ve výchozím nastavení (například **prohledávatelné** a **retrievable** jsou pravdivé pro řetězcová pole) a často stačí je nastavit, pokud je chcete vypnout. Pro .NET SDK opak je pravdou. U všech vlastností, které explicitně nenastavíte, je ve výchozím nastavení zakázáno odpovídající chování hledání, pokud jej výslovně nepovolíte.

| Atribut | Popis |
| --- | --- |
| `key` |Řetězec obsahující jedinečné ID jednotlivých dokumentů, které slouží k vyhledávání dokumentů. Každý index musí mít jeden klíč. Jenom jedno pole může být klíč a jeho typ musí být nastavený na Edm.String. |
| `retrievable` |Určuje, jestli může být pole vrácené ve výsledku hledání. |
| `filterable` |Umožňuje použít pole ve filtrovacích dotazech. |
| `Sortable` |Umožňuje dotazu seřadit výsledky hledání podle tohoto pole. |
| `facetable` |Umožňuje použití pole ve struktuře [fasetové navigace](search-faceted-navigation.md) k filtrování, které je řízené samotným uživatelem. Jako fasety obvykle nejlépe fungují pole, která obsahují opakované hodnoty použitelné k seskupení více dokumentů (například více dokumentů, které spadají pod jednu značku nebo kategorii služeb). |
| `searchable` |Označí pole jako fulltextově prohledávatelné. |

## <a name="index-size"></a>Velikost indexu

Velikost indexu je určena velikostí dokumentů, které nahrajete, a konfigurací indexu, například zda zahrnete návrhy a jak nastavíte atributy pro jednotlivá pole. Následující snímek obrazovky ilustruje vzory úložiště indexu vyplývající z různých kombinací atributů.

Index je založen na [vestavěném zdroji dat ukázkového zdroje nemovitostí,](search-get-started-portal.md) který můžete indexovat a dotazovat na portálu. Přestože schémata indexu nejsou zobrazeny, můžete odvodit atributy na základě názvu indexu. Například index *s možností vyhledávání v reálném provozu* má vybraný atribut pro **vyhledávání** a nic jiného, index s možností *vyhledávání nemovitostí* má vybraný atribut **retrievable** a nic jiného a tak dále.

![Velikost indexu na základě výběru atributu](./media/search-what-is-an-index/realestate-index-size.png "Velikost indexu na základě výběru atributu")

I když jsou tyto varianty indexu umělé, můžeme na ně odkazovat pro široké porovnání toho, jak atributy ovlivňují úložiště. Zvyšuje nastavení **načtení** velikostindexu? Ne. Zvyšuje přidání polí do **sunatoru** velikost indexu? Ano.

Indexy, které podporují filtrování a řazení jsou proporcionálně větší než indexy podporující pouze fulltextové vyhledávání. Filtrování a řazení operací hledat přesné shody, které vyžadují přítomnost neporušené dokumenty. Naproti tomu prohledávatelná pole podporující fulltextové a přibližné hledání používají invertované indexy, které jsou naplněny tokenizovanými termíny, které spotřebovávají méně místa než celé dokumenty. 

> [!Note]
> Architektura úložiště je považována za podrobnosti implementace Azure Cognitive Search a může změnit bez předchozího upozornění. Neexistuje žádná záruka, že současné chování bude přetrvávat i v budoucnu.

## <a name="suggesters"></a>Moduly pro návrhy
Návrhovač je část schématu, která definuje, která pole v indexu se používají pro podporu automatického dokončování nebo dotazů typu dopředu při vyhledávání. Obvykle částečné vyhledávací řetězce jsou odesílány [návrhy (ROZHRANÍ REST API),](https://docs.microsoft.com/rest/api/searchservice/suggestions) zatímco uživatel zadává vyhledávací dotaz a rozhraní API vrátí sadu navržených dokumentů nebo frází. 

Pole přidaná do návrhu se používají k vytváření hledaných výrazů typu dopředu. Všechny hledané termíny jsou vytvořeny během indexování a uloženy samostatně. Další informace o vytvoření struktury návrhu naleznete v tématu [Přidání návrhovek](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Profily skórování

[Profil hodnocení](index-add-scoring-profiles.md) je část schématu, která definuje vlastní chování hodnocení, které umožňuje ovlivnit, které položky se zobrazí vyšší ve výsledcích hledání. Profily hodnocení se shodnou z polí závaží a funkcí. Chcete-li je použít, zadejte profil podle názvu v řetězci dotazu.

Výchozí profil hodnocení pracuje na pozadí vypočítat skóre hledání pro každou položku v sadě výsledků. Můžete použít interní, nepojmenovaný profil hodnocení. Případně nastavte **defaultScoringProfile** použít vlastní profil jako výchozí, vyvolána vždy, když vlastní profil není zadán na řetězec dotazu.

## <a name="analyzers"></a>Analyzátory

Prvek analyzátory nastaví název analyzátoru jazyka pro pole. Další informace o rozsahu analyzátorů, které máte k dispozici, naleznete [v tématu Přidání analyzátorů do indexu Azure Cognitive Search](search-analyzers.md). Analyzátory lze použít pouze s prohledávatelnými poli. Jakmile je analyzátor přiřazen k poli, nelze jej změnit, dokud index neobnovíte.

## <a name="cors"></a>CORS

JavaScript na straně klienta nemůže ve výchozím nastavení volat žádná připojení k souboru API, protože prohlížeč zabrání všem požadavkům napříč původem. Chcete-li povolit dotazy křížového původu do indexu, povolte CORS (Cross-Origin Resource Sharing) nastavením atributu **corsOptions.** Z bezpečnostních důvodů podporují cors pouze api dotazu. 

Pro CORS lze nastavit následující možnosti:

+ **allowedOrigins** (povinné): Toto je seznam původů, které budou uděleny přístup k indexu. To znamená, že jakýkoli kód JavaScript obsluhovaný z těchto původů bude mít možnost dotazovat se na váš index (za předpokladu, že poskytuje správný klíč api). Každý původ je obvykle `protocol://<fully-qualified-domain-name>:<port>` formuláře, i když `<port>` je často vynechán. Další podrobnosti najdete [v článku Sdílení zdrojů mezi zdroji (Wikipedie).](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)

  Pokud chcete povolit přístup ke všem `*` původům, zahrnout jako jednu položku v **allowedOrigins** pole. *To není doporučená praxe pro produkční vyhledávací služby,* ale je často užitečné pro vývoj a ladění.

+ **maxAgeInSeconds** (volitelné): Prohlížeče používají tuto hodnotu k určení doby trvání (v sekundách) do mezipaměti odpovědí kontroly před výstupem CORS. Musí se jednat o nezáporné celé číslo. Čím větší je tato hodnota, tím lepší výkon bude, ale čím déle bude trvat, než se změny zásad CORS projeví. Pokud není nastavena, bude použita výchozí doba trvání 5 minut.

## <a name="encryption-key"></a>Šifrovací klíč

Zatímco všechny indexy Azure Cognitive Search jsou ve výchozím nastavení šifrovány pomocí klíčů spravovaných společností Microsoft, indexy lze nakonfigurovat tak, aby byly šifrovány pomocí **klíčů spravovaných zákazníky** v trezoru klíčů. Další informace najdete [v tématu Správa šifrovacích klíčů v Azure Cognitive Search](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Další kroky

S pochopení složení indexu, můžete pokračovat v portálu k vytvoření prvníindex.

> [!div class="nextstepaction"]
> [Přidání indexu (portál)](search-create-index-portal.md)
