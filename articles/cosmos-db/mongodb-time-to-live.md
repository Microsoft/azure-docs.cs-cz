---
title: Funkce MongoDB na dokument TTL v Azure Cosmos DB
description: Naučte se, jak nastavit hodnotu Time to Live pro dokumenty pomocí rozhraní API Azure Cosmos DB pro MongoDB, aby je po určitou dobu automaticky vymazala ze systému.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: javascript
ms.topic: how-to
ms.date: 12/26/2018
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 6ef3092c611e1eede2be1e0994ce69959a46f1d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340919"
---
# <a name="expire-data-with-azure-cosmos-dbs-api-for-mongodb"></a>Vypršení platnosti dat s využitím rozhraní API služby Azure Cosmos DB pro MongoDB.
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Funkce TTL (Time to Live) umožňuje automatické vypršení platnosti dat v databázi. Rozhraní API Azure Cosmos DB pro MongoDB využívá základní možnosti TTL Cosmos DB. Podporují se dva režimy: nastavení výchozí hodnoty TTL pro celou kolekci a nastavení hodnot TTL pro každý dokument zvlášť. Logika řízení indexů TTL a hodnot TTL pro jednotlivé dokumenty v rozhraní Cosmos DB API pro MongoDB je [stejná jako v Cosmos DB](../cosmos-db/mongodb-indexing.md).

## <a name="ttl-indexes"></a>Indexy TTL
Pokud chcete v kolekci povolit všeobecnou hodnotu TTL, je potřeba vytvořit [index TTL (Time to Live)](../cosmos-db/mongodb-indexing.md). Index TTL je index u pole _ts s hodnotou expireAfterSeconds.

Příklad:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : true,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Příkaz v předchozím příkladu vytvoří index s funkcí TTL. Po vytvoření indexu databáze z dané kolekce automaticky odstraní všechny dokumenty, které se během posledních 10 sekund neupravily. 

> [!NOTE]
> Pole **_ts** je pole specifické pro službu Cosmos DB a není přístupné z klientů MongoDB. Jedná se o vyhrazenou (systémovou) vlastnost obsahující časové razítko poslední úpravy dokumentu.
>
    
Tady je navíc příklad v jazyce C#: 

```csharp
var options = new CreateIndexOptions {ExpireAfter = TimeSpan.FromSeconds(10)}; 
var field = new StringFieldDefinition<BsonDocument>("_ts"); 
var indexDefinition = new IndexKeysDefinitionBuilder<BsonDocument>().Ascending(field); 
await collection.Indexes.CreateOneAsync(indexDefinition, options); 
``` 

## <a name="set-time-to-live-value-for-a-document"></a>Nastavení hodnoty TTL pro dokument 
Podporují se také hodnoty TTL pro jednotlivé dokumenty. Dokumenty musí obsahovat vlastnost ttl (malými písmeny) na kořenové úrovni a pro danou kolekci musí být vytvořený index TTL, jak je popsáno výše. Hodnoty TTL nastavené pro dokument přepíší hodnotu TTL kolekce.

Hodnota TTL musí být typu int32. Případně to může být hodnota typu int64, která se vejde do typu int32, nebo typu double bez desetinné části, která se vejde do typu int32. Hodnoty vlastnosti TTL, které neodpovídají těmto specifikacím, jsou povolené, ale nepovažují se za smysluplné hodnoty TTL dokumentu.

Hodnota TTL pro dokument je volitelná – do kolekce je možné vkládat i dokumenty bez hodnoty TTL.  V takovém případě se bude respektovat hodnota TTL kolekce. 

Následující dokumenty mají platné hodnoty TTL. Po vložení dokumentů přepíší hodnoty TTL dokumentu hodnoty TTL kolekce. Dokumenty se proto odeberou po 20 sekundách.   

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.0}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberInt(20)}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(20)}) 
```

Následující dokumenty mají neplatné hodnoty TTL. Dokumenty se vloží, ale hodnota TTL dokumentu se nebude respektovat. Dokumenty se proto kvůli hodnotě TTL kolekce odeberou po 10 sekundách. 

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.5}) //TTL value contains non-zero decimal part. 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(2147483649)}) //TTL value is greater than Int32.MaxValue (2,147,483,648). 
``` 

## <a name="how-to-activate-the-per-document-ttl-feature"></a>Postup aktivace funkce TTL pro jednotlivé dokumenty

Funkci MongoDB pro každý dokument lze aktivovat pomocí rozhraní API Azure Cosmos DB pro.

:::image type="content" source="./media/mongodb-ttl/mongodb_portal_ttl.png" alt-text="Snímek obrazovky s aktivací funkce TTL na základě dokumentů na portálu":::

## <a name="next-steps"></a>Další kroky
* [Automatické vypršení platnosti dat v Azure Cosmos DB s časem až Live](../cosmos-db/time-to-live.md)
* [Indexování databáze Cosmos s nakonfigurovaným rozhraním API Azure Cosmos DB pro MongoDB](../cosmos-db/mongodb-indexing.md)
