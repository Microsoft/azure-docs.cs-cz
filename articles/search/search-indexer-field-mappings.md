---
title: Mapování polí v indexerech
titleSuffix: Azure Cognitive Search
description: Nakonfigurujte mapování polí v indexeru tak, aby zohledňovala rozdíly v názvech polí a reprezentacích dat.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3e09741e841897032b8146dee67b79e0c26ea5cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275148"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapování polí a transformace pomocí indexerů Azure Cognitive Search

Při použití indexerů Azure Cognitive Search někdy zjistíte, že vstupní data zcela neodpovídají schématu cílového indexu. V těchto případech můžete použít **mapování polí** ke přetvoření dat během procesu indexování.

Některé situace, kdy jsou užitečná mapování polí:

* Váš zdroj dat má `_id`pole s názvem , ale Azure Cognitive Search neumožňuje názvy polí, které začínají podtržítkem. Mapování polí umožňuje efektivně přejmenovat pole.
* Chcete naplnit několik polí v indexu ze stejných dat zdroje dat. Můžete například použít různé analyzátory pro tato pole.
* Chcete naplnit pole indexu daty z více než jednoho zdroje dat a zdroje dat používají různé názvy polí.
* Musíte Base64 zakódovat nebo dekódovat data. Mapování polí podporuje několik **mapovacích funkcí**, včetně funkcí pro kódování a dekódování Base64.

> [!NOTE]
> Funkce mapování polí indexerů Azure Cognitive Search poskytuje jednoduchý způsob mapování datových polí na indexová pole s několika možnostmi převodu dat. Složitější data mohou vyžadovat předběžné zpracování, aby bylo možné změnit jejich tvar do formuláře, který lze snadno indexovat.
>
> Microsoft Azure Data Factory je výkonné cloudové řešení pro import a transformaci dat. Můžete také napsat kód pro transformaci zdrojových dat před indexováním. Příklady kódu naleznete v [tématu Model relační data](search-example-adventureworks-modeling.md) a [model víceúrovňové omezující čáry](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Nastavení mapování polí

Mapování polí se skládá ze tří částí:

1. A `sourceFieldName`, který představuje pole ve zdroji dat. Tato vlastnost je povinná.
2. Volitelné `targetFieldName`, které představuje pole v indexu vyhledávání. Pokud je vynechán, použije se stejný název jako ve zdroji dat.
3. Volitelné `mappingFunction`, které lze transformovat data pomocí jedné z několika předdefinovaných funkcí. Úplný seznam funkcí je [uveden níže](#mappingFunctions).

Mapování polí jsou přidány `fieldMappings` do pole definice indexeru.

## <a name="map-fields-using-the-rest-api"></a>Mapování polí pomocí rozhraní REST API

Mapování polí můžete přidat při vytváření nového indexeru pomocí požadavku vytvořit rozhraní API [indexeru.](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) Mapování polí existujícího indexeru můžete spravovat pomocí požadavku rozhraní API [aktualizace indexeru.](https://docs.microsoft.com/rest/api/searchservice/update-indexer)

Tady je například postup mapování zdrojového pole na cílové pole s jiným názvem:

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

Na zdrojové pole lze odkazovat ve více mapováních polí. Následující příklad ukazuje, jak "rozvinit" pole, kopírování stejného zdrojového pole do dvou různých polí indexu:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Cognitive Search používá porovnání bez rozlišování velkých a malých písmen k vyřešení názvů polí a funkcí v mapování polí. To je výhodné (nemusíte získat všechna písmena vpravo), ale to znamená, že zdroj dat nebo index nemůže mít pole, která se liší pouze případ.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapování polí pomocí sady .NET SDK

Mapování polí v sdk .NET sdk pomocí [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) `SourceFieldName` třídy, která má vlastnosti a `TargetFieldName`, a volitelný `MappingFunction` odkaz.

Mapování polí můžete určit při vytváření indexeru nebo později přímým nastavením vlastnosti. `Indexer.FieldMappings`

Následující příklad jazyka C# nastaví mapování polí při vytváření indexeru.

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

Funkce mapování polí transformuje obsah pole před uložením v indexu. V současné době jsou podporovány následující funkce mapování:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenatPosition](#extractTokenAtPositionFunction)
* [Kolekce jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [Urlencode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode

Provádí kódování vstupního řetězce Base64 *bezpečné pro adresu URL.* Předpokládá, že vstup je kódován UTF-8.

#### <a name="example---document-key-lookup"></a>Příklad – vyhledávání klíčů dokumentu

V klíči dokumentu Azure Cognitive Search se mohou zobrazit pouze znaky bezpečné pro adresy URL (protože zákazníci musí být schopni adresovat dokument pomocí [vyhledávacího rozhraní API).](https://docs.microsoft.com/rest/api/searchservice/lookup-document) Pokud zdrojové pole klíče obsahuje znaky nebezpečné pro `base64Encode` adresy URL, můžete tuto funkci použít k převodu v době indexování. Klíč dokumentu (před i po převodu) však nesmí být delší než 1 024 znaků.

Když načtete kódovaný klíč v době hledání, `base64Decode` můžete pomocí funkce získat původní hodnotu klíče a použít ji k načtení zdrojového dokumentu.

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

Pokud nezahrnete vlastnost parameters pro funkci mapování, bude `{"useHttpServerUtilityUrlTokenEncode" : true}`výchozí hodnota .

Azure Cognitive Search podporuje dvě různá kódování Base64. Při kódování a dekódování stejného pole byste měli použít stejné parametry. Další informace naleznete v [tématu base64 možnosti kódování](#base64details) rozhodnout, které parametry použít.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode

Provede dekódování vstupního řetězce Base64. Vstup je považován za řetězec kódovaný *pro základní adresu URL* Base64.

#### <a name="example---decode-blob-metadata-or-urls"></a>Příklad – dekódování metadat objektu blob nebo adres URL

Zdrojová data mohou obsahovat řetězce kódované base64, jako jsou řetězce metadat objektu blob nebo webové adresy URL, které chcete prohledávat jako prostý text. `base64Decode` Pomocí funkce můžete při vyplnění indexu vyhledávání převést kódovaná data zpět na běžné řetězce.

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

Pokud nezahrnete vlastnost parameters, bude výchozí `{"useHttpServerUtilityUrlTokenEncode" : true}`hodnota .

Azure Cognitive Search podporuje dvě různá kódování Base64. Při kódování a dekódování stejného pole byste měli použít stejné parametry. Další podrobnosti naleznete v [tématu base64 možnosti kódování](#base64details) rozhodnout, které parametry použít.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 možnosti kódování

Azure Cognitive Search podporuje kódování base64 bezpečné pro adresy URL a normální kódování base64. Řetězec, který je base64 kódován během indexování by měl být dekódován později se stejnými možnostmi kódování, jinak výsledek nebude odpovídat originálu.

Pokud `useHttpServerUtilityUrlTokenEncode` jsou `useHttpServerUtilityUrlTokenDecode` parametry nebo pro kódování a `true`dekódování `base64Encode` nastaveny na , pak se chová `base64Decode` jako [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) a chová se jako [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

> [!WARNING]
> Pokud `base64Encode` se používá k `useHttpServerUtilityUrlTokenEncode` výrobě hodnoty klíče, musí být nastavena na hodnotu true. Pro klíčové hodnoty lze použít pouze kódování base64 bezpečné pro url. Viz [Pravidla pojmenování &#40;Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/naming-rules) pro úplnou sadu omezení znaků v klíčových hodnotách.

Knihovny .NET v Azure Cognitive Search předpokládají úplné rozhraní .NET Framework, které poskytuje integrované kódování. A `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` možnosti využívají tuto vestavěnou funkci. Pokud používáte .NET Core nebo jiné rozhraní, `false` doporučujeme nastavit tyto možnosti a volání architektury kódování a dekódování funkce přímo.

Následující tabulka porovnává různé základní 64 kódování `00>00?00`řetězce . Chcete-li určit požadované další zpracování (pokud existuje) pro vaše base64 `00>00?00` funkce, použijte knihovnu `MDA-MDA_MDA`kódovat funkci na řetězci a porovnat výstup s očekávaným výstupem .

| Kódování | Base64 kódovat výstup | Další zpracování po kódování knihovny | Další zpracování před dekódováním knihovny |
| --- | --- | --- | --- |
| Base64 s polstrováním | `MDA+MDA/MDA=` | Použití znaků bezpečných pro adresu URL a odebrání odsazení | Použití standardních znaků base64 a přidání odsazení |
| Base64 bez polstrování | `MDA+MDA/MDA` | Použití znaků bezpečných pro adresy URL | Použití standardních znaků base64 |
| Url-safe base64 s polstrováním | `MDA-MDA_MDA=` | Odstranit odsazení | Přidání odsazení |
| Url-safe base64 bez polstrování | `MDA-MDA_MDA` | Žádný | Žádný |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>funkce extractTokenatPosition

Rozdělí pole řetězce pomocí zadaného oddělovače a vybere token na zadané pozici ve výsledném rozdělení.

Tato funkce používá následující parametry:

* `delimiter`: řetězec, který se použije jako oddělovač při rozdělení vstupního řetězce.
* `position`: celočíselná pozice tokenu založená na nule, kterou chcete vybrat po rozdělení vstupního řetězce.

Například pokud je `Jane Doe`vstup `delimiter` , `" "`je (mezera) `position` a je `Jane`0, výsledek je ; pokud `position` je 1, výsledek `Doe`je . Pokud pozice odkazuje na token, který neexistuje, je vrácena chyba.

#### <a name="example---extract-a-name"></a>Příklad - extrahovat název

Zdroj dat obsahuje `PersonName` pole a chcete jej indexovat `FirstName` `LastName` jako dvě samostatná pole a pole. Pomocí této funkce můžete rozdělit vstup pomocí znaku mezery jako oddělovače.

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

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection

Transformuje řetězec formátovaný jako pole JSON řetězců do pole řetězců, `Collection(Edm.String)` které lze použít k naplnění pole v indexu.

Pokud `["red", "white", "blue"]`je například vstupní řetězec , bude `Collection(Edm.String)` cílové pole typu naplněno `red` `white`třemi `blue`hodnotami , a . Pro vstupní hodnoty, které nelze analyzovat jako pole řetězců JSON, je vrácena chyba.

#### <a name="example---populate-collection-from-relational-data"></a>Příklad - naplnění kolekce z relačních dat

Azure SQL Database nemá předdefinovaný datový typ, který `Collection(Edm.String)` se přirozeně mapuje na pole v Azure Cognitive Search. Chcete-li naplnit pole kolekce řetězců, můžete předem zpracovat zdrojová data `jsonArrayToStringCollection` jako pole řetězců JSON a potom použít funkci mapování.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Podrobný příklad, který transformuje relační data do polí kolekce indexu, naleznete v [tématu Model relační data](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode

Tuto funkci lze použít ke kódování řetězce tak, aby byl "URL safe". Při použití s řetězcem, který obsahuje znaky, které nejsou povoleny v adrese URL, tato funkce převede tyto "nebezpečné" znaky na ekvivalenty znakové entity. Tato funkce používá formát kódování UTF-8.

#### <a name="example---document-key-lookup"></a>Příklad – vyhledávání klíčů dokumentu

`urlEncode`funkce může být použita `base64Encode` jako alternativa k funkci, pokud mají být převedeny pouze nebezpečné znaky URL, při zachování jiných znaků tak, jak jsou.

Řekněme, že `<hello>` vstupní řetězec je - `(Edm.String)` pak cílové pole typu bude naplněno hodnotou`%3chello%3e`

Když načtete kódovaný klíč v době hledání, `urlDecode` můžete pomocí funkce získat původní hodnotu klíče a použít ji k načtení zdrojového dokumentu.

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

 ### <a name="urldecode-function"></a>urlDecode

 Tato funkce převede řetězec kódovaný adresou URL na dekódovaný řetězec pomocí formátu kódování UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Příklad – dekódování metadat objektu blob

 Někteří klienti úložiště Azure automaticky kódují metadata objektů blob, pokud obsahují znaky bez ASCII. Pokud však chcete, aby byla tato metadata prohledávatelná `urlDecode` (jako prostý text), můžete pomocí této funkce převést kódovaná data zpět na běžné řetězce při vyplnění indexu vyhledávání.

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
 
 ### <a name="fixedlengthencode-function"></a>funkce fixedLengthEncode
 
 Tato funkce převede řetězec libovolné délky na řetězec s pevnou délkou.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Příklad - mapovat klíče dokumentu, které jsou příliš dlouhé
 
Pokud čelíte chybám, které si stěžují na to, že klíč dokumentu je delší než 1024 znaků, lze tuto funkci použít ke zkrácení délky klíče dokumentu.

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
