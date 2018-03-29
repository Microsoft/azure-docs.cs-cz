---
title: Mapování polí v Azure Search indexery
description: Konfigurace mapování polí indexer Azure Search, aby se zohlednily rozdíly v názvy polí a reprezentace dat
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''
ms.assetid: 0325a4de-0190-4dd5-a64d-4e56601d973b
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/30/2017
ms.author: eugenesh
ms.openlocfilehash: e4d6960e540641405b879064a8064d45521dc04f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="field-mappings-in-azure-search-indexers"></a>Mapování polí v Azure Search indexery
Při použití indexerů Azure Search, můžete sami někdy nalézt v situacích, kde vstupní data poměrně neodpovídá schématu cílový index. V takových případech můžete použít **pole mapování** pro transformaci dat do požadovaného tvaru.

Některé situace, kdy jsou užitečné mapování polí:

* Váš zdroj dat má pole `_id`, ale Azure Search neumožňuje názvy polí začíná podtržítkem. Mapování polí umožňuje "přejmenovat" pole.
* Chcete naplnění několik polí v indexu se stejnými daty zdroje dat, například vzhledem k tomu, že chcete použít jiný analyzátorů těchto polí. Mapování polí umožňují "rozvětvit" pole datového zdroje.
* Budete potřebovat formátu Base64 zakódování nebo dekódování vaše data. Mapování polí podporují několik **mapování funkce**, včetně funkcí pro Base64 kódování a dekódování.   

## <a name="setting-up-field-mappings"></a>Nastavení mapování polí
Při vytváření nového pomocí indexeru můžete přidat mapování polí [vytvořit Indexer](https://msdn.microsoft.com/library/azure/dn946899.aspx) rozhraní API. Můžete spravovat mapování polí na indexování indexer pomocí [aktualizace Indexer](https://msdn.microsoft.com/library/azure/dn946892.aspx) rozhraní API.

Mapování polí se skládá ze tří částí:

1. A `sourceFieldName`, která představuje pole ve zdroji dat. Tato vlastnost je vyžadována.
2. Volitelný `targetFieldName`, která představuje pole v indexu vyhledávání. Pokud tento parametr vynechán, stejný název jako zdroj dat se používá.
3. Volitelný `mappingFunction`, které můžete transformovat data pomocí jedné z několik předdefinovaných funkcí. Úplný seznam funkcí je [pod](#mappingFunctions).

Mapování polí jsou přidány do `fieldMappings` poli v definici indexer.

Můžete zde je ukázka, jak můžete přizpůsobilo rozdílům v názvy polí:

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

Indexer může mít několik mapování polí. Například je zde jak vám může "rozvětvit" pole:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Služba Azure Search používá porovnávání překládat názvy polí a funkce v mapování polí. To je vhodné (není třeba získat správné všechny malá a velká písmena), ale znamená, že nemůže mít zdroj dat nebo index pole, která se liší pouze v případě.  
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
Provede *URL bezpečných* vstupní řetězec kódování Base64. Předpokládá, že vstup je UTF-8.

### <a name="sample-use-case---document-key-lookup"></a>Případ použití ukázkové - vyhledávání klíčů dokumentu
Pouze adresy URL bezpečných znaky se mohou objevit v klíči dokumentu Azure Search (protože zákazníci musí umět vyřešit pomocí dokumentu [rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/lookup-document), například). Pokud vaše data obsahuje adresu URL unsafe znaky a chcete použít k naplnění klíčové pole v indexu vyhledávání, použijte tuto funkci. Jakmile je zakódován klíč, můžete použít base64 dekódovat načíst původní hodnotu. Podrobnosti najdete v tématu [base64 kódování a dekódování](#base64details) části.

#### <a name="example"></a>Příklad:
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Případ použití ukázkové - načtení původního klíče
Máte indexer objektů blob, který indexů objektů BLOB s cesta metadata objektu blob jako klíč dokumentu. Po načtení klíč kódovaného dokumentu, budete chtít dekódovat cestu a stáhnout objektu blob.

#### <a name="example"></a>Příklad:
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
```

Pokud nepotřebujete pro vyhledávání dokumentů na klíče a také není nutné dekódování zakódovaný obsahu, můžete právě ponechat `parameters` pro mapování funkce, které výchozí `useHttpServerUtilityUrlTokenEncode` k `true`. Jinak, najdete v části [base64 podrobnosti](#base64details) části rozhodnout, která nastavení se mají použít.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Provede Base64 dekódování vstupního řetězce. Vstup se předpokládá, že *URL bezpečných* řetězec s kódováním Base64.

### <a name="sample-use-case"></a>Ukázka případ použití
Hodnoty vlastní metadata objektu BLOB musí být kódováním ASCII. Kódování Base64 můžete reprezentovat libovolný řetězce UTF-8 v vlastní metadata objektu blob. Ale aby vyhledávání smysl, můžete tuto funkci zapnout kódovaného data zpět do "Regulérní" řetězce při naplňování indexu vyhledávání.

#### <a name="example"></a>Příklad:
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Pokud nezadáte žádné `parameters`, pak na výchozí hodnotu `useHttpServerUtilityUrlTokenDecode` je `true`. V tématu [base64 podrobnosti](#base64details) části rozhodnout, která nastavení se mají použít.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Podrobnosti o base64 kódování a dekódování
Služba Azure Search podporuje dvě kódování base64: adresa URL HttpServerUtility kódování base64 tokenu a bezpečné pro adresu URL bez odsazení. Budete muset použít stejné kódování jako mapování funkce, pokud chcete zakódovat klíč dokumentu pro vzhledu nahoru, hodnota rozluštit indexeru zakódování nebo dekódování pole kódovaný se indexer.

Pokud používáte rozhraní .NET Framework, můžete nastavit `useHttpServerUtilityUrlTokenEncode` a `useHttpServerUtilityUrlTokenDecode` k `true`, kódování a dekódování v uvedeném pořadí. Potom `base64Encode` chová jako [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) a `base64Decode` chová jako [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Pokud nepoužíváte rozhraní .NET Framework, pak byste měli nastavit `useHttpServerUtilityUrlTokenEncode` a `useHttpServerUtilityUrlTokenDecode` k `false`. V závislosti na knihovně použijete Base64, pomocí kódování a dekódování nástroj funkce se může lišit od Azure Search.

Následující tabulka porovnává kódování base64 jiné řetězce `00>00?00`. Pokud chcete zjistit vyžaduje další zpracování (pokud existuje) pro funkce base64, použít knihovnu kódování funkce na řetězec `00>00?00` a porovnání výstup s očekávaný výstup `MDA-MDA_MDA`.

| Encoding | Výstup kódování Base64 | Další zpracování po kódování knihovny | Další zpracování před dekódování knihovny |
| --- | --- | --- | --- |
| Base64 s odsazení | `MDA+MDA/MDA=` | Použít znaky adresy URL bezpečných a odeberte odsazení | Použít standardní base64 znaky a přidejte odsazení |
| Base64 bez odsazení | `MDA+MDA/MDA` | Použít znaky adresy URL bezpečných | Použít standardní base64 znaky |
| Base64 URL bezpečné pomocí odsazení | `MDA-MDA_MDA=` | Odebrat odsazení | Přidat odsazení |
| Adresa URL bezpečných base64 bez odsazení | `MDA-MDA_MDA` | Žádný | Žádné |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Rozdělí pole řetězce pomocí zadaného oddělovače a vybere token na zadané pozici v výsledné rozdělení.

Například, pokud je vstupní `Jane Doe`, `delimiter` je `" "`(místo) a `position` je 0, výsledkem je `Jane`; Pokud `position` je 1, výsledkem je `Doe`. Pokud pozice odkazuje na token, který neexistuje, je vrácena chyba.

### <a name="sample-use-case"></a>Ukázka případ použití
Zdroj dat obsahuje `PersonName` pole a chcete indexu jako dva samostatné `FirstName` a `LastName` pole. Tato funkce slouží k rozdělení vstupu pomocí znak mezery jako oddělovače.

### <a name="parameters"></a>Parametry
* `delimiter`: řetězec, který chcete použít jako oddělovače při rozdělování vstupní řetězec.
* `position`: celé číslo pozice s nulovým základem tokenu a vyberte po vstupní řetězec rozdělen.    

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
Transformuje řetězec formátovaný jako pole JSON řetězců do pole řetězec, který slouží k naplnění `Collection(Edm.String)` pole v indexu.

Například, pokud je vstupní řetězec `["red", "white", "blue"]`, pak cílové pole typu `Collection(Edm.String)` vyplní pomocí tří hodnot `red`, `white`, a `blue`. Pro vstupní hodnoty, které nelze analyzovat jako řetězec pole JSON je vrácena chyba.

### <a name="sample-use-case"></a>Ukázka případ použití
Databáze SQL Azure nemá předdefinované datový typ, který mapuje přirozeně `Collection(Edm.String)` pole ve službě Azure Search. K naplnění kolekci polí s řetězcem, formátování zdroj dat jako pole řetězců JSON a tuto funkci můžete používat.

### <a name="example"></a>Příklad:
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Pomozte nám vylepšit Azure Search
Pokud máte žádosti o funkce nebo vylepšení nápady, kontaktujte nás na našem [UserVoice lokality](https://feedback.azure.com/forums/263029-azure-search/).
