---
title: Použití předvolby Čtení s rozhraním API Azure Cosmos DB pro MongoDB
description: Zjistěte, jak používat předvolbu čtení MongoDB s rozhraním API Služby Azure Cosmos DB pro MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 579767a0d535605a2316c35bd413a75474b5a3de
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410000"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Jak globálně distribuovat čtení pomocí rozhraní API Azure Cosmos DB pro MongoDB

Tento článek ukazuje, jak globálně distribuovat operace čtení s [nastavením předvoleb čtení MongoDB](https://docs.mongodb.com/manual/core/read-preference/) pomocí rozhraní API Azure Cosmos DB pro MongoDB.

## <a name="prerequisites"></a>Požadavky 
Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Pokyny k nastavení účtu Cosmos s globální distribucí a připojení k němu najdete v tomto článku [pro rychlý start.](tutorial-global-distribution-mongodb.md)

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Otevřete okno terminálu Git, jako je třeba Git Bash, a pomocí `cd` přejděte do pracovního adresáře.  

Spuštěním následujících příkazů naklonujte ukázkové úložiště. Na základě vaší platformy zájmu použijte jeden z následujících ukázkových úložišť:

1. [Ukázková aplikace ROZHRANÍ .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Ukázková aplikace NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Aplikace vzorku Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Ukázková aplikace Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Ukázková aplikace SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Spuštění aplikace

V závislosti na použité platformě nainstalujte požadované balíčky a spusťte aplikaci. Chcete-li nainstalovat závislosti, postupujte podle readme zahrnuty v úložišti ukázkových aplikací. Například v ukázkové aplikaci NodeJS použijte následující příkazy k instalaci požadovaných balíčků a spuštění aplikace.

```bash
cd mean
npm install
node index.js
```
Aplikace se pokusí připojit ke zdroji MongoDB a selže, protože připojovací řetězec je neplatný. Podle pokynů v readme aktualizovat připojovací řetězec `url`. Také aktualizujte `readFromRegion` na oblast pro čtení ve vašem účtu Cosmos. Následující pokyny jsou ze vzorku NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Po provedení následujících kroků ukázkové aplikace spustí a vytvoří následující výstup:

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

## <a name="read-using-read-preference-mode"></a>Čtení v režimu předvoleb čtení

Protokol MongoDB poskytuje klientům následující režimy předvoleb čtení:

1. Primární
2. PRIMARY_PREFERRED
3. Sekundární
4. SECONDARY_PREFERRED
5. Nejbližší

Podrobnosti o chování každého z těchto režimů předvoleb čtení naleznete v podrobné dokumentaci [o chování předvoleb čtení mongoDB.](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) V Cosmos DB primární mapy write oblasti a sekundární mapy na oblast ČTENÍ.

Na základě běžných scénářů doporučujeme použít následující nastavení:

1. Pokud je **vyžadováno čtení s nízkou latencí,** použijte režim předvoleb čtení **NEAREST.** Toto nastavení přesměruje operace čtení do nejbližší dostupné oblasti. Všimněte si, že pokud nejbližší oblast je oblast WRITE, pak tyto operace jsou směrovány do této oblasti.
2. Pokud je požadována **vysoká dostupnost a geografická distribuce čtení** (latence není omezení), použijte režim předvoleb **primární upřednostňované** nebo **sekundární upřednostňované** čtení. Toto nastavení směruje operace čtení do oblasti zápisu nebo čtení v uvedeném pořadí. Pokud oblast není k dispozici, pak požadavky jsou směrovány do další dostupné oblasti podle chování předvoleb pro čtení.

Následující úryvek z ukázkové aplikace ukazuje, jak nakonfigurovat předvolbu čtení NEAREST v NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Podobně následující úryvek ukazuje, jak nakonfigurovat předvolbu čtení SECONDARY_PREFERRED v NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Předvolbu čtení lze také `readPreference` nastavit předáním jako parametr v možnostech identifikátoru URI připojovacího řetězce:

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

Odkazovat na odpovídající ukázkové aplikace repo pro jiné platformy, jako je [například .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) a [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Čtení pomocí značek

Kromě režimu předvolby čtení umožňuje protokol MongoDB použití značek k přímým operacím čtení. V rozhraní API Cosmos DB pro MongoDB je `region` značka ve `isMaster` výchozím nastavení zahrnuta jako součást odpovědi:

```json
"tags": {
         "region": "West US"
      }
```

Proto MongoClient můžete `region` použít značku spolu s názvem oblasti k přímé operace čtení do určitých oblastí. Pro účty Cosmos se názvy oblastí najdou na portálu Azure vlevo v **části Data repliky >nastavení mj.** Toto nastavení je užitečné pro dosažení **izolace čtení** – případy, ve kterých klientská aplikace chcete směrovat operace čtení pouze do určité oblasti. Toto nastavení je ideální pro scénáře typu neprodukční/analytické, které běží na pozadí a nejsou kritické služby pro produkční prostředí.

Následující úryvek z ukázkové aplikace ukazuje, jak nakonfigurovat předvolbu čtení pomocí značek v NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Odkazovat na odpovídající ukázkové aplikace repo pro jiné platformy, jako je [například .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) a [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

V tomto článku jste se naučili globálně distribuovat operace čtení pomocí předvolby čtení s rozhraním API Azure Cosmos DB pro MongoDB.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v používání této aplikace, odstraňte všechny prostředky vytvořené v tomto článku na webu Azure Portal pomocí následujících kroků:

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku. 
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Importování dat MongoDB do databáze Azure Cosmos](mongodb-migrate.md)
* [Nastavení globálně distribuované databáze pomocí rozhraní API Azure Cosmos DB pro MongoDB](tutorial-global-distribution-mongodb.md)
* [Vývoj místně pomocí emulátoru Azure Cosmos DB](local-emulator.md)
