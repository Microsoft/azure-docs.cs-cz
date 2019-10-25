---
title: Mapování polí pro automatizované indexování pomocí indexerů
titleSuffix: Azure Cognitive Search
description: Nakonfigurujte mapování polí v indexeru na účet pro rozdíly v názvech polí a reprezentace dat.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc863ee3dc7f2dc8049fcd22189acac94a855352
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786975"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapování polí a transformace pomocí indexerů Azure Kognitivní hledání

Při použití služby Azure Kognitivní hledání indexery někdy zjistíte, že vstupní data neodpovídají schématu cílového indexu. V těchto případech můžete použít **mapování polí** k přetvarování dat během procesu indexování.

Některé situace, kdy je vhodné mapování polí:

* Zdroj dat obsahuje pole s názvem `_id`, ale Azure Kognitivní hledání nepovoluje názvy polí, které začínají podtržítkem. Mapování polí umožňuje efektivně přejmenovat pole.
* Chcete naplnit několik polí v indexu ze stejných dat zdroje dat. Například můžete chtít u těchto polí použít různé analyzátory.
* Chcete vyplnit pole indexu daty z více než jednoho zdroje dat a jednotlivé zdroje dat používají různé názvy polí.
* Je potřeba kódovat nebo dekódovat data v kódování Base64. Mapování polí podporují několik **funkcí mapování**, včetně funkcí pro kódování a dekódování base64.

> [!NOTE]
> Funkce mapování polí v indexerech Azure Kognitivní hledání poskytuje jednoduchý způsob, jak mapovat datová pole do polí indexu, a to s několika možnostmi pro převod dat. Složitější data mohou vyžadovat předběžné zpracování k jejímu přetvarování do formuláře, který se snadno indexuje.
>
> Microsoft Azure Data Factory je výkonné cloudové řešení pro import a transformaci dat. Můžete také napsat kód pro transformaci zdrojových dat před indexováním. Příklady kódu naleznete v tématu [model relačních dat](search-example-adventureworks-modeling.md) a [modelových omezujících vlastností](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Nastavení mapování polí

Mapování polí se skládá ze tří částí:

1. `sourceFieldName`, který představuje pole ve zdroji dat. Tato vlastnost je povinná.
2. Volitelný `targetFieldName`, který představuje pole v indexu vyhledávání. Je-li tento parametr vynechán, je použit stejný název jako ve zdroji dat.
3. Volitelný `mappingFunction`, který umožňuje transformovat data pomocí jedné z několika předdefinovaných funkcí. Úplný seznam funkcí je [uvedený níže](#mappingFunctions).

Mapování polí jsou přidána do pole `fieldMappings` definice indexeru.

## <a name="map-fields-using-the-rest-api"></a>Mapování polí pomocí REST API

Mapování polí můžete přidat při vytváření nového indexeru pomocí požadavku [Create indexer](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) API. Mapování polí stávajícího indexeru můžete spravovat pomocí požadavku rozhraní API pro [aktualizaci indexeru](https://docs.microsoft.com/rest/api/searchservice/update-indexer) .

Tady je příklad, jak namapovat zdrojové pole na cílové pole s jiným názvem:

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

Na zdrojové pole se může odkazovat v mapování více polí. Následující příklad ukazuje, jak rozvětvit pole a zkopírovat stejné zdrojové pole do dvou různých indexových polí:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Kognitivní hledání používá porovnávání bez rozlišení velkých a malých písmen k překladu polí a názvů funkcí v mapování polí. To je pohodlné (nemusíte mít vše v pravém), ale to znamená, že váš zdroj dat nebo index nemůže obsahovat pole, která se liší pouze velikostí písmen.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapování polí pomocí sady .NET SDK

Mapování polí v sadě .NET SDK definujete pomocí třídy [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) , která má vlastnosti `SourceFieldName` a `TargetFieldName`a volitelně odkaz na `MappingFunction`.

Mapování polí můžete zadat při sestavování indexeru nebo později přímo nastavením vlastnosti `Indexer.FieldMappings`.

Následující C# příklad nastaví mapování polí při sestavování indexeru.

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

Funkce mapování pole transformuje obsah pole předtím, než se uloží do indexu. V současné době jsou podporovány následující funkce mapování:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode – funkce

Provádí kódování base64 ve vstupním řetězci *zabezpečeném adresou URL* . Předpokládá, že vstup je kódovaný v kódování UTF-8.

#### <a name="example---document-key-lookup"></a>Příklad – vyhledávání klíčů dokumentu

V klíči dokumentu Azure Kognitivní hledání se můžou vyskytovat jenom bezpečné znaky URL (protože zákazníci musí být schopni dokument adresovat pomocí [rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Pokud zdrojové pole pro klíč obsahuje nezabezpečené znaky URL, můžete použít funkci `base64Encode` pro její převod v čase indexování.

Když načtete kódovaný klíč při hledání, můžete pomocí funkce `base64Decode` získat původní hodnotu klíče a použít ji k načtení zdrojového dokumentu.

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

Pokud pro funkci mapování nezadáte vlastnost Parameters, použije se výchozí hodnota `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Azure Kognitivní hledání podporuje dvě různá kódování Base64. Při kódování a dekódování stejného pole byste měli použít stejné parametry. Další informace najdete v tématu [Možnosti kódování Base64](#base64details) pro určení parametrů, které se mají použít.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode – funkce

Provádí dekódování Base64 vstupního řetězce. U vstupu se předpokládá, že se jedná o řetězec, který je *bezpečný* pro kódování Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Příklad – dekódování metadat nebo adres URL objektu BLOB

Vaše zdrojová data mohou obsahovat řetězce kódované v kódování Base64, například řetězce metadat objektů BLOB nebo webové adresy URL, které chcete prohledávat jako prostý text. Můžete použít funkci `base64Decode` k zapnutí šifrovaných dat zpět do regulárních řetězců při naplňování indexu vyhledávání.

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

Pokud vlastnost Parameters nezadáte, použije se výchozí hodnota `{"useHttpServerUtilityUrlTokenEncode" : true}`.

Azure Kognitivní hledání podporuje dvě různá kódování Base64. Při kódování a dekódování stejného pole byste měli použít stejné parametry. Další podrobnosti najdete v tématu [Možnosti kódování Base64](#base64details) pro rozhodování o tom, jaké parametry se mají použít.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>možnosti kódování Base64

Azure Kognitivní hledání podporuje dvě různá kódování Base64: **tokeny zabezpečení HttpServerUtility**a **kódování Base64 zabezpečené URL bez odsazení**. Řetězec, který má při indexování kódování Base64, by měl být později dekódovaný se stejnými možnostmi kódování, jinak výsledek nebude odpovídat původnímu.

Pokud parametry `useHttpServerUtilityUrlTokenEncode` nebo `useHttpServerUtilityUrlTokenDecode` pro kódování a dekódování jsou nastaveny na `true`, `base64Encode` se chovají jako [HttpServerUtility. UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) a `base64Decode` se chovají jako [HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Pokud nepoužíváte úplný .NET Framework (to znamená, že používáte .NET Core nebo jiné rozhraní) k vytvoření klíčových hodnot pro emulaci chování Azure Kognitivní hledání, měli byste nastavit `useHttpServerUtilityUrlTokenEncode` a `useHttpServerUtilityUrlTokenDecode` na `false`. V závislosti na knihovně, kterou použijete, se funkce kódování a dekódování Base64 můžou lišit od těch, které používá Azure Kognitivní hledání.

Následující tabulka porovnává různá kódování Base64 `00>00?00`řetězců. Chcete-li určit požadované dodatečné zpracování (pokud existuje) pro funkce Base64, použijte funkci kódování knihovny na řetězec `00>00?00` a porovnejte výstup s očekávaným výstupem `MDA-MDA_MDA`.

| Encoding | Výstup kódování Base64 | Další zpracování po kódování knihovny | Další zpracování před dekódováním knihovny |
| --- | --- | --- | --- |
| Base64 s odsazením | `MDA+MDA/MDA=` | Používejte znaky bezpečné pro URL a odstraňte odsazení. | Použití standardních znaků Base64 a přidání odsazení |
| Base64 bez odsazení | `MDA+MDA/MDA` | Použít znaky bezpečné pro URL | Použití standardních znaků base64 |
| Zabezpečená adresa URL – Base64 s odsazením | `MDA-MDA_MDA=` | Odebrat odsazení | Přidat odsazení |
| Zabezpečená adresa URL – bez odsazení kódu base64 | `MDA-MDA_MDA` | Žádné | Žádné |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition – funkce

Rozdělí pole řetězce pomocí zadaného oddělovače a vybere token na zadané pozici ve výsledném rozdělení.

Tato funkce používá následující parametry:

* `delimiter`: řetězec, který má být použit jako oddělovač při rozdělení vstupního řetězce.
* `position`: celočíselná pozice tokenu, která se má vybrat po rozdělení vstupního řetězce, na celé číslo s nulovým základem.

Pokud je například vstup `Jane Doe`, `delimiter` je `" "`(místo) a `position` je 0, výsledek je `Jane`; Pokud je `position` 1, výsledek je `Doe`. Pokud pozice odkazuje na token, který neexistuje, je vrácena chyba.

#### <a name="example---extract-a-name"></a>Příklad – extrakce názvu

Zdroj dat obsahuje pole `PersonName` a chcete ho indexovat jako dvě samostatná `FirstName` a `LastName`ová pole. Tuto funkci můžete použít k rozdělení vstupu pomocí znaku mezery jako oddělovače.

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

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection – funkce

Transformuje řetězec formátovaný jako pole JSON řetězců na pole řetězců, které lze použít k naplnění `Collection(Edm.String)`ho pole v indexu.

Například pokud je vstupní řetězec `["red", "white", "blue"]`, pak cílové pole typu `Collection(Edm.String)` se naplní třemi hodnotami `red`, `white`a `blue`. Pro vstupní hodnoty, které nelze analyzovat jako pole řetězců JSON, je vrácena chyba.

#### <a name="example---populate-collection-from-relational-data"></a>Příklad: naplnit kolekci z relačních dat

Azure SQL Database nemá integrovaný datový typ, který se přirozeně mapuje na `Collection(Edm.String)` pole v Azure Kognitivní hledání. Pro naplnění polí kolekce řetězců můžete zdrojová data předzpracovat jako pole řetězců JSON a potom použít funkci mapování `jsonArrayToStringCollection`.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Podrobný příklad, který transformuje relační data na indexovací pole kolekce, najdete v tématu [model relačních dat](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode – funkce

Tato funkce se dá použít ke kódování řetězce tak, aby byla zabezpečená adresa URL. Při použití s řetězcem, který obsahuje znaky, které nejsou povoleny v adrese URL, tato funkce převede tyto "nebezpečné" znaky na ekvivalenty znakové entity. Tato funkce používá formát kódování UTF-8.

#### <a name="example---document-key-lookup"></a>Příklad – vyhledávání klíčů dokumentu

funkci `urlEncode` lze použít jako alternativu k funkci `base64Encode`, pokud chcete převést pouze nebezpečné znaky adresy URL, přičemž zachováte jiné znaky tak, jak jsou.

Řekněme, že vstupní řetězec je `<hello>` – cílové pole typu `(Edm.String)` se naplní hodnotou `%3chello%3e`

Když načtete kódovaný klíč při hledání, můžete pomocí funkce `urlDecode` získat původní hodnotu klíče a použít ji k načtení zdrojového dokumentu.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlDecode – funkce

 Tato funkce převede řetězec s kódováním URL na dekódovaný řetězec pomocí formátu UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Příklad – dekódování metadat objektů BLOB

 Někteří klienti Azure Storage automaticky zakódují metadata objektů blob, pokud obsahují jiné znaky než ASCII. Pokud však chcete taková metadata vyhledat (jako prostý text), můžete použít funkci `urlDecode` k zapnutí šifrovaných dat zpět do regulárních řetězců při plnění indexu vyhledávání.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```