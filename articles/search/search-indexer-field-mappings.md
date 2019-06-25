---
title: Mapování polí pro automatické indexování pomocí indexerů – Azure Search
description: Nakonfigurujte mapování polí indexeru Azure Search pro rozdíly v názvy polí a formátovaná data.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147792"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Mapování polí a transformace pomocí indexerů Azure Search

Když pomocí indexerů Azure Search, někdy zjistíte, že se vstupní data poměrně neodpovídá schématu cílový index. V takových případech můžete použít **mapování polí** tvaru dat během indexování.

Některé situace, ve kterém jsou užitečné mapování polí:

* Zdroj dat má pole s názvem `_id`, ale Azure Search neumožňuje názvy polí, které začínají znakem podtržítka. Mapování polí umožňuje účinně přejmenovat pole.
* Chcete naplnit pole několik polí v indexu z stejná data datového zdroje. Například můžete chtít použít jinou analyzátory do těchto polí.
* Chcete naplnit pole indexu s daty z více než jeden zdroj dat, a zdroje dat jednotlivých názvy jiného pole.
* Potřebujete z formátu Base64. kódování nebo dekódování vaše data. Mapování polí podporovat několik **mapování funkce**, včetně funkcí pro Base64, kódování a dekódování.

> [!NOTE]
> Funkce mapování pole indexerů Azure Search poskytuje jednoduchý způsob, jak mapování na pole indexu, s několika možnostmi pro převod dat datová pole. Složitější data můžou vyžadovat předběžné zpracování tvaru do formuláře, který se snadno do indexu.
>
> Microsoft Azure Data Factory je výkonné cloudové řešení pro import a transformovat data. Můžete také napsat kód k transformují zdrojová data před indexování. Příklady kódu naleznete v tématu [modelování relačních dat](search-example-adventureworks-modeling.md) a [Model víceúrovňové omezující vlastnosti](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Nastavení mapování polí

Mapování polí se skládá ze tří částí:

1. A `sourceFieldName`, který reprezentuje pole ve zdroji dat. Tato vlastnost se vyžaduje.
2. Volitelně `targetFieldName`, která představuje pole v indexu vyhledávání. Pokud tento parametr vynechán, stejný název jako zdroj dat se používá.
3. Volitelně `mappingFunction`, které můžete transformovat svoje data pomocí jedné z několika předdefinovanými funkcemi. Úplný seznam funkcí je [níže](#mappingFunctions).

Mapování polí jsou přidány do `fieldMappings` pole definice indexeru.

## <a name="map-fields-using-the-rest-api"></a>Mapování polí pomocí rozhraní REST API

Při vytváření nového pomocí indexeru můžete přidat mapování polí [vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) žádosti na rozhraní API. Můžete spravovat pomocí indexeru existující mapování polí [aktualizace Indexer](https://docs.microsoft.com/rest/api/searchservice/update-indexer) žádosti na rozhraní API.

Například tady je způsob mapování zdrojové pole z cílového pole s jiným názvem:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Zdrojové pole může být odkazováno v několika mapování polí. Následující příklad ukazuje, jak "vytvořit fork" pole kopírování stejné zdrojové pole do dvou různých indexu pole:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Služba Azure Search používá porovnání velká a malá písmena pro názvy polí a funkce v mapování polí. Tato možnost je pohodlná (není nutné získat správný všechny malých a velkých písmen), to ale znamená, že zdroj dat nebo index nemůže mít pole, která se liší pouze velikostí písma.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapování polí pomocí sady .NET SDK

Můžete definovat mapování polí pomocí sady .NET SDK [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) třídu, která má vlastnosti `SourceFieldName` a `TargetFieldName`a případně `MappingFunction` odkaz.

Mapování polí můžete zadat při vytváření indexeru nebo později tak, že nastavíte přímo `Indexer.FieldMappings` vlastnost.

Následující C# příklad nastaví mapování polí při vytváření indexeru.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funkce mapování polí

Funkce mapování pole transformuje obsah pole před jejich uložením v indexu. Aktuálně jsou podporovány následující funkce mapování:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode – funkce

Provádí *adresu URL bezpečné* kódování Base64 vstupního řetězce. Předpokládá, že vstup je UTF-8.

#### <a name="example---document-key-lookup"></a>Příklad: hledání dokumentů

Pouze adresu URL bezpečné znaky se mohou objevit v klíči dokumentu Azure Search (protože zákazníci musí být schopni vyřešit dokumentu pomocí [rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Pokud zdrojové pole pro klíč obsahuje adresu URL problematické znaky, můžete použít `base64Encode` funkce pro převod na indexování čas.

Při načtení kódovaného klíče v době vyhledávání, pak můžete použít `base64Decode` funkci k získání původní hodnoty klíče a použijte ho k načtení zdrojovém dokumentu.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Pokud nezadáte vlastnost parametry funkce mapování, použije se výchozí hodnota `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Služba Azure Search podporuje dvě různé kódování Base64. Měli byste použít stejné parametry při kódování a dekódování stejné pole. Další informace najdete v tématu [možnosti kódování base64](#base64details) rozhodnout, jaké parametry se mají použít.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode – funkce

Provádí ve formátu Base64 dekódování vstupního řetězce. Vstup je považován za *adresu URL bezpečné* řetězec s kódováním Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Příklad – dekódování metadata objektu blob nebo adresy URL

Zdroj dat může obsahovat řetězce s kódováním Base64, například řetězce metadata objektu blob nebo webové adresy URL, které mají být prohledávatelné jako prostý text. Můžete použít `base64Decode` funkce, chcete-li kódovaná data zpět do regulárních řetězců při naplňování indexu vyhledávání.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Pokud nezadáte vlastnost parameters, použije se výchozí hodnota `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Služba Azure Search podporuje dvě různé kódování Base64. Měli byste použít stejné parametry při kódování a dekódování stejné pole. Další podrobnosti najdete v tématu [možnosti kódování base64](#base64details) rozhodnout, jaké parametry se mají použít.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Možnosti kódování Base64

Služba Azure Search podporuje dvě různé kódování Base64: **Adresa URL HttpServerUtility token**, a **kódování Base64 bezpečná pro adresu URL bez odsazení**. Řetězec, který je s kódováním base64 během indexování by se později dekódovat se stejnými možnostmi kódování, jinak výsledek nebude odpovídají originálu.

Pokud `useHttpServerUtilityUrlTokenEncode` nebo `useHttpServerUtilityUrlTokenDecode` parametry pro kódování a dekódování v uvedeném pořadí jsou nastaveny na `true`, pak `base64Encode` se chová jako [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) a `base64Decode` se chová jako [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Pokud nepoužíváte úplné rozhraní .NET Framework (to znamená, používáte .NET Core nebo jiné rozhraní framework) k vytvoření hodnoty klíče, abyste emulovali chování Azure Search, pak byste měli nastavit `useHttpServerUtilityUrlTokenEncode` a `useHttpServerUtilityUrlTokenDecode` k `false`. V závislosti na knihovně, které používáte ve formátu base64 kódování a dekódování funkce mohou lišit od těch, které používá služba Azure Search.

Následující tabulka porovnává různé base64 kódování řetězce `00>00?00`. K určení vyžaduje další zpracování (pokud existuje) pro vaše funkce ve formátu base64, použít knihovnu kódování funkce v řetězci `00>00?00` a porovnat výstupu se očekávaný výstup `MDA-MDA_MDA`.

| Kódování | Výstup zakódovat do formátu Base64 | Další zpracování po kódování knihovny | Další zpracování před dekódování knihovny |
| --- | --- | --- | --- |
| Ve formátu Base64 s odsazením | `MDA+MDA/MDA=` | Použijte adresu URL bezpečné znaků a odeberte odsazení | Použít standardní base64 znaky a přidejte odsazení |
| Ve formátu Base64 bez odsazení | `MDA+MDA/MDA` | Použijte adresu URL bezpečné znaků | Použít standardní base64 znaky |
| Bezpečná pro adresu URL ve formátu base64 s odsazením | `MDA-MDA_MDA=` | Odebere odsazení | Přidat odsazení |
| Bezpečná pro adresu URL ve formátu base64 bez odsazení | `MDA-MDA_MDA` | Žádný | Žádný |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition – funkce

Rozdělí pole řetězce, pomocí zadaného oddělovače a vybere token na konkrétní pozici v výsledný rozdělení.

Tato funkce používá následující parametry:

* `delimiter`: řetězec pro použití jako oddělovače při rozdělení vstupního řetězce.
* `position`: celé číslo pozice s nulovým základem tokenu k výběru po rozdělení vstupního řetězce.

Například, pokud je vstup `Jane Doe`, `delimiter` je `" "`(místo) a `position` je 0, je výsledkem `Jane`; Pokud `position` 1, výsledkem je `Doe`. Pokud umístění odkazuje na token, který neexistuje, vrátí se chyba.

#### <a name="example---extract-a-name"></a>Příklad – extrahovat název

Zdroj dat obsahuje `PersonName` pole a chcete, aby se jako dva samostatné `FirstName` a `LastName` pole. Tuto funkci můžete použít k rozdělení vstupního použití znaku mezery jako oddělovače.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection function

Převede řetězec formátovaný jako pole řetězců JSON, který slouží k naplnění pole řetězců `Collection(Edm.String)` pole v indexu.

Například, pokud je vstupní řetězec `["red", "white", "blue"]`, pak pole cílového typu `Collection(Edm.String)` naplní tři hodnoty `red`, `white`, a `blue`. K zadávání hodnot, které nelze analyzovat jako pole řetězce JSON vrátí se chyba.

#### <a name="example---populate-collection-from-relational-data"></a>Příklad: naplnění kolekce z relačních dat

Azure SQL Database nemá integrované datový typ, který se přirozeně mapuje `Collection(Edm.String)` pole ve službě Azure Search. K naplnění kolekce polí s řetězcem, můžete předběžně zpracovat svá zdrojová data jako pole řetězců JSON a pak použít `jsonArrayToStringCollection` mapování funkce.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Podrobný příklad, který transformuje relační data do indexu kolekce polí naleznete v tématu [modelování relačních dat](search-example-adventureworks-modeling.md).
