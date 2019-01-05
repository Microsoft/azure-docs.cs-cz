---
title: Použití rozhraní API služby Azure Cosmos DB pro podpora funkce MongoDB
description: Další informace o podporovaných funkcích, které nabízí rozhraní API služby Azure Cosmos DB pro MongoDB k MongoDB 3.4.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 7bf8258524b0b5e73c1a499fb6d3578098a0821e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039159"
---
# <a name="use-azure-cosmos-dbs-api-for-mongodb-support-for-mongodb-features-and-syntax"></a>Použít pro podporu MongoDB pro funkce a syntaxi MongoDB API služby Azure Cosmos DB

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Může komunikovat s rozhraním API Azure Cosmos DB pro MongoDB pomocí kteréhokoli z klienta MongoDB opensourcových [ovladače](https://docs.mongodb.org/ecosystem/drivers). Azure Cosmos DB API pro MongoDB umožňuje použití existujících ovladačů klienta dodržováním MongoDB [svážete protokol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Pomocí rozhraní Azure Cosmos DB API pro MongoDB můžete využívat výhod MongoDB jste zvyklí, se všemi podnikových funkcí, které poskytuje služba Cosmos DB: [globální distribuce](distribute-data-globally.md), [automatické horizontální dělení](partition-data.md), záruky latence a dostupnost, automatické indexování každé pole, šifrování neaktivních uložených dat, zálohování a spoustu dalších věcí.

## <a name="mongodb-protocol-support"></a>Podpora protokolu MongoDB

Rozhraní API pro Azure Cosmos DB pro MongoDB je kompatibilní s verzí serveru MongoDB **3.2** ve výchozím nastavení. Podporované operátory a případná omezení nebo výjimky jsou uvedené níže. Funkce nebo operátory dotazu přidané v MongoDB verze **3.4** jsou v současné době dostupné jako funkce Preview. Všechny ovladače klienta, která analyzuje tyto protokoly by měl být schopný se připojit k rozhraní API služby Azure Cosmos DB pro MongoDB.

[Kanál agregace MongoDB](#aggregation-pipeline) je v současné době také dostupný jako samostatná funkce Preview.

## <a name="mongodb-query-language-support"></a>Podpora dotazovacího jazyka MongoDB

Rozhraní API služby Azure Cosmos DB pro MongoDB poskytuje komplexní podporu pro konstruktory jazyka dotazů MongoDB. Níže najdete podrobný seznam aktuálně podporovaných operací, operátorů, fází, příkazů a možností.

## <a name="database-commands"></a>Databázové příkazy

Rozhraní API služby Azure Cosmos DB pro MongoDB podporuje následující příkazy databáze:

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

Cosmos DB podporuje agregačního kanálu ve verzi public preview. Pokyny k registraci veřejné verze Preview najdete na [blogu Azure](https://aka.ms/mongodb-aggregation).

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

Operátor | Příklad: |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Poznámky

V dotazech $regex umožňují zleva ukotvené výrazy hledání indexu. Použití modifikátorů „i“ (rozlišování malých a velkých písmen) a „m“ (více řádků) ale způsobí, že se kolekce prohledává ve všech výrazech.
Pokud potřebujete začlenit „$“ nebo „|“, je nejlepší vytvořit dva (nebo více) dotazů využívajících regulární výrazy. Například následující původní dotaz ```find({x:{$regex: /^abc$/})``` by se měl upravit takto: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
V první části se použije index k omezení hledání na dokumenty, které začínají na ^abc a ve druhé části se porovnají přesné položky. Operátor „|“ se chová jako funkce „nebo“ – dotaz ```find({x:{$regex: /^abc|^def/})``` porovnává dokumenty, ve kterých pole „x“ nabývá hodnot, které začínají na „abc“ nebo „def“. Pokud chcete využít index, je doporučeno dotaz rozdělit na dva různé dotazy spojené operátorem $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

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

Operátor | Příklad: 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ano
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ano
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Ano
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ano
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Ano
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ano
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Ano
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano

## <a name="additional-operators"></a>Další operátory

Operátor | Příklad: | Poznámky 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Nepodporuje se. Použijte místo toho $regex.

## <a name="unsupported-operators"></a>Nepodporované operátory

Azure Cosmos DB nepodporuje operátory ```$where``` a ```$eval```.

### <a name="methods"></a>Metody

Podporují se následující metody:

#### <a name="cursor-methods"></a>Metody kurzoru

Metoda | Příklad: | Poznámky 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Dokumenty bez klíče řazení se nevracejí

## <a name="unique-indexes"></a>Jedinečné indexy

Cosmos DB indexuje všechna pole v dokumentech, které jsou ve výchozím nastavení zapsány do databáze. Jedinečné indexy zajišťují, aby určité pole nemělo duplicitní hodnoty v rámci všech dokumentů v kolekci. Je to podobné, jako když má být zachovaná jedinečnost výchozího klíče „_id“. Můžete vytvořit vlastní indexů ve službě Cosmos DB s použitím příkazu createIndex, včetně omezení "unique".

Jedinečné indexy jsou k dispozici pro všechny účty Cosmos pomocí rozhraní API služby Azure Cosmos DB pro MongoDB.

## <a name="time-to-live-ttl"></a>Hodnota TTL (Time-To-Live)

Cosmos DB podporuje hodnotu time-to-live (TTL) podle časového razítka dokumentu. Hodnota TTL je možné povolit pro kolekce tak, že přejdete [webu Azure portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Správa uživatelů a rolí

Cosmos DB zatím nepodporuje uživatelů a rolí. Nicméně, Cosmos DB podporuje řízení přístupu na základě role (RBAC) a čtení i zápis a jen pro čtení hesla/klíče, které můžete získat prostřednictvím [webu Azure portal](https://portal.azure.com) (stránka připojovací řetězec).

## <a name="replication"></a>Replikace

Cosmos DB podporuje automatické, nativní replikace na nejnižší vrstvy. Tato logika umožňuje zároveň dosáhnout nízké latence a globální replikace. Cosmos DB nepodporuje ruční replikaci příkazy.

## <a name="write-concern"></a>Write Concern

Některé aplikace, [zápisu problém](https://docs.mongodb.com/manual/reference/write-concern/) která určuje počet odpovědí vyžaduje během operace zápisu. Kvůli tomu, jak Cosmos DB zpracovává replikaci na pozadí, jsou všechny zápisy ve výchozím nastavení automaticky kvora. Všechny zápisy uvedených klientským kódem se ignoruje. Další informace najdete v tématu popisujícím [využití úrovní konzistence pro maximalizaci dostupnosti a výkonu](consistency-levels.md).

## <a name="sharding"></a>Sharding

Cosmos DB podporuje na straně serveru, automatické horizontální dělení. Cosmos DB nepodporuje příkazy ruční horizontální dělení.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [použití studia 3T](mongodb-mongochef.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.
- Zjistěte, jak [použít Robo 3T](mongodb-robomongo.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.
- Prozkoumejte MongoDB [ukázky](mongodb-samples.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.

<sup>Poznámka: Tento článek popisuje funkce služby Azure Cosmos DB, která poskytuje kompatibilitu s protokolem při přenosu pomocí databáze MongoDB. Microsoft se nespustí databáze MongoDB pro tuto službu poskytovat. Azure Cosmos DB není přidružený žádný MongoDB, Inc.</sup>
