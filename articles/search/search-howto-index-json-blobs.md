---
title: Hledání v blobech JSON
titleSuffix: Azure Cognitive Search
description: Procházení objektů BLOB služby Azure JSON pro obsah textu pomocí indexeru objektů BLOB v Azure Kognitivní hledání. Indexery automatizují přijímání dat pro vybrané zdroje dat, jako je Azure Blob Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259046"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Indexování objektů BLOB JSON pomocí indexeru objektů BLOB v Azure Kognitivní hledání

V tomto článku se dozvíte, jak [nakonfigurovat indexer objektů BLOB](search-howto-indexing-azure-blob-storage.md) pro objekty blob, které se skládají z dokumentů JSON. Objekty blob JSON ve službě Azure Blob Storage obvykle předpokládají některou z těchto forem:

+ Jeden dokument JSON
+ Dokument JSON obsahující pole dobře formátovaného elementu JSON
+ Dokument JSON obsahující několik entit, které jsou oddělené znakem nového řádku

Indexer objektů BLOB poskytuje **`parsingMode`** parametr pro optimalizaci výstupu vyhledávacího dokumentu založeného na režimech analýzy struktury sestávají z následujících možností:

| parsingMode | Dokument JSON | Description |
|--------------|-------------|--------------|
| **`json`** | Jedna na objekt BLOB | výchozí Analyzuje objekty blob JSON jako jeden blok textu. Každý objekt BLOB JSON se bude jednat o jediný dokument hledání. |
| **`jsonArray`** | Víc na objekt BLOB | Analyzuje pole JSON v objektu blob, kde se každý prvek pole stal samostatným dokumentem hledání.  |
| **`jsonLines`** | Víc na objekt BLOB | Analyzuje objekt blob, který obsahuje více entit JSON (také pole), s jednotlivými prvky oddělenými novým řádkem. Indexer spustí nový dokument hledání za každým novým řádkem. |

U obou **`jsonArray`** i byste **`jsonLines`** měli projít část [indexování jednoho objektu BLOB a získat tak mnoho dokumentů hledání](search-howto-index-one-to-many-blobs.md) , abyste porozuměli tomu, jak indexer objektů BLOB zpracovává nejednoznačnost klíče dokumentu pro více dokumentů hledání vytvořených ze stejného objektu BLOB.

V definici indexeru můžete volitelně nastavit [mapování polí](search-indexer-field-mappings.md) a vybrat, které vlastnosti zdrojového dokumentu JSON se použijí k naplnění cílového vyhledávacího indexu. Například při použití **`jsonArray`** režimu analýzy, pokud pole existuje jako vlastnost nižší úrovně, můžete nastavit **`document root`** vlastnost určující, kde je pole umístěno v objektu BLOB.

V následujících částech jsou popsány jednotlivé režimy podrobněji. Pokud neznáte klienty a koncepty indexeru, přečtěte si téma [Vytvoření indexeru vyhledávání](search-howto-create-indexers.md). Měli byste se také seznámit s podrobnostmi o [základní konfiguraci indexeru objektů BLOB](search-howto-indexing-azure-blob-storage.md), který se tady neopakuje.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Indexovat jednotlivé dokumenty JSON (jedna za objekt BLOB)

Ve výchozím nastavení indexery objektů BLOB analyzují objekty blob JSON jako jeden blok textu, jeden vyhledávací dokument pro každý objekt BLOB v kontejneru. Pokud je kód JSON strukturovaný, dokument hledání může odrážet tuto strukturu, přičemž jednotlivé prvky představují jednotlivé položky. Předpokládejme například, že máte v úložišti objektů BLOB v Azure následující dokument JSON:

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Indexer objektů BLOB analyzuje dokument JSON do jednoho vyhledávaného dokumentu a načítá index spárováním "text", "datePublished" a "Tags" ze zdroje proti identicky pojmenovanému a typovanému poli indexu. V případě indexu s poli "text", "datePublished a" Tags "může indexer objektů BLOB odvodit správné mapování bez mapování polí přítomného v žádosti.

Přestože je výchozím chováním jeden vyhledávací dokument pro každý objekt BLOB JSON, nastavení režimu analýzy JSON mění interní mapování polí pro obsah a podporuje pole v rámci `content` skutečných polí indexu vyhledávání. Ukázková definice indexeru pro **`json`** režim analýzy může vypadat takto:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Stejně jako u všech indexerů platí, že pokud se pole jednoznačně neshodují, měli byste očekávat explicitní zadání [mapování jednotlivých polí](search-indexer-field-mappings.md) , pokud nepoužíváte mapování implicitních polí dostupných pro obsah objektů BLOB a metadata, jak je popsáno v [základní konfiguraci indexeru objektů BLOB](search-howto-indexing-azure-blob-storage.md).

### <a name="json-example-single-hotel-json-files"></a>Příklad JSON (soubory JSON s jedním hotelem)

[Sada dat dokumentu JSON pro Hotel](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) na GitHubu je užitečná pro testování analýzy JSON, kde každý objekt BLOB představuje strukturovaný soubor JSON. Datové soubory můžete nahrát do úložiště objektů BLOB a pomocí průvodce **importem dat** rychle vyhodnotit, jak se tento obsah analyzuje na jednotlivé dokumenty hledání. 

Datová sada se skládá z pěti objektů blob, z nichž každý obsahuje dokument hotelu s kolekcí adres a kolekcí místností. Indexer objektů BLOB detekuje obě kolekce a odráží strukturu vstupních dokumentů ve schématu indexu.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analyzovat pole JSON

Alternativně můžete použít možnost pole JSON. Tato možnost je užitečná v případě, že objekty blob obsahují pole dobře formátovaného objektu JSON a vy chcete, aby se každý prvek stal samostatným dokumentem hledání. Pomocí **`jsonArrays`** , následující objekt BLOB JSON vytvoří tři samostatné dokumenty, každý s `"id"` `"text"` poli a.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

**`parameters`** Vlastnost v indexeru obsahuje hodnoty režimu analýzy. V případě pole JSON by definice indexeru měla vypadat podobně jako v následujícím příkladu.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>Příklad jsonArrays (data ukázek klinických pokusů)

[Data JSON pro klinická hodnocení](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) na GitHubu jsou užitečná pro testování analýzy polí JSON. Datové soubory můžete nahrát do úložiště objektů BLOB a pomocí průvodce **importem dat** rychle vyhodnotit, jak se tento obsah analyzuje na jednotlivé dokumenty hledání. 

Datová sada se skládá z osmi objektů blob, z nichž každý obsahuje pole JSON entit, za celkem 100 entit. Entity se liší podle toho, která pole jsou vyplněna, ale konečný výsledek je jeden hledaný dokument na entitu ze všech polí ve všech objektech blob.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>Analýza vnořených polí JSON

Pro pole JSON obsahující vnořené prvky lze určit, **`documentRoot`** aby označovala strukturu s více úrovněmi. Například pokud vaše objekty blob vypadají takto:

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

Použijte tuto konfiguraci k indexování pole obsaženého ve `level2` vlastnosti:

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>Analyzovat entity JSON oddělené newlines

Pokud váš objekt BLOB obsahuje více entit JSON oddělených novým řádkem a chcete, aby se každý prvek stal samostatným dokumentem hledání, použijte **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

V případě řádků JSON by definice indexeru měla vypadat podobně jako v následujícím příkladu.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>Příklad jsonLines (ukázková data caselaw)

[Datová sada CASELAW JSON](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) na GitHubu je užitečná pro testování analýzy nových řádků JSON. Stejně jako u jiných ukázek můžete tato data nahrát do úložiště objektů BLOB a pomocí průvodce **importem dat** rychle vyhodnotit dopad režimu analýzy na jednotlivé objekty blob.

Datová sada se skládá z jednoho objektu BLOB obsahujícího 10 entit JSON oddělených novým řádkem, kde každá Entita popisuje jeden právní případ. Konečný výsledek je jeden hledaný dokument na entitu.

## <a name="map-json-fields-to-search-fields"></a>Mapování polí JSON na vyhledávací pole

Mapování polí slouží k přidružení zdrojového pole k cílovému poli v situacích, kdy názvy a typy polí nejsou totožné. Mapování polí lze ale použít také k porovnávání částí dokumentu JSON a "výtah" do polí nejvyšší úrovně v dokumentu hledání.

Následující příklad znázorňuje tento scénář. Další informace o mapování polí obecně najdete v tématu [mapování polí](search-indexer-field-mappings.md).

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Vybere index vyhledávání s následujícími poli: `text` typu `Edm.String` , `date` typu `Edm.DateTimeOffset` a `tags` typu `Collection(Edm.String)` . Všimněte si rozdílů mezi "datePublished" ve zdroji a `date` v poli v indexu. K namapování JSON na požadovaný tvar použijte následující mapování polí:

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

Zdrojová pole jsou určena pomocí zápisu [ukazatele JSON](https://tools.ietf.org/html/rfc6901) . Začínáte s dopředným lomítkem, kde odkazujete na kořen dokumentu JSON, a pak vyberte požadovanou vlastnost (na libovolné úrovni vnoření) pomocí cesty oddělené lomítkem.

Můžete také odkazovat na jednotlivé prvky pole pomocí indexu založeného na nule. Chcete-li například vybrat první prvek pole "značky" z výše uvedeného příkladu, použijte mapování polí takto:

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Pokud **`sourceFieldName`** odkazuje na vlastnost, která neexistuje v objektu BLOB JSON, toto mapování se přeskočí bez chyby. Toto chování umožňuje indexování pokračovat pro objekty blob JSON, které mají jiné schéma (což je běžný případ použití). Vzhledem k tomu, že nedochází k žádné kontrole ověřování, zkontrolujte mapování pečlivě pro překlepy, aby nedošlo ke ztrátě dokumentů z nesprávného důvodu.
>

## <a name="next-steps"></a>Další kroky

+ [Konfigurace indexerů objektů BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Definování mapování polí](search-indexer-field-mappings.md)
+ [Přehled indexerů](search-indexer-overview.md)
+ [Indexování objektů BLOB CSV pomocí indexeru objektů BLOB](search-howto-index-csv-blobs.md)
+ [Kurz: hledání částečně strukturovaných dat z Azure Blob Storage](search-semi-structured-data.md)