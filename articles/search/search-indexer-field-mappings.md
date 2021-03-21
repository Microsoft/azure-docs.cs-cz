---
title: Mapování polí v indexerech
titleSuffix: Azure Cognitive Search
description: Nakonfigurujte mapování polí v indexeru na účet pro rozdíly v názvech polí a reprezentace dat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: fb3a77291d8b24d5774094533f8c214f1527d771
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430441"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapování polí a transformace pomocí indexerů Azure Kognitivní hledání

![Fáze indexeru](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "fáze indexeru")

Při použití služby Azure Kognitivní hledání indexery někdy zjistíte, že vstupní data neodpovídají schématu cílového indexu. V těchto případech můžete použít **mapování polí** k přetvarování dat během procesu indexování.

Některé situace, kdy je vhodné mapování polí:

* Váš zdroj dat obsahuje pole s názvem `_id` , ale Azure kognitivní hledání nepovoluje názvy polí, které začínají podtržítkem. Mapování polí umožňuje efektivně přejmenovat pole.
* Chcete naplnit několik polí v indexu ze stejných dat zdroje dat. Například můžete chtít u těchto polí použít různé analyzátory.
* Chcete vyplnit pole indexu daty z více než jednoho zdroje dat a jednotlivé zdroje dat používají různé názvy polí.
* Je potřeba kódovat nebo dekódovat data v kódování Base64. Mapování polí podporují několik **funkcí mapování**, včetně funkcí pro kódování a dekódování base64.

> [!NOTE]
> Mapování polí v indexerech je jednoduchý způsob, jak mapovat datová pole k indexovaným polím, a to s určitou možností pro převod dat s lehkým zatížením. Složitější data mohou vyžadovat předběžné zpracování, aby bylo možné je znovu natvarovat do formuláře, který je přispívající k indexování. Jedna z možností, kterou můžete zvážit, je [Azure Data Factory](../data-factory/index.yml).

## <a name="set-up-field-mappings"></a>Nastavení mapování polí

Mapování polí se skládá ze tří částí:

1. A `sourceFieldName` , který představuje pole ve zdroji dat. Tato vlastnost je povinná.
2. Volitelné `targetFieldName` , které představuje pole v indexu vyhledávání. Je-li tento parametr vynechán, je použit stejný název jako ve zdroji dat.
3. Volitelná `mappingFunction` , která může transformovat data pomocí jedné z několika předdefinovaných funkcí. To lze použít pro mapování vstupních i výstupních polí. Úplný seznam funkcí je [uvedený níže](#mappingFunctions).

Mapování polí jsou přidána do `fieldMappings` pole definice indexeru.

> [!NOTE]
> Pokud nejsou přidána žádná mapování polí, předpokládají indexery pole zdroje dat, které by měly být mapovány na indexová pole se stejným názvem. Přidáním mapování polí dojde k odebrání těchto výchozích mapování polí pro zdrojové a cílové pole. Některé indexery, jako je [indexer úložiště objektů BLOB](search-howto-indexing-azure-blob-storage.md), přidají mapování výchozích polí pro pole klíč indexu.

## <a name="map-fields-using-rest"></a>Mapování polí pomocí REST

Mapování polí můžete přidat při vytváření nového indexeru pomocí požadavku [Create indexer](/rest/api/searchservice/create-Indexer) API. Mapování polí stávajícího indexeru můžete spravovat pomocí požadavku rozhraní API pro [aktualizaci indexeru](/rest/api/searchservice/update-indexer) .

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

## <a name="map-fields-using-net"></a>Mapování polí pomocí .NET

Mapování polí v sadě .NET SDK definujete pomocí třídy [FieldMapping](/dotnet/api/azure.search.documents.indexes.models.fieldmapping) , která má vlastnosti a a `SourceFieldName` `TargetFieldName` volitelný `MappingFunction` odkaz.

Mapování polí můžete zadat při vytváření indexeru nebo později přímo nastavením `Indexer.FieldMappings` Vlastnosti.

Následující příklad jazyka C# nastaví mapování polí při sestavování indexeru.

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

V klíči dokumentu Azure Kognitivní hledání se můžou vyskytovat jenom bezpečné znaky URL (protože zákazníci musí být schopni dokument adresovat pomocí [rozhraní API pro vyhledávání](/rest/api/searchservice/lookup-document) ). Pokud zdrojové pole pro klíč obsahuje nezabezpečené znaky URL, můžete použít `base64Encode` funkci k jejímu převodu v čase indexování. Nicméně klíč dokumentu (před i po převodu) nemůže být delší než 1 024 znaků.

Když načtete kódovaný klíč v době hledání, můžete použít `base64Decode` funkci k získání původní hodnoty klíče a použít ji k načtení zdrojového dokumentu.

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

#### <a name="example---preserve-original-values"></a>Příklad – zachování původních hodnot

[Indexer úložiště objektů BLOB](search-howto-indexing-azure-blob-storage.md) automaticky přidá mapování polí z `metadata_storage_path` identifikátoru URI objektu blob do pole klíč indexu, pokud není zadané žádné mapování polí. Tato hodnota je zakódovaná v kódování Base64, aby ji bylo možné bezpečně použít jako klíč dokumentu Kognitivní hledání Azure. Následující příklad ukazuje, jak současně namapovat verzi kódovaného kódu base64 na konkrétní *adresu URL* `metadata_storage_path` na `index_key` pole a zachovat původní hodnotu v `metadata_storage_path` poli:

```JSON

"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

Pokud nezadáte vlastnost Parameters pro funkci mapování, použije se výchozí hodnota `{"useHttpServerUtilityUrlTokenEncode" : true}` .

Azure Kognitivní hledání podporuje dvě různá kódování Base64. Při kódování a dekódování stejného pole byste měli použít stejné parametry. Další informace najdete v tématu [Možnosti kódování Base64](#base64details) pro určení parametrů, které se mají použít.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode – funkce

Provádí dekódování Base64 vstupního řetězce. U vstupu se předpokládá, že se jedná o řetězec, který je *bezpečný* pro kódování Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Příklad – dekódování metadat nebo adres URL objektu BLOB

Vaše zdrojová data mohou obsahovat řetězce kódované v kódování Base64, například řetězce metadat objektů BLOB nebo webové adresy URL, které chcete prohledávat jako prostý text. Funkci lze použít `base64Decode` k zapnutí šifrovaných dat zpět do regulárních řetězců při naplňování indexu vyhledávání.

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

Pokud vlastnost Parameters nezadáte, použije se výchozí hodnota `{"useHttpServerUtilityUrlTokenEncode" : true}` .

Azure Kognitivní hledání podporuje dvě různá kódování Base64. Při kódování a dekódování stejného pole byste měli použít stejné parametry. Další podrobnosti najdete v tématu [Možnosti kódování Base64](#base64details) pro rozhodování o tom, jaké parametry se mají použít.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>možnosti kódování Base64

Azure Kognitivní hledání podporuje kódování Base64 s bezpečným URL a normální kódování Base64. Řetězec, který má kódování Base64 během indexování, by měl být později dekódovaný se stejnými možnostmi kódování nebo jinak výsledek nebude odpovídat původnímu.

Pokud `useHttpServerUtilityUrlTokenEncode` parametry nebo `useHttpServerUtilityUrlTokenDecode` pro kódování a dekódování jsou nastaveny na, se `true` `base64Encode` chovají jako [HttpServerUtility. UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) a `base64Decode` chovají se jako [HttpServerUtility. UrlTokenDecode](/dotnet/api/system.web.httpserverutility.urltokendecode).

> [!WARNING]
> Pokud `base64Encode` se používá k vytvoření hodnot klíče, `useHttpServerUtilityUrlTokenEncode` musí být nastaven na hodnotu true. Pro klíčové hodnoty lze použít pouze kódování Base64 v bezpečí adrese URL. Úplnou sadu omezení znaků v hodnotách klíčů najdete v tématu [pravidla Pojmenování &#40;Azure Kognitivní hledání&#41;](/rest/api/searchservice/naming-rules) .

Knihovny .NET v Azure Kognitivní hledání předpokládají úplný .NET Framework, který poskytuje integrované kódování. `useHttpServerUtilityUrlTokenEncode`Možnosti a využívají `useHttpServerUtilityUrlTokenDecode` tuto vestavěnou funkci. Pokud používáte .NET Core nebo jiné rozhraní, doporučujeme, abyste tyto možnosti nastavovat na `false` a přímo zavolali funkce kódování a dekódování vašeho rozhraní.

Následující tabulka porovnává různá kódování Base64 řetězce `00>00?00` . Chcete-li určit požadované dodatečné zpracování (pokud existuje) pro funkce Base64, použijte funkci kódování knihovny na řetězec `00>00?00` a porovnejte výstup s očekávaným výstupem `MDA-MDA_MDA` .

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
* `position`: celé číslo nulové pozice tokenu, který má být vybrán po rozdělení vstupního řetězce.

Například pokud je vstup `Jane Doe` , `delimiter` je `" "` (Space) a `position` je 0, výsledek je `Jane` ; Pokud `position` je hodnota 1, výsledkem je `Doe` . Pokud pozice odkazuje na token, který neexistuje, je vrácena chyba.

#### <a name="example---extract-a-name"></a>Příklad – extrakce názvu

Zdroj dat obsahuje `PersonName` pole a chcete ho indexovat jako dvě samostatná `FirstName` `LastName` pole a. Tuto funkci můžete použít k rozdělení vstupu pomocí znaku mezery jako oddělovače.

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

Transformuje řetězec formátovaný jako pole JSON řetězců na pole řetězců, které lze použít k naplnění `Collection(Edm.String)` pole v indexu.

Například pokud je vstupní řetězec `["red", "white", "blue"]` , pak cílové pole typu `Collection(Edm.String)` bude vyplněno třemi hodnotami `red` , `white` a `blue` . Pro vstupní hodnoty, které nelze analyzovat jako pole řetězců JSON, je vrácena chyba.

#### <a name="example---populate-collection-from-relational-data"></a>Příklad: naplnit kolekci z relačních dat

Azure SQL Database nemá integrovaný datový typ, který je přirozeně mapován na `Collection(Edm.String)` pole ve službě Azure kognitivní hledání. Pro naplnění polí kolekce řetězců můžete zdrojová data předzpracovat jako pole řetězce JSON a potom použít `jsonArrayToStringCollection` funkci Mapping.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode – funkce

Tato funkce se dá použít ke kódování řetězce tak, aby byla zabezpečená adresa URL. Při použití s řetězcem, který obsahuje znaky, které nejsou povoleny v adrese URL, tato funkce převede tyto "nebezpečné" znaky na ekvivalenty znakové entity. Tato funkce používá formát kódování UTF-8.

#### <a name="example---document-key-lookup"></a>Příklad – vyhledávání klíčů dokumentu

`urlEncode` funkci lze použít jako alternativu k `base64Encode` funkci, pokud je třeba převést pouze nebezpečné znaky adresy URL, přičemž zachová jiné znaky tak, jak jsou.

Řekněme, že vstupní řetězec je a `<hello>` pak cílové pole typu `(Edm.String)` se naplní hodnotou. `%3chello%3e`

Když načtete kódovaný klíč v době hledání, můžete použít `urlDecode` funkci k získání původní hodnoty klíče a použít ji k načtení zdrojového dokumentu.

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

 Někteří klienti Azure Storage automaticky zakódují metadata objektů blob, pokud obsahují jiné znaky než ASCII. Pokud však chcete taková metadata vyhledat (jako prostý text), můžete použít `urlDecode` funkci k zapnutí šifrovaných dat zpět do regulárních řetězců při plnění indexu vyhledávání.

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
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>fixedLengthEncode – funkce
 
 Tato funkce převede řetězec libovolné délky na řetězec s pevnou délkou.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Příklad – mapování klíčů dokumentů, které jsou příliš dlouhé
 
Pokud se na základě chyb týkajících se klíče dokumentu nachází více než 1024 znaků, lze tuto funkci použít pro snížení délky klíče dokumentu.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```