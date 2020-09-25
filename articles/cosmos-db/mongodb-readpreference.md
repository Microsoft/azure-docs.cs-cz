---
title: Použití předvoleb pro čtení s rozhraním API Azure Cosmos DB pro MongoDB
description: Naučte se používat předvolbu MongoDB Read s rozhraním API Azure Cosmos DB pro MongoDB.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: devx-track-js
ms.openlocfilehash: a0bc2118c1a7e719c5ac45cb4d253a0708acefdf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91292334"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Jak globálně distribuovat čtení pomocí rozhraní Azure Cosmos DB API pro MongoDB

V tomto článku se dozvíte, jak globálně distribuovat operace čtení s nastavením [předvoleb čtení MongoDB](https://docs.mongodb.com/manual/core/read-preference/) pomocí rozhraní API Azure Cosmos DB pro MongoDB.

## <a name="prerequisites"></a>Požadavky 
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Pokyny k použití Azure Portal k nastavení účtu Cosmos s globální distribucí a připojení k němu najdete v tomto článku [rychlý Start](tutorial-global-distribution-mongodb.md) .

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Otevřete okno terminálu Git, jako je třeba Git Bash, a pomocí `cd` přejděte do pracovního adresáře.  

Spuštěním následujících příkazů naklonujte ukázkové úložiště. V závislosti na vaší platformě zájmu použijte jedno z následujících ukázkových úložišť:

1. [Ukázková aplikace .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Ukázková aplikace NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Ukázková aplikace Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Ukázková aplikace Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Ukázková aplikace SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Spuštění aplikace

V závislosti na použité platformě nainstalujte požadované balíčky a spusťte aplikaci. Pokud chcete nainstalovat závislosti, postupujte podle souboru READme zahrnutého do úložiště ukázkové aplikace. Například v ukázkové aplikaci NodeJS pomocí následujících příkazů nainstalujte požadované balíčky a spusťte aplikaci.

```bash
cd mean
npm install
node index.js
```
Aplikace se pokusí připojit ke zdroji MongoDB a dojde k chybě, protože připojovací řetězec je neplatný. Pokud chcete aktualizovat připojovací řetězec, postupujte podle kroků v souboru READme `url` . Aktualizujte také `readFromRegion` oblast pro čtení v účtu Cosmos. Následující pokyny jsou uvedené v ukázce NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Po provedení následujících kroků se ukázková aplikace spustí a vytvoří následující výstup:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Číst pomocí režimu předvolby čtení

Protokol MongoDB poskytuje klientům použití následující režimy předvolby pro čtení:

1. PRIMÁRNÍ
2. PRIMARY_PREFERRED
3. SEKUNDÁRNÍ
4. SECONDARY_PREFERRED
5. MÍSTA

Podrobnosti o chování každého z těchto režimů předvolby čtení najdete v podrobné dokumentaci k [chování předvoleb MongoDB Read](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) . V Cosmos DB primární mapuje na oblast zápisu a sekundární mapy na oblast čtení.

Na základě běžných scénářů doporučujeme použít následující nastavení:

1. Pokud je vyžadováno **čtení s nízkou latencí** , použijte **nejbližší** režim předvolby čtení. Toto nastavení přesměruje operace čtení na nejbližší dostupnou oblast. Všimněte si, že pokud je nejbližší oblastí oblast zápisu, pak jsou tyto operace směrovány do této oblasti.
2. Pokud se vyžaduje **Vysoká dostupnost a geografická distribuce čtení** (latence není omezením), použijte **primární** nebo **sekundární preferovaný** režim čtení. Toto nastavení směruje operace čtení do dostupné oblasti pro zápis nebo čtení v uvedeném pořadí. Pokud oblast není k dispozici, požadavky se přesměrují do další dostupné oblasti podle chování předvoleb pro čtení.

Následující fragment kódu z ukázkové aplikace ukazuje, jak nakonfigurovat nejbližší předvolbu pro čtení v NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Podobně níže uvedený fragment kódu ukazuje, jak nakonfigurovat SECONDARY_PREFERRED předvolby pro čtení v NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Předvolbu pro čtení lze také nastavit předáním `readPreference` jako parametru v možnostech identifikátoru URI připojovacího řetězce:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Přečtěte si odpovídající vzorová úložiště aplikací pro jiné platformy, jako je [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) a [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Čtení pomocí značek

Kromě režimu předvolby čtení umožňuje protokol MongoDB použití značek k přímému čtení operací. V rozhraní API Cosmos DB pro MongoDB `region` je značka standardně zahrnutá jako součást `isMaster` odpovědi:

```json
"tags": {
         "region": "West US"
      }
```

Proto může MongoClient použít `region` značku spolu s názvem oblasti a směrovat tak operace čtení na konkrétní oblasti. V případě účtů Cosmos se názvy oblastí nacházejí v Azure Portal vlevo v části **Nastavení – >data repliky globálně**. Toto nastavení je užitečné při dosahování **izolace čtení** – případy, kdy klientská aplikace chce směrovat operace čtení pouze do konkrétní oblasti. Toto nastavení je ideální pro scénáře typu neprodukčního a analytického prostředí, které běží na pozadí a nejsou důležitými službami pro produkční prostředí.

Následující fragment kódu z ukázkové aplikace ukazuje, jak nakonfigurovat předvolby pro čtení pomocí značek v NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Přečtěte si odpovídající vzorová úložiště aplikací pro jiné platformy, jako je [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) a [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

V tomto článku jste se naučili, jak globálně distribuovat operace čtení pomocí předvoleb pro čtení s rozhraním API Azure Cosmos DB pro MongoDB.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte všechny prostředky vytvořené tímto článkem v Azure Portal pomocí následujících kroků:

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku. 
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Importování dat MongoDB do databáze Azure Cosmos](mongodb-migrate.md)
* [Nastavení globálně distribuované databáze s rozhraním API Azure Cosmos DB pro MongoDB](tutorial-global-distribution-mongodb.md)
* [Vývoj místně pomocí emulátoru Azure Cosmos DB](local-emulator.md)
