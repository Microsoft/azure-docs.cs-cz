---
title: Mapování polí v indexerech Azure Search
description: Nakonfigurujte mapování polí indexeru Azure Search pro rozdíly v názvy polí a formátovaná data
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: 01edc7001ec463cb16d8b6c715794b0028508143
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404713"
---
# <a name="field-mappings-in-azure-search-indexers"></a>Mapování polí v indexerech Azure Search
Když pomocí indexerů Azure Search, čas od času najdete sami v situacích, kde vstupní data poměrně neodpovídá schématu cílový index. V takových případech můžete použít **mapování polí** pro transformaci dat do požadované podoby.

Některé situace, ve kterém jsou užitečné mapování polí:

* Zdroj dat má pole `_id`, ale Azure Search neumožňuje názvy polí začínající podtržítkem. Mapování polí můžete pole "přejmenovat".
* Chcete naplnit pole několik polí v indexu se stejnými daty zdroj dat, například vzhledem k tomu, že chcete použít jiný analyzátory do těchto polí. Mapování polí umožňují "vytvořit fork" pole datového zdroje.
* Potřebujete z formátu Base64. kódování nebo dekódování vaše data. Mapování polí podporovat několik **mapování funkce**, včetně funkcí pro Base64, kódování a dekódování.   

## <a name="setting-up-field-mappings"></a>Nastavení mapování polí
Při vytváření nového pomocí indexeru můžete přidat mapování polí [vytvoření indexeru](https://msdn.microsoft.com/library/azure/dn946899.aspx) rozhraní API. Mapování polí na indexování pomocí indexeru můžete spravovat [aktualizace Indexer](https://msdn.microsoft.com/library/azure/dn946892.aspx) rozhraní API.

Mapování polí se skládá ze tří částí:

1. A `sourceFieldName`, který reprezentuje pole ve zdroji dat. Tato vlastnost se vyžaduje.
2. Volitelně `targetFieldName`, která představuje pole v indexu vyhledávání. Pokud tento parametr vynechán, stejný název jako zdroj dat se používá.
3. Volitelně `mappingFunction`, které můžete transformovat svoje data pomocí jedné z několika předdefinovanými funkcemi. Úplný seznam funkcí je [níže](#mappingFunctions).

Mapování polí jsou přidány do `fieldMappings` pole na definice indexeru.

Tady je například jak vám přizpůsobilo rozdílům v názvy polí:

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

Indexer můžete mít více mapování polí. Například tady je jak vám může "vytvořit fork" pole:

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

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funkce mapování polí
Tyto funkce jsou aktuálně podporovány:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Provádí *adresu URL bezpečné* kódování Base64 vstupního řetězce. Předpokládá, že vstup je UTF-8.

### <a name="sample-use-case---document-key-lookup"></a>Případ použití ukázkové - hledání dokumentů
Pouze adresu URL bezpečné znaky se mohou objevit v klíči dokumentu Azure Search (protože zákazníci musí být schopni vyřešit dokumentu pomocí [rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/lookup-document), například). Pokud data obsahují adresu URL problematické znaky a chcete ho použít k naplnění klíčů pole v indexu vyhledávání, použijte tuto funkci. Jakmile je zakódován klíč, můžete použít k načtení původní hodnoty dekódovat z formátu base64. Podrobnosti najdete v tématu [base64 kódování a dekódování](#base64details) oddílu.

#### <a name="example"></a>Příklad:
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Vzorové případy použití – načíst původního klíče
Máte indexeru blob, který indexů objektů BLOB pomocí cesty metadata objektu blob jako klíč dokumentu. Po načtení klíč kódovaného dokumentu, budete chtít dekódovat cestu a stáhnout objekt blob.

#### <a name="example"></a>Příklad:
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
 ```

Pokud není nutné při hledání dokumentů podle klíčů a také není nutné k dekódování kódovaný obsahu, můžete pouze vynecháte `parameters` pro funkci mapování, která má výchozí hodnotu `useHttpServerUtilityUrlTokenEncode` k `true`. V opačném případě najdete v článku [podrobnosti ve formátu base64](#base64details) části rozhodnout nastavení, které chcete použít.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Provádí ve formátu Base64 dekódování vstupního řetězce. Vstup se předpokládá, že *adresu URL bezpečné* řetězec s kódováním Base64.

### <a name="sample-use-case"></a>Případ použití vzorku
Hodnoty vlastních metadat objektu BLOB musí být kódováním ASCII. K reprezentaci libovolného řetězce UTF-8 v vlastní metadata objektu blob můžete použít kódování Base64. Ale aby hledání smysl, můžete tuto funkci zapnout kódovaná data zpět do "běžné" řetězce při naplňování indexu vyhledávání.

#### <a name="example"></a>Příklad:
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Pokud nezadáte žádné `parameters`, klikněte na výchozí hodnotu `useHttpServerUtilityUrlTokenDecode` je `true`. Zobrazit [podrobnosti ve formátu base64](#base64details) části rozhodnout nastavení, které chcete použít.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Podrobnosti ve formátu base64, kódování a dekódování
Služba Azure Search podporuje dvě kódování base64: adresa URL HttpServerUtility kódování base64 token a adresu URL bezpečné bez odsazení. Budete muset použít stejné kódování jako funkce mapování, pokud chcete zakódovat klíč dokumentu najdete nahoru, hodnotu, která se dekódovat. že se indexer zakódování nebo dekódování pole kódovaný pomocí indexeru.

Pokud `useHttpServerUtilityUrlTokenEncode` nebo `useHttpServerUtilityUrlTokenDecode` parametry pro kódování a dekódování v uvedeném pořadí jsou nastaveny na `true`, pak `base64Encode` se chová jako [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) a `base64Decode` se chová jako [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Pokud nepoužíváte úplné rozhraní .NET Framework (to znamená, že používáte .NET Core nebo jiné programovací prostředí) k vytvoření hodnoty klíče, abyste emulovali chování Azure Search, pak byste měli nastavit `useHttpServerUtilityUrlTokenEncode` a `useHttpServerUtilityUrlTokenDecode` k `false`. V závislosti na knihovně použijete base64, kódování a dekódování nástroj funkce může být jiné než Azure Search.

Následující tabulka porovnává různé base64 kódování řetězce `00>00?00`. K určení vyžaduje další zpracování (pokud existuje) pro vaše funkce ve formátu base64, použít knihovnu kódování funkce v řetězci `00>00?00` a porovnat výstupu se očekávaný výstup `MDA-MDA_MDA`.

| Kódování | Výstup zakódovat do formátu Base64 | Další zpracování po kódování knihovny | Další zpracování před dekódování knihovny |
| --- | --- | --- | --- |
| Ve formátu Base64 s odsazením | `MDA+MDA/MDA=` | Použijte adresu URL bezpečné znaků a odeberte odsazení | Použít standardní base64 znaky a přidejte odsazení |
| Ve formátu Base64 bez odsazení | `MDA+MDA/MDA` | Použijte adresu URL bezpečné znaků | Použít standardní base64 znaky |
| Bezpečná pro adresu URL ve formátu base64 s odsazením | `MDA-MDA_MDA=` | Odebere odsazení | Přidat odsazení |
| Bezpečná pro adresu URL ve formátu base64 bez odsazení | `MDA-MDA_MDA` | Žádný | Žádný |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Rozdělí pole řetězce, pomocí zadaného oddělovače a vybere token na konkrétní pozici v výsledný rozdělení.

Například, pokud je vstup `Jane Doe`, `delimiter` je `" "`(místo) a `position` je 0, je výsledkem `Jane`; Pokud `position` 1, výsledkem je `Doe`. Pokud umístění odkazuje na token, který neexistuje, vrátí se chyba.

### <a name="sample-use-case"></a>Případ použití vzorku
Zdroj dat obsahuje `PersonName` pole a chcete, aby se jako dva samostatné `FirstName` a `LastName` pole. Tuto funkci můžete použít k rozdělení vstupního použití znaku mezery jako oddělovače.

### <a name="parameters"></a>Parametry
* `delimiter`: řetězec pro použití jako oddělovače při rozdělení vstupního řetězce.
* `position`: celé číslo pozice s nulovým základem tokenu k výběru po rozdělení vstupního řetězce.    

### <a name="example"></a>Příklad:
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

## <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Převede řetězec formátovaný jako pole řetězců JSON, který slouží k naplnění pole řetězců `Collection(Edm.String)` pole v indexu.

Například, pokud je vstupní řetězec `["red", "white", "blue"]`, pak pole cílového typu `Collection(Edm.String)` naplní tři hodnoty `red`, `white`, a `blue`. K zadávání hodnot, které nelze analyzovat jako pole řetězce JSON vrátí se chyba.

### <a name="sample-use-case"></a>Případ použití vzorku
Azure SQL database nemá integrované datový typ, který se přirozeně mapuje `Collection(Edm.String)` pole ve službě Azure Search. K naplnění kolekce pole řetězců, formátování zdroje dat jako pole řetězců JSON a tuto funkci používat.

### <a name="example"></a>Příklad:
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Pomozte nám vylepšit Azure Search
Pokud máte požadavky na funkce nebo nápady na vylepšení, kontaktujte nás na naše [webu UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
