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
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Vytvoření Indexer (verze rozhraní api REST služby vyhledávání systému Azure = 2017-11-11-Preview)

Tento referenční dokumentace rozhraní API je specifické pro verzi preview verze v dokumentaci týkající se vylepšení kognitivní vyhledávání indexování.

Stejně jako u [všeobecně dostupná](https://docs.microsoft.com/rest/api/searchservice/create-indexer) verze, můžete vytvořit nový indexer v rámci služby Azure Search pomocí požadavku HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
**Klíč api-key** musí být klíče správce (na rozdíl od klíč dotazů). Informace naleznete v sekci ověřování v [zabezpečení ve službě Azure Search](search-security-overview.md) Další informace o klíčích. [Vytvoření služby Azure Search na portálu](search-create-service-portal.md) vysvětluje, jak získat adresu URL služby a klíče vlastnosti používané v požadavku.

Alternativně můžete použít PUT a zadejte název zdroje dat v identifikátoru URI. Pokud zdroj dat neexistuje, bude vytvořen.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
**Verze rozhraní api** je vyžadován. Aktuální verze je `2016-09-01`. V tématu [verze rozhraní API ve službě Azure Search](search-api-versions.md) podrobnosti.

Pro příslušnou platformu data pokyny k vytváření indexery, začínat [indexery přehled](search-indexer-overview.md), což zahrnuje úplný seznam [souvisejících článcích](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  Maximální počet indexery povoleno se liší podle cenové úrovně. Bezplatné služby umožňuje až 3 indexery. Standardní služby umožňuje 50 indexery. V tématu [omezení služby](search-limits-quotas-capacity.md) podrobnosti.    

## <a name="request"></a>Žádost  
 Text žádosti obsahuje definici indexer, který určuje zdroje dat a cílový index pro indexování, jakož i volitelné indexování plán a parametry.  

 Syntaxe pro vytváření struktury datová část požadavku je následující. Ukázková žádost je k dispozici dále v tomto tématu.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>Plán indexeru  
Indexer Volitelně můžete zadat plán. Pokud se nachází plánu, spuštění indexeru pravidelně podle plánu. Plánovač je integrovaný; nelze použít externí plánovače. **Plán** má následující atributy: 

-   **interval**: vyžaduje. Doba trvání hodnotu, která určuje interval nebo období pro indexer se spustí. Nejkratší povolený interval je 5 minut; nejdelší je jeden den. Musí být naformátovaná jako hodnota "hodnoty doby podle" XSD (omezená podmnožina [ISO 8601 trvání](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) hodnotu). Je tento vzor: `"P[nD][T[nH][nM]]".` příklady: `PT15M` pro každých 15 minut, `PT2H` pro každé 2 hodiny.  

-   **startTime**: volitelné. Pokud by se měl indexer spustit systémem datetime UTC.  

### <a name="indexer-parameters"></a>Indexer parametry  
 Indexer Volitelně můžete zadat několik parametrů, které ovlivňují své chování. Všechny následující parametry jsou volitelné.  

-   **maxFailedItems**: počet neúspěšně indexovaných kterého se spuštění indexeru považuje za selhání položek. Výchozí hodnota je 0. Vrátí informace o neúspěšné položky [získání stavu Indexer &#40;rozhraní REST API služby Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) operaci.  

-   **maxFailedItemsPerBatch**: počet položek, které může být indexované v každé dávce, kterého se spuštění indexeru považuje za selhání. Výchozí hodnota je 0.  

-   **batchSize:** určuje počet položek, které čtou ze zdroje dat a indexované jako jeden batch za účelem zlepšení výkonu. Výchozí hodnota závisí na typu zdroje dat: je 1 000 pro Azure SQL a Azure Cosmos DB a 10 pro Azure Blob Storage.

### <a name="field-mapping-parameters"></a>Mapování pole parametrů

Indexer definice obsahovat pole přidružení pro mapování pole zdroje na cílové pole v indexu Azure Search. Existují dva typy přidružení v závislosti na tom, jestli přenos obsahu následuje přímý nebo provádět rozšířené cesta:

+ **fieldMappings** jsou volitelné, použije, když se názvy polí zdroj cíl se neshodují, nebo když chcete určit funkce.
+ **outputFieldMappings** jsou povinné, pokud vytváříte [kanálu obohacení](cognitive-search-concept-intro.md). Pole výstup v kanálu obohacení je konstrukce definované během procesu obohacení. Pole výstup může být například složené struktura vytvořené během obohacení ze dvou samostatných polích ve zdrojovém dokumentu. 

#### <a name="fieldmappings"></a>fieldMappings

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

#### <a name="outputfieldmappings"></a>outputFieldMappings

Ve scénářích kognitivní vyhledávání, ve kterých je vázána skillset indexer, je nutné přidat `outputFieldMappings` přidružit žádný výstup na obohacení krok, který poskytuje prohledávatelné pole v indexu.

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

#### <a name="field-mapping-functions"></a>Funkce mapování polí

Mapování polí lze použít také k transformaci hodnoty polí zdroje pomocí *pole mapování funkce*. Například libovolný řetězec hodnota může být kódováním base64, může sloužit k vyplnění pole klíče dokumentu.

Další informace o kdy a jak používat funkce mapování polí, najdete v části [funkce mapování polí](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

### <a name="request-body-examples"></a>Příklady text žádosti  
 Následující příklad vytvoří indexer, který kopíruje data z tabulky odkazuje `ordersds` zdroje dat pro `orders` index podle plánu, který začíná na 1 ledna 2015 UTC a spouští každou hodinu. Každé vyvolání indexeru bude úspěšné, pokud se nepodaří indexovat v každé dávce víc než 5 položek a maximálně 10 položek nepodaří indexovat celkem.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>Odpověď  
 Pro úspěšné žádosti 201 – vytvořeno.  

## <a name="see-also"></a>Další informace najdete v tématech

+ [Přehled kognitivní vyhledávání](cognitive-search-concept-intro.md)
+ [Rychlý úvod: Zkuste kognitivní vyhledávání](cognitive-search-quickstart-blob.md)
+ [Mapování polí](cognitive-search-output-field-mapping.md)
