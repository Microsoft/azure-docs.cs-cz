---
title: Rozhraní API Azure Cosmos DB pro mongoDB (verze 3.2) podporované funkce a syntaxe
description: Další informace o azure cosmos DB rozhraní API pro MongoDB (verze 3.2) podporované funkce a syntaxe.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 12e5dba0339b6092564e5d35c1a6250b0c47f50f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "72755006"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-32-version-supported-features-and-syntax"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB (verze 3.2): Podporované funkce a syntax

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete komunikovat s rozhraním API Azure Cosmos DB pro MongoDB pomocí libovolného z open source [ovladačů](https://docs.mongodb.org/ecosystem/drivers)klienta MongoDB . Rozhraní API Azure Cosmos DB pro MongoDB umožňuje použití existujících klientských ovladačů dodržováním [drátového protokolu](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)MongoDB .

Pomocí rozhraní API Azure Cosmos DB pro MongoDB můžete využívat výhody MongoDB, na které jste zvyklí, se všemi podnikovými funkcemi, které Cosmos DB poskytuje: [globální distribuce](distribute-data-globally.md), [automatické sharding](partition-data.md), záruky dostupnosti a latence, automatické indexování každého pole, šifrování v klidovém stavu, zálohování a mnoho dalšího.

> [!NOTE]
> Tento článek je pro rozhraní API Azure Cosmos DB pro MongoDB 3.2. Informace o verzi MongoDB 3.6 naleznete v [tématu Podporované funkce a syntaxe MongoDB 3.6](mongodb-feature-support-36.md).

## <a name="protocol-support"></a>Podpora protokolu

Všechny nové účty rozhraní API Azure Cosmos DB pro MongoDB jsou kompatibilní se serverem MongoDB verze **3.6**. Tento článek se věnuje MongoDB verze 3.2. Podporované operátory a případná omezení nebo výjimky jsou uvedené níže. Jakýkoli ovladač klienta, který podporuje tyto protokoly, by se měl dokázat připojit k rozhraní API služby Azure Cosmos DB pro MongoDB.

## <a name="query-language-support"></a>Podpora jazyka dotazu

Rozhraní API Azure Cosmos DB pro MongoDB poskytuje komplexní podporu pro konstrukce dotazovacích jazyků MongoDB. Níže najdete podrobný seznam aktuálně podporovaných operací, operátorů, fází, příkazů a možností.

## <a name="database-commands"></a>Databázové příkazy

Rozhraní API Azure Cosmos DB pro MongoDB podporuje následující databázové příkazy:

### <a name="query-and-write-operation-commands"></a>Příkazy pro dotazovací a zápisové operace

- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Příkazy pro ověření

- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Příkazy pro správu

- dropDatabase
- listCollections
- drop
- vytvoření
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Příkazy pro diagnostiku

- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Agregační zřetězení</a>

Cosmos DB podporuje kanál agregace pro MongoDB 3.2 ve verzi Public Preview. Pokyny k registraci veřejné verze Preview najdete na [blogu Azure](https://aka.ms/mongodb-aggregation).

### <a name="aggregation-commands"></a>Příkazy pro agregaci

- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Fáze agregace

- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Výrazy pro agregaci

#### <a name="boolean-expressions"></a>Logické výrazy

- $and
- $or
- $not

#### <a name="set-expressions"></a>Nastavovací výrazy

- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Porovnávací výrazy

- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Aritmetické výrazy

- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Řetězcové výrazy

- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Maticové výrazy

- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Datumové výrazy

- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Podmíněné výrazy

- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Agregační zásobníky

- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operátory

Jsou podporované následující operátory, doplněné o odpovídající příklady použití. V níže uvedených dotazech se používá tento ukázkový dokument:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Operátor | Příklad |
--- | --- |
$eq | `{ "Volcano Name": { $eq: "Rainier" } }` |  | -
$gt | `{ "Elevation": { $gt: 4000 } }` |  | -
$gte | `{ "Elevation": { $gte: 4392 } }` |  | -
$lt | `{ "Elevation": { $lt: 5000 } }` |  | -
$lte | `{ "Elevation": { $lte: 5000 } }` | | -
$ne | `{ "Elevation": { $ne: 1 } }` |  | -
$in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |  | -
$nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` | | -
$or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$not | `{ "Elevation": { $not: { $gt: 5000 } } }`|  | -
$nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |  | -
$exists | `{ "Status": { $exists: true } }`|  | -
$type | `{ "Status": { $type: "string" } }`|  | -
$mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` |  | -
$regex | `{ "Volcano Name": { $regex: "^Rain"} }`|  | -

### <a name="notes"></a>Poznámky

V dotazech $regex umožňují zleva ukotvené výrazy hledání indexu. Použití modifikátorů „i“ (rozlišování malých a velkých písmen) a „m“ (více řádků) ale způsobí, že se kolekce prohledává ve všech výrazech.
Pokud potřebujete začlenit „$“ nebo „|“, je nejlepší vytvořit dva (nebo více) dotazů využívajících regulární výrazy.
Například následující původní dotaz ```find({x:{$regex: /^abc$/})``` by se měl upravit takto: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
V první části se použije index k omezení hledání na dokumenty, které začínají na ^abc a ve druhé části se porovnají přesné položky.
Operátor „|“ se chová jako funkce „nebo“ – dotaz ```find({x:{$regex: /^abc|^def/})``` porovnává dokumenty, ve kterých pole „x“ nabývá hodnot, které začínají na „abc“ nebo „def“. Pokud chcete využít index, je doporučeno dotaz rozdělit na dva různé dotazy spojené operátorem $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Aktualizační operátory

#### <a name="field-update-operators"></a>Operátory pro aktualizaci polí

- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Operátory pro aktualizaci matic

- $addToSet
- $pop
- $pullAll
- $pull (Poznámka: $pull s podmínkou se nepodporuje.)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Operátor pro bitovou aktualizaci

- $bit

### <a name="geospatial-operators"></a>Geoprostorové operátory

Operátor | Příklad | |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ano |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ano |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Ano |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ano |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Ano |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ano |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Ano |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano |

## <a name="sort-operations"></a>Operace řazení

Při použití `findOneAndUpdate` operace jsou podporovány operace řazení na jednom poli, ale operace řazení na více polích nejsou podporovány.

## <a name="additional-operators"></a>Další operátory

Operátor | Příklad | Poznámky
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` |
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |
$size | ```{ "Location.coordinates": { $size: 2 } }``` |
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` |
$text |  | Není podporováno. Použijte místo toho $regex.

## <a name="unsupported-operators"></a>Nepodporované operátory

Azure Cosmos DB nepodporuje operátory ```$where``` a ```$eval```.

### <a name="methods"></a>Metody

Podporují se následující metody:

#### <a name="cursor-methods"></a>Metody kurzoru

Metoda | Příklad | Poznámky
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Dokumenty bez klíče řazení se nevracejí

## <a name="unique-indexes"></a>Jedinečné indexy

Cosmos DB indexuje každé pole v dokumentech, které jsou ve výchozím nastavení zapsány do databáze. Jedinečné indexy zajišťují, že určité pole nemá duplicitní hodnoty ve všech dokumentech v kolekci, `_id` podobně jako je zachována jedinečnost ve výchozím klíči. Vlastní indexy v Cosmos DB můžete vytvořit pomocí příkazu createIndex, včetně omezení "unique".

Jedinečné indexy jsou k dispozici pro všechny účty Cosmos pomocí rozhraní API Azure Cosmos DB pro MongoDB.

## <a name="time-to-live-ttl"></a>Hodnota TTL (Time-To-Live)

Cosmos DB podporuje time-to-live (TTL) na základě časového razítka dokumentu. TTL lze povolit pro kolekce tím, že přejdete na [portál Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Správa uživatelů a rolí

Cosmos DB ještě nepodporuje uživatele a role. Cosmos DB však podporuje řízení přístupu na základě rolí (RBAC) a hesla/klíče jen pro čtení a jen pro čtení, které lze získat prostřednictvím [portálu Azure](https://portal.azure.com) (stránka spojovacího řetězce).

## <a name="replication"></a>Replikace

Cosmos DB podporuje automatickou nativní replikaci v nejnižších vrstvách. Tato logika umožňuje zároveň dosáhnout nízké latence a globální replikace. Cosmos DB nepodporuje příkazy ruční replikace.

## <a name="write-concern"></a>Write Concern

Některé aplikace spoléhají na [problém zápisu,](https://docs.mongodb.com/manual/reference/write-concern/) který určuje počet odpovědí požadovaných během operace zápisu. Kvůli tomu, jak Cosmos DB zpracovává replikaci na pozadí, jsou všechny zápisy ve výchozím nastavení automaticky kvora. Všechny problémy s zápisem určené klientským kódem jsou ignorovány. Další informace najdete v tématu popisujícím [využití úrovní konzistence pro maximalizaci dostupnosti a výkonu](consistency-levels.md).

## <a name="sharding"></a>Sharding

Azure Cosmos DB podporuje automatický sharding na straně serveru. Spravuje vytváření úlomků, umístění a vyrovnávání automaticky. Azure Cosmos DB nepodporuje ruční šněrování příkazy, což znamená, že není nutné vyvolat příkazy, jako je například shardCollection, addShard, balancerStart, moveChunk atd. Stačí zadat klíč střepu při vytváření kontejnerů nebo dotazování na data.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Zjistěte, jak [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MongoDB pomocí rozhraní API Azure Cosmos DB pro MongoDB.

<sup>Poznámka: Tento článek popisuje funkci Azure Cosmos DB, která poskytuje kompatibilitu drátového protokolu s databázemi MongoDB. Společnost Microsoft nespouštěla databáze MongoDB k poskytování této služby. Azure Cosmos DB není přidružená k MongoDB, Inc.</sup>
