---
title: Rozhraní API Azure Cosmos DB pro mongoDB (verze 3.6) podporované funkce a syntaxe
description: Další informace o azure cosmos DB rozhraní API pro MongoDB (verze 3.6) podporované funkce a syntaxe.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 01/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 5df21b2c1926803a65eca911c66b059f36ee18aa
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393617"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB (verze 3.6): Podporované funkce a syntax

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete komunikovat s rozhraním API Azure Cosmos DB pro MongoDB pomocí libovolného z open source [ovladačů](https://docs.mongodb.org/ecosystem/drivers)klienta MongoDB . Rozhraní API Azure Cosmos DB pro MongoDB umožňuje použití existujících klientských ovladačů dodržováním [drátového protokolu](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)MongoDB .

Pomocí rozhraní API Azure Cosmos DB pro MongoDB můžete využívat výhody MongoDB, na které jste zvyklí, se všemi podnikovými funkcemi, které Cosmos DB poskytuje: [globální distribuce](distribute-data-globally.md), [automatické rozdělení ,](partition-data.md)záruky dostupnosti a latence, šifrování v klidovém stavu, zálohování a mnoho dalšího.

## <a name="protocol-support"></a>Podpora protokolu

Rozhraní API Azure Cosmos DB pro MongoDB je ve výchozím nastavení kompatibilní se serverem MongoDB verze **3.6** pro nové účty. Podporované operátory a případná omezení nebo výjimky jsou uvedené níže. Jakýkoli ovladač klienta, který podporuje tyto protokoly, by se měl dokázat připojit k rozhraní API služby Azure Cosmos DB pro MongoDB. Všimněte si, že při použití rozhraní API Azure Cosmos DB pro účty MongoDB, `*.mongo.cosmos.azure.com` 3.6 verze účtů mají koncový bod ve `*.documents.azure.com`formátu vzhledem k tomu, 3.2 verze účtů mají koncový bod ve formátu .

## <a name="query-language-support"></a>Podpora jazyka dotazu

Rozhraní API Azure Cosmos DB pro MongoDB poskytuje komplexní podporu pro konstrukce dotazovacích jazyků MongoDB. Níže naleznete podrobný seznam aktuálně podporovaných operací, operátorů, fází, příkazů a možností.

## <a name="database-commands"></a>Databázové příkazy

Rozhraní API Azure Cosmos DB pro MongoDB podporuje následující databázové příkazy:

### <a name="query-and-write-operation-commands"></a>Příkazy pro dotazovací a zápisové operace

|Příkaz  |Podporuje se |
|---------|---------|
|delete | Ano |
|find | Ano     |
|findAndModify | Ano  |
|getLastError|   Ano |
|getMore  |  Ano  |
|getPrevError | Ne  |
|insert  |   Ano  |
|parallelCollectionScan  | Ano   |
|chyba reset |    Ne  |
|update  |   Ano  |
|[Změna datových proudů](mongodb-change-streams.md)  |  Ano  |
|Mřížky |   Ano  |

### <a name="authentication-commands"></a>Příkazy pro ověření

|Příkaz  |Podporuje se |
|---------|---------|
|authenticate    |   Ano      |
|logout    |      Ano   |
|getnonce   |    Ano     |


### <a name="administration-commands"></a>Příkazy pro správu

|Příkaz  |Podporuje se |
|---------|---------|
|Limitované sbírky   |   Ne      |
|cloneCollectionAsCapped     |   Ne      |
|collMod     |   Ne      |
|collMod: expireAfterSeconds   |   Ne      |
|převéstToCapped   |  Ne       |
|copydb     |  Ne       |
|vytvoření   |    Ano     |
|createIndexes     |  Ano       |
|currentOp     |  Ano       |
|drop     |   Ano      |
|dropDatabase     |  Ano       |
|dropIndexes     |   Ano      |
|filemd5    |   Ano      |
|killCursors    |  Ano       |
|killOp     |   Ne      |
|listCollections     |  Ano       |
|listDatabases     |  Ano       |
|listIndexes     |  Ano       |
|reIndex     |    Ano     |
|přejmenovatvzpomínky     |    Ne     |
|connectionStatus    |     Ne    |

### <a name="diagnostics-commands"></a>Příkazy pro diagnostiku

|Příkaz  |Podporuje se |
|---------|---------|
|buildInfo         |   Ano      |
|collStats    |  Ano       |
|connPoolStats     |  Ne       |
|connectionStatus     |  Ne       |
|dataVelikost     |   Ne      |
|dbHash    |    Ne     |
|dbStats     |   Ano      |
|Vysvětlit     |   Ano      |
|vysvětlit: exekuceStats     |   Ano      |
|funkce     |    Ne     |
|hostInfo     |   Ne      |
|listDatabases         |   Ano      |
|listCommands     |  Ne       |
|Profiler     |  Ne       |
|serverStatus     |  Ne       |
|top     |    Ne     |
|whatsmyuri     |   Ano      |

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Agregační zřetězení</a>

### <a name="aggregation-commands"></a>Příkazy pro agregaci

|Příkaz  |Podporuje se |
|---------|---------|
|aggregate |   Ano  |
|count     |   Ano  |
|distinct  | Ano |
|mapReduce | Ne |

### <a name="aggregation-stages"></a>Fáze agregace

|Příkaz  |Podporuje se |
|---------|---------|
|$collStats    |Ne|
|$project    |Ano|
|$match    |Ano|
|$redact|    Ano|
|$limit    |Ano|
|$skip    |Ano|
|$unwind|    Ano|
|$group    |    Ano|
|$sample|        Ano|
|$sort    |Ano|
|$geoNear|    Ne|
|$lookup    |    Ano|
|$out        |Ano|
|$indexStats|        Ne|
|$facet    |Ne|
|$bucket|    Ne|
|$bucketAuto|    Ne|
|$sortByCount|    Ano|
|$addFields    |Ano|
|$replaceRoot|    Ano|
|$count    |Ano|
|$currentOp|    Ne|
|$listLocalSessions    |Ne|
|$listSessions    |Ne|
|$graphLookup    |Ne|

### <a name="boolean-expressions"></a>Logické výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$and| Ano|
|$or|Ano|
|$not|Ano|

### <a name="set-expressions"></a>Nastavovací výrazy

|Příkaz  |Podporuje se |
|---------|---------|
| $setEquals | Ano|
|$setIntersection|Ano|
| $setUnion|Ano|
| $setDifference|Ano|
| $setIsSubset|Ano|
| $anyElementTrue|Ano|
| $allElementsTrue|Ano|

### <a name="comparison-expressions"></a>Porovnávací výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$cmp     |  Ano       |
|$eq|    Ano| 
|$gt |    Ano| 
|$gte|    Ano| 
|$lt    |Ano|
|$lte|    Ano| 
|$ne    |    Ano| 
|$in    |    Ano| 
|$nin    |    Ano| 

### <a name="arithmetic-expressions"></a>Aritmetické výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$abs |  Ano       |
| $add |  Ano       |
| $ceil |  Ano       |
| $divide |  Ano       |
| $exp |  Ano       |
| $floor |  Ano       |
| $ln |  Ano       |
| $log |  Ano       |
| $log10 |  Ano       |
| $mod |  Ano       |
| $multiply |  Ano       |
| $pow |  Ano       |
| $sqrt |  Ano       |
| $subtract |  Ano       |
| $trunc |  Ano       |

### <a name="string-expressions"></a>Řetězcové výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$concat |  Ano       |
| $indexOfBytes|  Ano       |
| $indexOfCP|  Ano       |
| $split|  Ano       |
| $strLenBytes|  Ano       |
| $strLenCP|  Ano       |
| $strcasecmp|  Ano       |
| $substr|  Ano       |
| $substrBytes|  Ano       |
| $substrCP|  Ano       |
| $toLower|  Ano       |
| $toUpper|  Ano       |

### <a name="text-search-operator"></a>Operátor vyhledávání textu

|Příkaz  |Podporuje se |
|---------|---------|
| $meta | Ne|

### <a name="array-expressions"></a>Maticové výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$arrayElemAt    |    Ano|
|$arrayToObject|    Ano|
|$concatArrays    |    Ano|
|$filter    |    Ano|
|$indexOfArray    |Ano|
|$isArray    |    Ano|
|$objectToArray    |Ano|
|$range    |Ano|
|$reverseArray    |    Ano|
|$reduce|    Ano|
|$size    |    Ano|
|$slice    |    Ano|
|$zip    |    Ano|
|$in    |    Ano|

### <a name="variable-operators"></a>Operátory proměnných

|Příkaz  |Podporuje se |
|---------|---------|
|$map    |Ne|
|$let    |Ano|

### <a name="system-variables"></a>Systémové proměnné

|Příkaz  |Podporuje se |
|---------|---------|
|$$CURRENT|    Ano|
|$$DESCEND|        Ano|
|$$KEEP        |Ano|
|$$PRUNE    |    Ano|
|$$REMOVE    |Ano|
|$$ROOT        |Ano|

### <a name="literal-operator"></a>Doslovný operátor

|Příkaz  |Podporuje se |
|---------|---------|
|$literal    |Ano|

### <a name="date-expressions"></a>Datumové výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$dayOfYear    |Ano    |
|$dayOfMonth|    Ano    |
|$dayOfWeek    |Ano    |
|$year    |Ano    |
|$month    |Ano|    
|$week    |Ano    |
|$hour    |Ano    |
|$minute|    Ano|    
|$second    |Ano    |
|$millisecond|    Ano|    
|$dateToString    |Ano    |
|$isoDayOfWeek    |Ano    |
|$isoWeek    |Ano    |
|$dateFromParts|    Ne|    
|$dateToParts    |Ne    |
|$dateFromString|    Ne|
|$isoWeekYear    |Ano    |

### <a name="conditional-expressions"></a>Podmíněné výrazy

|Příkaz  |Podporuje se |
|---------|---------|
| $cond| Ano|
| $ifNull| Ano|
| $switch |Ano|

### <a name="data-type-operator"></a>Operátor datového typu

|Příkaz  |Podporuje se |
|---------|---------|
| $type| Ano|

### <a name="accumulator-expressions"></a>Akumulační výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$sum    |Ano    |
|$avg    |Ano    |
|$first|    Ano|
|$last    |Ano    |
|$max    |Ano    |
|$min    |Ano    |
|$push|    Ano|
|$addToSet|    Ano|
|$stdDevPop|    Ne    |
|$stdDevSamp|    Ne|

### <a name="merge-operator"></a>Operátor sloučení

|Příkaz  |Podporuje se |
|---------|---------|
| $mergeObjects | Ano|

## <a name="data-types"></a>Typy dat

|Příkaz  |Podporuje se |
|---------|---------|
|Double    |Ano    |
|Řetězec    |Ano    |
|Objekt    |Ano    |
|Pole    |Ano    |
|Binární data    |Ano|    
|ObjectId    |Ano    |
|Logická hodnota    |Ano    |
|Datum    |Ano    |
|Null    |Ano    |
|32bitové celé číslo (int)    |Ano    |
|Časové razítko    |Ano    |
|64bitové celé číslo (dlouhé)    |Ano    |
|MinKey    |Ano    |
|Maximální klíč    |Ano    |
|Desetinné číslo128    |Ano|    
|Regulární výraz    |Ano|
|JavaScript    |Ano|
|JavaScript (s rozsahem)|    Ano    |
|Nedefinované    |Ano    |

## <a name="indexes-and-index-properties"></a>Indexy a vlastnosti indexu

### <a name="indexes"></a>Indexy

|Příkaz  |Podporuje se |
|---------|---------|
|Index jednoho pole    |Ano    |
|Složený index    |Ano    |
|Index více klíčů    |Ano    |
|Textový index    |Ne|
|2dsféra    |Ano    |
|2d index    |Ne    |
|Hashed Index    | Ano|

### <a name="index-properties"></a>Vlastnosti indexu

|Příkaz  |Podporuje se |
|---------|---------|
|Hodnota TTL|    Ano    |
|Jedinečná    |Ano|
|Částečné|    Ne|
|Nerezivěje se malá a velká    |Ne|
|Řídké    |Ne |
|Pozadí|    Ano |

## <a name="operators"></a>Operátory

### <a name="logical-operators"></a>Logické operátory

|Příkaz  |Podporuje se |
|---------|---------|
|$or    |    Ano|
|$and    |    Ano|
|$not    |    Ano|
|$nor    |    Ano| 

### <a name="element-operators"></a>Operátory prvků

|Příkaz  |Podporuje se |
|---------|---------|
|$exists|    Ano|
|$type    |    Ano|

### <a name="evaluation-query-operators"></a>Operátory zkušebních dotazů

|Příkaz  |Podporuje se |
|---------|---------|
|$expr    |    Ne|
|$jsonSchema    |    Ne|
|$mod    |    Ano|
|$regex |    Ano|
|$text    | Ne (Není podporováno. Místo toho použijte $regex.)| 
|$where    |Ne| 

V $regex dotazů umožňují výrazy ukotvené vlevo prohledávat indexy. Použití modifikátorů „i“ (rozlišování malých a velkých písmen) a „m“ (více řádků) ale způsobí, že se kolekce prohledává ve všech výrazech.

Pokud potřebujete začlenit „$“ nebo „|“, je nejlepší vytvořit dva (nebo více) dotazů využívajících regulární výrazy. Například s ohledem na ```find({x:{$regex: /^abc$/})```následující původní dotaz: , musí být změněn takto:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

V první části se použije index k omezení hledání na dokumenty, které začínají na ^abc a ve druhé části se porovnají přesné položky. Operátor „|“ se chová jako funkce „nebo“ – dotaz ```find({x:{$regex: /^abc|^def/})``` porovnává dokumenty, ve kterých pole „x“ nabývá hodnot, které začínají na „abc“ nebo „def“. Pokud chcete využít index, je doporučeno dotaz rozdělit na dva různé dotazy spojené operátorem $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Operátory pole

|Příkaz  |Podporuje se | 
|---------|---------|
| $all | Ano| 
| $elemMatch | Ano| 
| $size | Ano | 

### <a name="comment-operator"></a>Operátor komentáře

|Příkaz  |Podporuje se | 
|---------|---------|
$comment |Ano| 

### <a name="projection-operators"></a>Projekční operátory

|Příkaz  |Podporuje se |
|---------|---------|
|$elemMatch    |Ano|
|$meta|    Ne|
|$slice    | Ano|

### <a name="update-operators"></a>Aktualizační operátory

#### <a name="field-update-operators"></a>Operátory pro aktualizaci polí

|Příkaz  |Podporuje se |
|---------|---------|
|$inc    |    Ano|
|$mul    |    Ano|
|$rename    |    Ano|
|$setOnInsert|    Ano|
|$set    |Ano|
|$unset| Ano|
|$min    |Ano|
|$max    |Ano|
|$currentDate    | Ano|

#### <a name="array-update-operators"></a>Operátory pro aktualizaci matic

|Příkaz  |Podporuje se |
|---------|---------|
|$    |Ano|
|$[]|    Ano|
|$[<identifier>]|    Ano|
|$addToSet    |Ano|
|$pop    |Ano|
|$pullAll|    Ano|
|$pull    |Ano|
|$push    |Ano|
|$pushAll| Ano|


#### <a name="update-modifiers"></a>Aktualizovat modifikátory

|Příkaz  |Podporuje se |
|---------|---------|
|$each    |    Ano|
|$slice    |Ano|
|$sort    |Ano|
|$position    |Ano|

#### <a name="bitwise-update-operator"></a>Operátor pro bitovou aktualizaci

|Příkaz  |Podporuje se |
|---------|---------|
| $bit    |    Ano|    
|$bitsAllSet    |    Ne|
|$bitsAnySet    |    Ne|
|$bitsAllClear    |Ne|
|$bitsAnyClear    |Ne|

### <a name="geospatial-operators"></a>Geoprostorové operátory

Operátor | Podporuje se| 
--- | --- |
$geoWithin | Ano |
$geoIntersects | Ano | 
$near |  Ano |
$nearSphere |  Ano |
$geometry |  Ano |
$minDistance | Ano |
$maxDistance | Ano |
$center | Ano |
$centerSphere | Ano |
$box | Ano |
$polygon |  Ano |

## <a name="cursor-methods"></a>Metody kurzoru

|Příkaz  |Podporuje se |
|---------|---------|
|cursor.batchSize()    |    Ano|
|cursor.close()    |Ano|
|cursor.isClosed()|        Ano|
|cursor.collation()|    Ne|
|cursor.comment()    |Ano|
|cursor.count()    |Ano|
|cursor.explain()|    Ne|
|cursor.forEach()    |Ano|
|cursor.hasNext()    |Ano|
|cursor.hint()    |Ano|
|cursor.isExhausted()|    Ano|
|cursor.itcount()    |Ano|
|cursor.limit()    |Ano|
|cursor.map()    |Ano|
|cursor.maxScan()    |Ano|
|cursor.maxTimeMS()|    Ano|
|cursor.max()    |Ano|
|cursor.min()    |Ano|
|cursor.next()|    Ano|
|cursor.noCursorTimeout()    |Ne|
|cursor.objsLeftInBatch()    |Ano|
|cursor.pretty()|    Ano|
|cursor.readConcern()|    Ano|
|cursor.readPref()        |Ano|
|cursor.returnKey()    |Ne|
|cursor.showRecordId()|    Ne|
|cursor.size()    |Ano|
|cursor.skip()    |Ano|
|cursor.sort()    |    Ano|
|cursor.tailable()|    Ne|
|cursor.toArray()    |Ano|

## <a name="sort-operations"></a>Operace řazení

Při použití `findOneAndUpdate` operace jsou podporovány operace řazení na jednom poli, ale operace řazení na více polích nejsou podporovány.

## <a name="unique-indexes"></a>Jedinečné indexy

Jedinečné indexy zajišťují, že určité pole nemá duplicitní hodnoty ve všech dokumentech v kolekci, podobně jako je zachována jedinečnost ve výchozím _id klíči. Vlastní indexy v Cosmos DB můžete vytvořit pomocí příkazu createIndex, včetně omezení "unique".

## <a name="time-to-live-ttl"></a>Hodnota TTL (Time-To-Live)

Cosmos DB podporuje time-to-live (TTL) na základě časového razítka dokumentu. TTL lze povolit pro kolekce tím, že přejdete na [portál Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Správa uživatelů a rolí

Cosmos DB ještě nepodporuje uživatele a role. Cosmos DB však podporuje řízení přístupu na základě rolí (RBAC) a hesla/klíče jen pro čtení a jen pro čtení, které lze získat prostřednictvím [portálu Azure](https://portal.azure.com) (stránka připojovacího řetězce).

## <a name="replication"></a>Replikace

Cosmos DB podporuje automatickou nativní replikaci v nejnižších vrstvách. Tato logika umožňuje zároveň dosáhnout nízké latence a globální replikace. Cosmos DB nepodporuje příkazy ruční replikace.

## <a name="write-concern"></a>Write Concern

Některé aplikace spoléhají na [problém zápisu,](https://docs.mongodb.com/manual/reference/write-concern/) který určuje počet odpovědí požadovaných během operace zápisu. Kvůli tomu, jak Cosmos DB zpracovává replikaci na pozadí, jsou všechny zápisy ve výchozím nastavení automaticky kvora. Všechny problémy s zápisem určené klientským kódem jsou ignorovány. Další informace najdete v tématu popisujícím [využití úrovní konzistence pro maximalizaci dostupnosti a výkonu](consistency-levels.md).

## <a name="sharding"></a>Sharding

Azure Cosmos DB podporuje automatický sharding na straně serveru. Spravuje vytváření úlomků, umístění a vyrovnávání automaticky. Azure Cosmos DB nepodporuje ruční šněrování příkazy, což znamená, že není nutné vyvolat příkazy, jako je například addShard, balancerStart, moveChunk atd. Stačí zadat klíč střepu při vytváření kontejnerů nebo dotazování na data.

## <a name="sessions"></a>Relace

Azure Cosmos DB ještě nepodporuje příkazy vedlejších relací na straně serveru.

## <a name="next-steps"></a>Další kroky

- Další informace naleznete v [funkcích verze Mongo 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Zjistěte, jak [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Zjistěte, jak [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MongoDB pomocí rozhraní API Azure Cosmos DB pro MongoDB.

<sup>Poznámka: Tento článek popisuje funkci Azure Cosmos DB, která poskytuje kompatibilitu drátového protokolu s databázemi MongoDB. Společnost Microsoft nespouštěla databáze MongoDB k poskytování této služby. Azure Cosmos DB není přidružená k MongoDB, Inc.</sup>
