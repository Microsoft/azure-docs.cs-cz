---
title: Příkazy rozšíření MongoDB pro správu dat v rozhraní API Azure Cosmos DB pro MongoDB
description: Tento článek popisuje, jak pomocí příkazů rozšíření MongoDB spravovat data uložená v rozhraní API Azure Cosmos DB pro MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2020
ms.author: lbosq
ms.custom: devx-track-js
ms.openlocfilehash: 4b069dea3f07477fcbca21e08166cdfad8cad2cf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326724"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Použití příkazů rozšíření MongoDB ke správě dat uložených v rozhraní API Azure Cosmos DB pro MongoDB 

Následující dokument obsahuje příkazy vlastní akce, které jsou specifické pro rozhraní API Azure Cosmos DB pro MongoDB. Tyto příkazy lze použít k vytvoření a získání databázových prostředků, které jsou specifické pro [model Azure Cosmos DB kapacity](databases-containers-items.md).

Díky rozhraní API Azure Cosmos DB pro MongoDB můžete využívat výhod Cosmos DB, jako je globální distribuce, automatická horizontálního dělení, vysoká dostupnost, záruky latence, automatické, šifrování v klidovém režimu, zálohování a spousta dalších, a zároveň zachovat investice do vaší aplikace MongoDB. Můžete komunikovat s rozhraním API Azure Cosmos DB pro MongoDB pomocí kteréhokoli z open-source [klientských ovladačů MongoDB](https://docs.mongodb.org/ecosystem/drivers). Rozhraní API pro Azure Cosmos DB pro MongoDB umožňuje použití existujících ovladačů klientů, které dodržuje [MongoDB síťový protokol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

## <a name="mongodb-protocol-support"></a>Podpora protokolu MongoDB

Rozhraní API Azure Cosmos DB pro MongoDB je kompatibilní s MongoDB serverem verze 3,2 a 3,6. Další podrobnosti najdete v tématu [podporované funkce a syntaxe](mongodb-feature-support.md) . 

Následující příkazy rozšíření poskytují možnost vytvářet a upravovat prostředky specifické pro Azure Cosmos DB přes požadavky databáze:

* [Vytvořit databázi](#create-database)
* [Aktualizovat databázi](#update-database)
* [Získat databázi](#get-database)
* [Vytvořit kolekci](#create-collection)
* [Aktualizovat kolekci](#update-collection)
* [Získat kolekci](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> Vytvořit databázi

Příkaz vytvořit rozšíření databáze vytvoří novou databázi MongoDB. Název databáze lze použít z kontextu databáze nastaveného `use database` příkazem. Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| `customAction`   |  `string`  |   Název vlastního příkazu musí být "CreateDatabase".      |
| `offerThroughput` | `int`  | Zřízená propustnost, kterou jste nastavili v databázi. Tento parametr je volitelný. |
| `autoScaleSettings` | `Object` | Vyžaduje se pro [režim automatického škálování](provision-throughput-autoscale.md). Tento objekt obsahuje nastavení přidružená k režimu kapacity automatického škálování. Můžete nastavit `maxThroughput` hodnotu, která popisuje nejvyšší množství jednotek požadavků, na které se kolekce zvyšuje dynamicky. |

### <a name="output"></a>Výstup

Pokud bude příkaz úspěšný, vrátí následující odpověď:

```javascript
{ "ok" : 1 }
```

Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

#### <a name="create-a-database"></a>Vytvoření databáze

K vytvoření databáze s názvem `"test"` , která používá všechny výchozí hodnoty, použijte následující příkaz:

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

Tento příkaz vytvoří databázi bez propustnosti na úrovni databáze. To znamená, že kolekce v rámci této databáze budou potřebovat určení množství propustnosti, kterou potřebujete použít.

#### <a name="create-a-database-with-throughput"></a>Vytvoření databáze s propustností

K vytvoření databáze s názvem `"test"` a k určení propustnosti 1000 ru na [úrovni databáze](set-throughput.md#set-throughput-on-a-database) použijte následující příkaz:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

Tím se vytvoří databáze a nastaví se její propustnost. Všechny kolekce v rámci této databáze budou sdílet propustnost, pokud se kolekce nevytváří s [určitou úrovní propustnosti](set-throughput.md#set-throughput-on-a-database-and-a-container).

#### <a name="create-a-database-with-autoscale-throughput"></a>Vytvoření databáze s propustností automatického škálování

K vytvoření databáze s názvem `"test"` a k určení propustnosti maximálního automatického škálování 20 000 ru/s na [úrovni databáze](set-throughput.md#set-throughput-on-a-database)použijte následující příkaz:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> Aktualizovat databázi

Příkaz aktualizovat databázi rozšíření aktualizuje vlastnosti přidružené k zadané databázi. Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| `customAction`    |    `string`     |   Název vlastního příkazu Musí být "UpdateDatabase".      |
|  `offerThroughput`   |  `int`       |     Nová zřízená propustnost, kterou chcete nastavit v databázi, pokud databáze používá [propustnost na úrovni databáze](set-throughput.md#set-throughput-on-a-database) .  |
| `autoScaleSettings` | `Object` | Vyžaduje se pro [režim automatického škálování](provision-throughput-autoscale.md). Tento objekt obsahuje nastavení přidružená k režimu kapacity automatického škálování. Můžete nastavit `maxThroughput` hodnotu, která popisuje nejvyšší množství jednotek požadavků, na které se bude databáze zvyšovat dynamicky. |

Tento příkaz používá databázi zadanou v kontextu relace. To je databáze, kterou jste použili v `use <database>` příkazu. V tuto chvíli není možné změnit název databáze pomocí tohoto příkazu.

### <a name="output"></a>Výstup

Pokud bude příkaz úspěšný, vrátí následující odpověď:

```javascript
{ "ok" : 1 }
```

Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>Aktualizace zřízené propustnosti přidružené k databázi

K aktualizaci zřízené propustnosti databáze s názvem `"test"` na 1200 ru použijte následující příkaz:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>Aktualizace propustnosti automatického škálování přidruženého k databázi

Chcete-li aktualizovat zřízenou propustnost databáze s názvem `"test"` na 20 000 ru nebo ji transformovat na [úroveň propustnosti automatického škálování](provision-throughput-autoscale.md), použijte následující příkaz:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> Získat databázi

Příkaz Get Database Extension vrátí objekt databáze. Název databáze je použit z kontextu databáze, proti kterému je příkaz spuštěn.

```javascript
{
  customAction: "GetDatabase"
}
```

Následující tabulka popisuje parametry v rámci příkazu:


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Název vlastního příkazu Musí být "getdatabase"|
        
### <a name="output"></a>Výstup

Pokud je příkaz úspěšný, odpověď obsahuje dokument s následujícími poli:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stav odpovědi 1 = = úspěch. 0 = = chyba.      |
| `database`    |    `string`        |   Název databáze.      |
|   `provisionedThroughput`  |    `int`      |    Zřízená propustnost, která je nastavena v databázi, pokud databáze používá  [Ruční propustnost na úrovni databáze](set-throughput.md#set-throughput-on-a-database)     |
| `autoScaleSettings` | `Object` | Tento objekt obsahuje parametry kapacity přidružené k databázi, pokud používá [režim automatického škálování](provision-throughput-autoscale.md). `maxThroughput`Hodnota popisuje nejvyšší množství jednotek požadavků, které se databáze zvýší na dynamicky. |

Pokud příkaz neproběhne úspěšně, vrátí se výchozí odpověď vlastního příkazu. Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

#### <a name="get-the-database"></a>Získat databázi

Chcete-li získat objekt databáze pro databázi s názvem `"test"` , použijte následující příkaz:

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

Pokud databáze nemá žádnou přidruženou propustnost, výstup by byl:

```javascript
{ "database" : "test", "ok" : 1 }
```

Pokud má databáze přidruženou [Ruční propustnost na úrovni databáze](set-throughput.md#set-throughput-on-a-database) , zobrazí výstup tyto `provisionedThroughput` hodnoty:

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

Pokud má databáze přidruženou [propustnost automatického škálování na úrovni databáze](provision-throughput-autoscale.md) , výstup by zobrazil `provisionedThroughput` , který popisuje minimální ru/s pro databázi, a `autoScaleSettings` objekt `maxThroughput` , včetně, který popisuje maximální ru/s pro databázi.

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> Vytvořit kolekci

Příkaz vytvořit rozšíření kolekce vytvoří novou kolekci MongoDB. Název databáze se používá z kontextu databáze nastaveného `use database` příkazem. Formát příkazu Vytvořitcollection je následující:

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  offerThroughput: (int), // Amount of throughput allocated to a specific collection

}
```

Následující tabulka popisuje parametry v rámci příkazu:

| **Pole** | **Typ** | **Požadováno** | **Popis** |
|---------|---------|---------|---------|
| `customAction` | `string` | Vyžadováno | Název vlastního příkazu Musí být "Vytvořitcollection".|
| `collection` | `string` | Vyžadováno | Název kolekce Nejsou povoleny žádné speciální znaky ani mezery.|
| `offerThroughput` | `int` | Volitelné | Zřízená propustnost pro nastavení databáze. Pokud tento parametr není zadán, bude výchozí hodnota minimálně 400 RU/s. * Pokud chcete zadat propustnost přesahující 10 000 RU/s, `shardKey` parametr je povinný.|
| `shardKey` | `string` | Vyžadováno pro kolekce s velkou propustností | Cesta k horizontálních oddílůmu klíči pro kolekci horizontálně dělené Tento parametr je vyžadován, pokud nastavíte více než 10 000 RU/s v `offerThroughput` .  Pokud je zadáno, budou všechny vložené dokumenty vyžadovat tento klíč a hodnotu. |
| `autoScaleSettings` | `Object` | Vyžaduje se pro [režim automatického škálování](provision-throughput-autoscale.md) . | Tento objekt obsahuje nastavení přidružená k režimu kapacity automatického škálování. Můžete nastavit `maxThroughput` hodnotu, která popisuje nejvyšší množství jednotek požadavků, na které se kolekce zvyšuje dynamicky. |

### <a name="output"></a>Výstup

Vrátí výchozí odpověď vlastního příkazu. Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

#### <a name="create-a-collection-with-the-minimum-configuration"></a>Vytvoření kolekce s minimální konfigurací

Chcete-li vytvořit novou kolekci s názvem `"testCollection"` a výchozími hodnotami, použijte následující příkaz: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

Výsledkem bude, že se vytvoří nová pevná, unsharded, kolekce s 400RU/s a `_id` automaticky se vytvoří index pole. Tento typ konfigurace se použije také při vytváření nových kolekcí prostřednictvím `insert()` funkce. Příklad: 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>Vytvoření kolekce unsharded

Chcete-li vytvořit kolekci unsharded s názvem `"testCollection"` a zřízenou propustností 1000 ru, použijte následující příkaz: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

Kolekci můžete vytvořit s až 10 000 RU/s, a to `offerThroughput` bez nutnosti zadat horizontálních oddílů klíč. Kolekce s větší propustností najdete v další části.

#### <a name="create-a-sharded-collection"></a>Vytvoření kolekce horizontálně dělené

Chcete-li vytvořit kolekci horizontálně dělené s názvem `"testCollection"` a zřízenou propustností 11 000 ru a `shardkey` vlastností "a. b", použijte následující příkaz:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

Tento příkaz nyní vyžaduje `shardKey` parametr, protože více než 10 000 ru/s je zadáno v `offerThroughput` .

#### <a name="create-an-unsharded-autoscale-collection"></a>Vytvoření kolekce automatického škálování unsharded

Pokud chcete vytvořit kolekci unsharded s názvem `'testCollection'` , která používá [kapacitu propustnosti automatického škálování](provision-throughput-autoscale.md) nastavenou na 4 000 ru/s, použijte následující příkaz:

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

Pro `autoScaleSettings.maxThroughput` hodnotu můžete zadat rozsah od 4 000 ru/s do 10 000 ru/s bez horizontálních oddílů klíče. Pro vyšší propustnost automatického škálování je nutné zadat `shardKey` parametr.

#### <a name="create-a-sharded-autoscale-collection"></a>Vytvoření kolekce automatického škálování horizontálně dělené

Chcete-li vytvořit kolekci horizontálně dělené s názvem `'testCollection'` s názvem horizontálních oddílů Key `'a.b'` a s využitím [kapacity propustnosti automatického škálování](provision-throughput-autoscale.md) nastavenou na 20 000 ru/s, použijte následující příkaz:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> Aktualizovat kolekci

Příkaz rozšíření kolekce aktualizací aktualizuje vlastnosti přidružené k zadané kolekci.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  offerThroughput: (int) // New throughput that will be set to the collection
}
```

Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Název vlastního příkazu Musí být "Updatecollection".      |
|  `collection`   |   `string`      |   Název kolekce       |
| `offerThroughput` | `int` |   Zřízená propustnost, která se má nastavit v kolekci|
| `autoScaleSettings` | `Object` | Vyžaduje se pro [režim automatického škálování](provision-throughput-autoscale.md). Tento objekt obsahuje nastavení přidružená k režimu kapacity automatického škálování. `maxThroughput`Hodnota popisuje nejvyšší množství jednotek požadavků, které bude kolekce zvyšovat na dynamicky. |

## <a name="output"></a>Výstup

Vrátí výchozí odpověď vlastního příkazu. Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>Aktualizace zřízené propustnosti přidružené ke kolekci

K aktualizaci zřízené propustnosti kolekce s názvem `"testCollection"` na 1200 ru použijte následující příkaz:

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> Získat kolekci

Vlastní příkaz Get Collection vrátí objekt kolekce.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

Následující tabulka popisuje parametry v rámci příkazu:


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| `customAction`    |   `string`      |   Název vlastního příkazu Musí být "GetCollection".      |
| `collection`    |    `string`     |    Název kolekce     |

### <a name="output"></a>Výstup

Pokud je příkaz úspěšný, odpověď obsahuje dokument s následujícími poli


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stav odpovědi 1 = = úspěch. 0 = = chyba.      |
| `database`    |    `string`     |   Název databáze.      |
| `collection`    |    `string`     |    Název kolekce     |
|  `shardKeyDefinition`   |   `document`      |  Dokument specifikace indexu použitý jako horizontálních oddílů klíč Toto je volitelný parametr odpovědi.       |
|  `provisionedThroughput`   |   `int`      |    Zřízená propustnost, která se má nastavit v kolekci Toto je volitelný parametr odpovědi.     |
| `autoScaleSettings` | `Object` | Tento objekt obsahuje parametry kapacity přidružené k databázi, pokud používá [režim automatického škálování](provision-throughput-autoscale.md). `maxThroughput`Hodnota popisuje nejvyšší množství jednotek požadavků, které bude kolekce zvyšovat na dynamicky. |

Pokud příkaz neproběhne úspěšně, vrátí se výchozí odpověď vlastního příkazu. Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

#### <a name="get-the-collection"></a>Získat kolekci

Chcete-li získat objekt kolekce pro kolekci s názvem `"testCollection"` , použijte následující příkaz:

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

Pokud má kolekce přidruženou kapacitu propustnosti, bude obsahovat `provisionedThroughput` hodnotu a výstup bude:

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

Pokud má kolekce přidruženou propustnost automatického škálování, bude obsahovat `autoScaleSettings` objekt s `maxThroughput` parametrem, který definuje maximální propustnost, které se kolekce narůstá na dynamicky. Kromě toho bude také obsahovat `provisionedThroughput` hodnotu, která definuje minimální propustnost, které tato kolekce bude snižovat, pokud v kolekci neexistují žádné žádosti: 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

Pokud kolekce sdílí propustnost na [úrovni databáze](set-throughput.md#set-throughput-on-a-database), buď v režimu automatického škálování, nebo ručně, výstup by byl:

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> Výchozí výstup vlastního příkazu

Pokud tento parametr nezadáte, vlastní odpověď obsahuje dokument s následujícími poli:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stav odpovědi 1 = = úspěch. 0 = = chyba.      |
| `code`    |   `int`      |   Vrátí se jenom v případě, že se příkaz nezdařil (tj. ok = = 0). Obsahuje kód chyby MongoDB. Toto je volitelný parametr odpovědi.      |
|  `errMsg`   |  `string`      |    Vrátí se jenom v případě, že se příkaz nezdařil (tj. ok = = 0). Obsahuje uživatelsky přívětivou chybovou zprávu. Toto je volitelný parametr odpovědi.      |

Příklad:

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>Další kroky

Dále si můžete přečíst následující pojmy Azure Cosmos DB: 

* [Indexování ve službě Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Automatické vypršení platnosti dat v Azure Cosmos DB s časem až Live](../cosmos-db/time-to-live.md)
