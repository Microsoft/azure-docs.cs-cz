---
title: Příkazy rozšíření MongoDB ke správě dat uložených v Azure Cosmos DB přes rozhraní API pro MongoDB
description: Tento článek popisuje, jak spravovat data uložená v Azure Cosmos DB přes rozhraní API pro MongoDB pomocí příkazů rozšíření MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: aef77f121f20d867c8ec5e764d8c9639c961713d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60446580"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Spravovat data uložená v Azure Cosmos DB přes rozhraní API pro MongoDB pomocí příkazů rozšíření MongoDB 

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Může komunikovat s rozhraním API pro Azure Cosmos DB pro MongoDB pomocí některého z oblasti open source [ovladače klienta MongoDB](https://docs.mongodb.org/ecosystem/drivers). Azure Cosmos DB API pro MongoDB umožňuje použití existujících ovladačů klienta dodržováním [přenosový protokol MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Pomocí rozhraní Azure Cosmos DB API pro MongoDB můžete využívat výhody služby Cosmos DB, jako je například globální distribuci, automatické horizontální dělení, vysokou dostupnost, garantuje latenci, automatické, šifrování v klidovém stavu, zálohování, a mnoho více, při zachování vašich investic ve vaší aplikaci MongoDB.

## <a name="mongodb-protocol-support"></a>Podpora protokolů pro MongoDB

Ve výchozím nastavení, rozhraní API Azure Cosmos DB pro MongoDB, je kompatibilní s databází MongoDB server verze 3.2, další podrobnosti najdete v tématu [podporovány funkcí a syntaxe](mongodb-feature-support.md). Funkce nebo operátory dotazu přidá MongoDB verze 3.4 jsou aktuálně k dispozici jako verze preview v Azure Cosmos DB API pro MongoDB. Následující příkazy rozšíření podporu určitých funkcí služby Azure Cosmos DB při provádění operací CRUD s daty uloženými v Azure Cosmos DB přes rozhraní API pro MongoDB:

* [Vytvoření databáze](#create-database)
* [Aktualizace databáze](#update-database)
* [Získat databázi](#get-database)
* [Vytvoření kolekce](#create-collection)
* [Aktualizace kolekce](#update-collection)
* [Získat kolekci](#get-collection)

## <a id="create-database"></a> Vytvoření databáze

Příkaz create database rozšíření vytvoří novou databázi MongoDB. Název databáze se používá v kontextu databáze, proti kterému spuštění příkazu. Formát příkazu CreateDatabase vypadá takto:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| customAction   |  string  |   Název vlastního příkazu musí být "CreateDatabase".      |
| offerThroughput | int  | Zřízená propustnost, kterou jste nastavili v databázi. Tento parametr je volitelný. |

### <a name="output"></a>Výstup

Vrátí výchozí odpověď vlastní příkaz. Zobrazit [výchozí výstup](#default-output) vlastní parametry ve výstupu příkazu.

### <a name="examples"></a>Příklady

**Vytvoření databáze**

Chcete-li vytvořit databázi s názvem "test", použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Vytvoření databáze s propustností**

Chcete-li vytvořit databázi s názvem "test" a zřízenou propustnost 1 000 RU, použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> Aktualizace databáze

Příkaz update databáze rozšíření aktualizuje vlastnosti přidružené k zadané databázi. V současné době lze aktualizovat pouze vlastnost "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| customAction    |    string     |   Název vlastní příkaz. Musí být "UpdateDatabase".      |
|  offerThroughput   |  int       |     Nové zřízená propustnost, kterou chcete nastavit v databázi.    |

### <a name="output"></a>Výstup

Vrátí výchozí odpověď vlastní příkaz. Zobrazit [výchozí výstup](#default-output) vlastní parametry ve výstupu příkazu.

### <a name="examples"></a>Příklady

**Aktualizace zřízená propustnost, které jsou přidružené k databázi**

Pokud chcete aktualizovat zřízená propustnost do databáze s názvem "test" na 1 200 RU, použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> Získat databázi

Příkaz get databáze rozšíření vrátí objekt databáze. Název databáze se používá v kontextu databáze, proti kterému spuštění příkazu.

```
{
  customAction: "GetDatabase"
}
```

Následující tabulka popisuje parametry v rámci příkazu:


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  customAction   |   string      |   Název vlastní příkaz. Musí být "GetDatabase"|
        
### <a name="output"></a>Výstup

Pokud příkaz proběhne úspěšně, odpověď obsahuje dokument s následující pole:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stav odpovědi. 1 == úspěch. 0 == selhání.      |
| `database`    |    `string`        |   Název databáze.      |
|   `provisionedThroughput`  |    `int`      |    Zřízená propustnost, který je nastaven na databázi. Toto je parametr volitelný odpovědi.     |

Pokud příkaz selže, vrátí se výchozí odpověď vlastní příkaz. Zobrazit [výchozí výstup](#default-output) vlastní parametry ve výstupu příkazu.

### <a name="examples"></a>Příklady

**Získat databázi**

Chcete-li získat objekt databáze pro databázi s názvem "test", použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> Vytvoření kolekce

Příkaz create kolekce rozšíření vytvoří novou kolekci MongoDB. Název databáze se používá v kontextu databáze, proti kterému spuštění příkazu. Formát příkazu CreateCollection vypadá takto:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| customAction    | string | Název vlastní příkaz. Musí být "CreateDatabase"     |
| kolekce      | string | Název kolekce                                   |
| offerThroughput | int    | Zřízená propustnost nastavit v databázi. Je volitelný parametr |
| shardKey        | string | Cesta klíče horizontálních oddílů k vytvoření horizontálně dělené kolekce. Je volitelný parametr |

### <a name="output"></a>Výstup

Vrátí výchozí odpověď vlastní příkaz. Zobrazit [výchozí výstup](#default-output) vlastní parametry ve výstupu příkazu.

### <a name="examples"></a>Příklady

**Vytvořit kolekci unsharded**

Vytvoření kolekce unsharded s názvem "testCollection" a zřízenou propustnost 1 000 RU, použijte následující příkaz: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Vytvoření horizontálně dělené kolekce**

Vytvoření horizontálně dělené kolekce s názvem "testCollection" a zřízenou propustnost 1 000 RU, použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> Aktualizace kolekce

Příkaz update kolekce rozšíření aktualizuje vlastnosti přidružené k zadané kolekci.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  customAction   |   string      |   Název vlastní příkaz. Musí být "UpdateCollection".      |
|  kolekce   |   string      |   Název kolekce.       |
| offerThroughput   |int|   Zřízená propustnost nastavit na kolekci.|

## <a name="output"></a>Výstup

Vrátí výchozí odpověď vlastní příkaz. Zobrazit [výchozí výstup](#default-output) vlastní parametry ve výstupu příkazu.

### <a name="examples"></a>Příklady

**Aktualizace zřízená propustnost související s kolekcí**

Pokud chcete aktualizovat zřízená propustnost kolekce s názvem "testCollection" na 1 200 RU, použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> Získat kolekci

Vlastní příkaz get kolekce vrátí objekt kolekce.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

Následující tabulka popisuje parametry v rámci příkazu:


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| customAction    |   string      |   Název vlastní příkaz. Musí být "GetCollection".      |
| kolekce    |    string     |    Název kolekce.     |

### <a name="output"></a>Výstup

Pokud příkaz proběhne úspěšně, odpověď obsahuje dokument s následující pole


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stav odpovědi. 1 == úspěch. 0 == selhání.      |
| `database`    |    `string`     |   Název databáze.      |
| `collection`    |    `string`     |    Název kolekce.     |
|  `shardKeyDefinition`   |   `document`      |  Specifikace dokumentu indexu použít jako klíč horizontálního oddílu. Toto je parametr volitelný odpovědi.       |
|  `provisionedThroughput`   |   `int`      |    Zřízená propustnost nastavit na kolekci. Toto je parametr volitelný odpovědi.     |

Pokud příkaz selže, vrátí se výchozí odpověď vlastní příkaz. Zobrazit [výchozí výstup](#default-output) vlastní parametry ve výstupu příkazu.

### <a name="examples"></a>Příklady

**Získat kolekci**

Chcete-li získat objekt kolekce pro kolekci s názvem "testCollection", použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> Výchozí výstup vlastní příkaz.

Pokud není zadán, obsahuje vlastní odpovědi dokument s následující pole:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stav odpovědi. 1 == úspěch. 0 == selhání.      |
| `code`    |   `int`      |   Pouze vrátí, když příkaz se nezdařil (to znamená ok == 0). Obsahuje kód chyby: MongoDB. Toto je parametr volitelný odpovědi.      |
|  `errMsg`   |  `string`      |    Pouze vrátí, když příkaz se nezdařil (to znamená ok == 0). Obsahuje uživatelsky přívětivou chybovou zprávu. Toto je parametr volitelný odpovědi.      |

## <a name="next-steps"></a>Další postup

Potom můžete přejít k další následující koncepty služby Azure Cosmos DB: 

* [Indexování ve službě Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Vypršení platnosti dat v Azure Cosmos DB automaticky s časem TTL](../cosmos-db/time-to-live.md)