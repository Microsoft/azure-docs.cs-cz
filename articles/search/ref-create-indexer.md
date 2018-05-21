---
title: Vytvoření Indexer (verze rozhraní api REST služby vyhledávání systému Azure = 2017-11-11-Preview)
description: Ve verzi preview rozhraní API indexery jsou rozšířené a zahrnout outputFieldMapping parametry, které slouží k mapování obohacení výstup na pole v indexu Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: 595502ecf0a78491c73800e8077de65707c7a486
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Vytvoření Indexer (verze rozhraní api REST služby vyhledávání systému Azure = 2017-11-11-Preview)

Tento referenční dokumentace rozhraní API je specifické pro verzi preview verze dokumentace, přidání [kognitivní vyhledávání](cognitive-search-concept-intro.md) prvky rozhraní API vytvořit Indexer. Stejně jako u [všeobecně dostupná](https://docs.microsoft.com/rest/api/searchservice/create-indexer) verze, můžete vytvořit nový indexer v rámci služby Azure Search pomocí požadavku HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json  
    api-key: [admin key]  
```  
**Klíč api-key** musí být klíče správce (na rozdíl od klíč dotazů). Informace naleznete v sekci ověřování v [zabezpečení ve službě Azure Search](search-security-overview.md) Další informace o klíčích. [Vytvoření služby Azure Search na portálu](search-create-service-portal.md) vysvětluje, jak získat adresu URL služby a klíče vlastnosti používané v požadavku.

Alternativně můžete použít PUT a zadejte název zdroje dat v identifikátoru URI. Pokud zdroj dat neexistuje, bude vytvořen.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
**Verze rozhraní api** je vyžadován. Aktuální obecně dostupná verze je `api-version=2017-11-11`, ale musíte verzi preview pro kognitivní vyhledávání: `api-version=2017-11-11-Preview`.  V tématu [verze rozhraní API ve službě Azure Search](search-api-versions.md) podrobnosti.

Pro příslušnou platformu data pokyny k vytváření indexery, začínat [indexery přehled](search-indexer-overview.md), což zahrnuje úplný seznam [souvisejících článcích](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  Maximální počet indexery povoleno se liší podle cenové úrovně. Bezplatné služby umožňuje až 3 indexery. Standardní služby umožňuje 50 indexery. Standardní služby HD nepodporují indexery vůbec. V tématu [omezení služby](search-limits-quotas-capacity.md) podrobnosti.    

## <a name="request"></a>Žádost  

A [zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source), [index](https://docs.microsoft.com/rest/api/searchservice/create-index), a [skillset](ref-create-skillset.md) jsou součástí [indexer](search-indexer-overview.md) definice, ale každý je nezávislé komponenty, které je možné v různé kombinace. Například můžete použít stejný zdroj dat s více indexery, nebo stejný index s více indexery nebo několik indexerů zápis do jednoho index.

 Text žádosti obsahuje definici indexer, pomocí následujících částí.

+ [dataSourceName](#dataSourceName)
+ [targetIndexName](#targetIndexName)
+ [skillsetName](#skillset)
+ [schedule](#indexer-schedule)
+ [parameters](#indexer-parameters)
+ [fieldMappings](#field-mappings)
+ [outputFieldMappings](#output-fieldmappings)

## <a name="request-syntax"></a>Syntaxe požadavku

Syntaxe pro vytváření struktury datová část požadavku je následující. Ukázková žádost zajišťuje později v tomto článku.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "skillsetName" : "Required for cognitive search enrichment",
    "schedule" : { Optional, but immediately runs once if unspecified. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```
<a name="dataSourceName"></a>

### <a name="datasourcename"></a>"dataSourceName"

A [definice zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) často obsahuje vlastnosti, které můžete použít indexer zneužít charakteristiky platformu zdroje. Jako takový zdroj dat, který můžete předat indexeru zjišťuje dostupnost určité vlastnosti a parametry, takový typ obsahu filtrování v Azure BLOB nebo vypršení časového limitu dotazu pro databázi SQL Azure. 

<a name="targetIndexName"></a>

### <a name="targetindexname"></a>"targetIndexName"

[Schéma indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) definuje kolekci polí obsahující s možností vyhledávání, filtrování, získat a dalších práv, které určují, jak se používá pole. Během indexování, indexeru prochází zdroj dat, volitelně praskliny dokumenty a extrahuje informace, serializuje výsledky do formátu JSON a indexy datové části podle schéma definované pro indexu.

<a name="skillset"></a>

### <a name="skillsetname"></a>"skillsetName"

[Kognitivní vyhledávání (preview)](cognitive-search-concept-intro.md) odkazuje na přirozeného jazyka a bitové kopie zpracování funkce ve službě Azure Search, použít při přijímání dat extrahovat entity, klíče frází, jazyka, informace z obrázků a tak dále. Transformace, které jsou použity k obsahu jsou prostřednictvím *dovednosti*, který zkombinovat do jednoho [ *skillset*](ref-create-skillset.md), jeden na každého indexer. Stejně jako u zdroje dat a indexy skillset je nezávislé kterou přiřadit indexer. Můžete změnit účel skillset s jinými indexery, ale každý indexer lze použít pouze jeden skillset najednou.
 
<a name="indexer-schedule"></a>

### <a name="schedule"></a>"plán"  
Indexer Volitelně můžete zadat plán. Bez plánu, spuštění indexeru okamžitě po odeslání žádosti o: připojování k procházení a indexování zdroje dat. V některých případech, včetně dlouho běžící úlohy indexování, se používají pro plány [rozšířit okno zpracování](search-howto-reindex.md#scale-out-indexing) nad maximální 24 hodin. Pokud se nachází plánu, spuštění indexeru pravidelně podle plánu. Je součástí plánovače; nelze použít externí plánovače. A **plán** má následující atributy: 

-   **interval**: vyžaduje. Doba trvání hodnotu, která určuje interval nebo období pro indexer se spustí. Nejkratší povolený interval je pět minut; nejdelší je jeden den. Musí být naformátovaná jako hodnota "hodnoty doby podle" XSD (omezená podmnožina [ISO 8601 trvání](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) hodnotu). Je tento vzor: `"P[nD][T[nH][nM]]".` příklady: `PT15M` pro každých 15 minut, `PT2H` pro každé 2 hodiny.  

-   **startTime**: volitelné. Pokud by se měl indexer spustit systémem datetime UTC.  

<a name="indexer-parameters"></a>

### <a name="parameters"></a>"parametry"

Indexer může volitelně trvat parametry konfigurace, které upravují chování za běhu. Parametry konfigurace jsou oddělený čárkami na žádosti indexer. 

```json
    {
      "name" : "my-blob-indexer-for-cognitive-search",
      ... other indexer properties
      "parameters" : { "maxFailedItems" : "15", "batchSize" : "100", "configuration" : { "parsingMode" : "json", "indexedFileNameExtensions" : ".json, .jpg, .png", "imageAction" : "generateNormalizedImages", "dataToExtract" : "contentAndMetadata" } }
    }
```

#### <a name="general-parameters-for-all-indexers"></a>Obecné parametry pro všechny indexery

| Parametr | Typ a povolené hodnoty   | Využití  |
|-----------|------------|--------------------------|--------|
| `"batchSize"` | Integer<br/>Výchozí hodnota je specifická pro zdroj (1 000 pro Azure SQL Database a Azure Cosmos DB, 10 pro Azure Blob Storage) | Určuje počet položek, které čtou ze zdroje dat a indexované jako jeden batch za účelem zlepšení výkonu. |
| `"maxFailedItems"` | Integer<br/>Výchozí hodnota je 0. | Počet chyb na tolerovat spuštění indexeru považuje za selhání. Nastavte na hodnotu -1, pokud nechcete, aby všechny chyby indexování proces zastavíte. Můžete načíst informace o selhání položek pomocí [získání stavu Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).  |
| `"maxFailedItemsPerBatch"` | Integer<br/>Výchozí hodnota je 0. | Počet chyb na tolerovat v každé dávce, spuštění indexeru považuje za selhání. Nastavte na hodnotu -1, pokud nechcete, aby všechny chyby indexování proces zastavíte. |

#### <a name="blob-configuration-parameters"></a>Parametry konfigurace objektů BLOB

Několik parametrů, jako jsou výhradně pro konkrétní indexeru, [objektů blob v Azure indexování](search-howto-indexing-azure-blob-storage.md).

| Parametr | Typ a povolené hodnoty   | Využití  |
|-----------|---------------------------|--------|
| `"parsingMode"` | Řetězec<br/>`"text"`<br/>`"delimitedText"`<br/>`"json"`<br/>`"jsonArray"`  | Pro [objektů BLOB Azure](search-howto-indexing-azure-blob-storage.md), nastavte pro `text` ke zlepšení výkonu indexování na prostý text souborů v blob storage. <br/>Pro [objekty BLOB CSV](search-howto-index-csv-blobs.md), nastavte pro `delimitedText` při objekty BLOB jsou prostý souborů CSV. <br/>Pro [objekty BLOB JSON](search-howto-index-json-blobs.md), nastavte pro `json` strukturovaná extrahování obsahu nebo `jsonArray` (preview) k extrakci jednotlivé prvky pole jako samostatné dokumenty ve službě Azure Search. |
| `"excludedFileNameExtensions"` | Řetězec<br/>čárkami oddělený seznam | Pro [objektů BLOB Azure](search-howto-indexing-azure-blob-storage.md), ignorovat všechny typy souborů v seznamu. Například může vyloučit "PNG, PNG, .mp4" Přeskočit prostřednictvím těchto souborů během indexování. | 
| `"indexedFileNameExtensions"` | Řetězec<br/>čárkami oddělený seznam | Pro [objektů BLOB Azure](search-howto-indexing-azure-blob-storage.md), pokud přípona souboru je v seznamu vybere objekty BLOB. Například může zaměřit na konkrétní aplikaci indexování soubory ".docx, PPTX, msg" konkrétně zahrnout tyto typy souborů. | 
| `"failOnUnsupportedContentType"` | true (pravda) <br/>NEPRAVDA (výchozí) | Pro [objektů BLOB Azure](search-howto-indexing-azure-blob-storage.md), nastavte pro `false` Pokud chcete pokračovat, indexování při zjistil se nepodporovaný typ obsahu, a předem neznáte všechny typy obsahu (přípony souborů). |
| `"failOnUnprocessableDocument"` | true (pravda) <br/>NEPRAVDA (výchozí)| Pro [objektů BLOB Azure](search-howto-indexing-azure-blob-storage.md), nastavte pro `false` Pokud chcete pokračovat, indexování Pokud dokument selže indexování. |
| `"indexStorageMetadataOnlyForOversizedDocuments"` | true (pravda) <br/>NEPRAVDA (výchozí)| Pro [objektů BLOB Azure](search-howto-indexing-azure-blob-storage.md), nastavte tuto vlastnost `true` stále indexování úložiště metadata pro obsah objektu blob, která je příliš velká pro zpracování.  Příliš velký objekty BLOB jsou považovány za chyby ve výchozím nastavení. Omezení pro velikost objektu blob, najdete v části [omezení služby](search-limits-quotas-capacity.md). |
| `"delimitedTextHeaders"` | Řetězec<br/>čárkami oddělený seznam| Pro [CSV objekty BLOB (preview)](search-howto-index-csv-blobs.md), určuje seznam s položkami oddělenými čárkou záhlaví sloupců, užitečné pro mapování polí zdrojového do cílového pole v indexu. |
| `"delimitedTextDelimiter"` | Řetězec<br/>Uživatelem definované | Pro [CSV objekty BLOB (preview)](search-howto-index-csv-blobs.md), určuje konec řádku oddělovač pro soubory CSV, kde každý řádek spustí nový dokument (například ""|"`).  |
| `"firstLineContainsHeaders"` | hodnotu true (výchozí) <br/>false (nepravda) | Pro [CSV objekty BLOB (preview)](search-howto-index-csv-blobs.md), označuje, že první řádek (Neprázdné) každý objekt blob obsahuje záhlaví.|
| `"documentRoot"`  | Řetězec<br/>Uživatelem definované | Pro [pole JSON (preview)](search-howto-index-json-blobs.md#nested-json-arrays), zadaný dokument strukturovaných nebo částečně strukturovaných, můžete zadat cestu k poli pomocí této vlastnosti. |
| `"dataToExtract"` | Řetězec<br/>`"storageMetadata"`<br/>`"allMetadata"`<br/>`"contentAndMetadata"` (výchozí) | Pro [objektů BLOB Azure](search-howto-indexing-azure-blob-storage.md):<br/>Nastavte na `"storageMetadata"` indexu jenom na [vlastnosti standardní objektů blob a zadat uživatele metadata](../storage/blobs/storage-properties-metadata.md). <br/>Nastavte na `"allMetadata"` extrahovat metadata poskytované subsystému úložiště objektů blob v Azure a [konkrétních metadat typu obsahu](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (například metadata jedinečné pro jenom soubory PNG) jsou indexované. <br/>Nastavte na `"contentAndMetadata"` extrahovat všechna metadata a textový obsah z jednotlivých objektů blob. <br/><br/>Pro [Analýza obrázků v kognitivní vyhledávání (preview)](cognitive-search-concept-image-scenarios.md), když `"imageAction"` je nastaven na `"generateNormalizedImages"`, `"dataToExtract"` nastavení určuje indexeru, která data extrahovat z obsahu bitové kopie. Platí pro vložený obrázek obsah. PDF nebo jiná aplikace nebo soubory obrázků, jako je například JPG a PNG v Azure BLOB.  |
| `"imageAction"` |  Řetězec<br/>`"none"`<br/>`"generateNormalizedImages"` | Pro [objektů BLOB Azure](search-howto-indexing-azure-blob-storage.md), nastavte pro`"none"` ignorovat vložené obrázky nebo soubory obrázků v datové sadě. Toto je výchozí hodnota. <br/><br/>Pro [Analýza obrázků v kognitivní vyhledávání](cognitive-search-concept-image-scenarios.md), nastavte pro`"generateNormalizedImages"` rozbalte text z bitových kopií (například word "stop" od provozu zastavení přihlašovací) a vložte jej jako součást pole obsahu. Během analýzy bitové kopie indexeru vytvoří pole bitových kopií normalizovaných jako součást hádání toho dokumentu a vloží informace generované do pole obsahu. Tato akce vyžaduje, aby `"dataToExtract"` je nastaven na `"contentAndMetadata"`. Bitovou kopii normalizovaný odkazuje na další zpracování, což vede k uniform image výstup, velikosti a otočený o zvýšení úrovně konzistentní vykreslování obsahují Image ve výsledcích hledání visual (například stejnou velikost fotografií v grafu řídit, jak je vidět [JFK ukázku](https://github.com/Microsoft/AzureSearch_JFK_Files)). Tato informace se generují každé bitové kopie, při použití |


#### <a name="other-configuration-parameters"></a>Další parametry konfigurace

Následující parametry jsou specifické pro Azure SQL Database.

| Parametr | Typ a povolené hodnoty   | Využití  |
|-----------|---------------------------|--------|
| `"queryTimeout"` | Řetězec<br/>hh: mm"<br/>"00: 05:00"| Pro [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), nastavte tento parametr zvýšit časový limit nad výchozí 5 minut.|

<a name="field-mappings"></a>

### <a name="fieldmappings"></a>"fieldMappings"

Indexer definice obsahovat pole přidružení pro mapování pole zdroje na cílové pole v indexu Azure Search. Existují dva typy přidružení v závislosti na tom, jestli přenos obsahu následuje přímý nebo provádět rozšířené cesta:

+ **fieldMappings** jsou volitelné, použije, když se názvy polí zdroj cíl se neshodují, nebo když chcete určit funkce.
+ **outputFieldMappings** jsou povinné, pokud vytváříte [kanálu obohacení](cognitive-search-concept-intro.md). Pole výstup v kanálu obohacení je konstrukce definované během procesu obohacení. Pole výstup může být například složené struktura vytvořené během obohacení ze dvou samostatných polích ve zdrojovém dokumentu. 

V následujícím příkladu, zvažte zdrojová tabulka s polem `_id`. Služba Azure Search neumožňuje pole název začíná podtržítkem, takže pole musí být přejmenován. To lze provést pomocí `fieldMappings` vlastnost indexeru následujícím způsobem:

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

Můžete určit více mapování polí:

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

Zdrojové a cílové názvy polí jsou velká a malá písmena.

Další informace o scénářích, kde jsou užitečné mapování polí najdete v tématu [mapování polí Indexer vyhledávání](https://docs.microsoft.com/azure/search/search-indexer-field-mappings).

<a name="output-fieldmappings"></a>

### <a name="outputfieldmappings"></a>"outputFieldMappings"

V [kognitivní vyhledávání](cognitive-search-concept-intro.md) scénáře, ve kterých je vázána skillset indexer, je nutné přidat `outputFieldMappings` přidružit žádný výstup na obohacení krok, který poskytuje prohledávatelné pole v indexu.

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

### <a name="field-mapping-functions"></a>Funkce mapování polí

Mapování polí lze použít také k transformaci hodnoty polí zdroje pomocí *pole mapování funkce*. Například libovolný řetězec hodnota může být kódováním base64, může sloužit k vyplnění pole klíče dokumentu.

Další informace o kdy a jak používat funkce mapování polí, najdete v části [funkce mapování polí](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

## <a name="request-examples"></a>Příklady požadavku  
 V prvním příkladu vytvoří indexer, který kopíruje data z tabulky odkazuje `ordersds` zdroje dat pro `orders` index podle plánu, který začíná na 1 ledna 2015 UTC a spouští každou hodinu. Každé vyvolání indexeru bude úspěšné, pokud se nepodaří indexovat v každé dávce víc než 5 položek a maximálně 10 položek nepodaří indexovat celkem.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2018-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

Druhý příklad uvádí operace kognitivní vyhledávání, indikován odkaz na skillset a [outputFieldMappings](#output-fieldmappings). [Skillsets](ref-create-skillset.md) jsou základní prostředky, definice samostatně. V tomto příkladu je zkratkou definice indexer v [kurz kognitivní search](cognitive-search-tutorial-blob.md).

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
        "sourceFieldName" : "content",
        "targetFieldName" : "content"
    }
   ],
  "outputFieldMappings" : 
  [
    {
        "sourceFieldName" : "/document/organizations", 
        "targetFieldName" : "organizations"
    },
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "configuration": 
    {
    "dataToExtract": "contentAndMetadata",
    "imageAction": "generateNormalizedImages"
    }
  }
}
```

## <a name="response"></a>Odpověď  
 Pro úspěšné žádosti 201 – vytvořeno.  

## <a name="see-also"></a>Další informace najdete v tématech

+ [Přehled indexeru](search-indexer-overview.md)
+ [Přehled kognitivní vyhledávání](cognitive-search-concept-intro.md)
+ [Rychlý úvod: Zkuste kognitivní vyhledávání](cognitive-search-quickstart-blob.md)
+ [Mapování polí](cognitive-search-output-field-mapping.md)
