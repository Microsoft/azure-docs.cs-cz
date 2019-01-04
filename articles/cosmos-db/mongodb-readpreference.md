---
title: Použití předvoleb pro čtení MongoDB pomocí rozhraní API služby Azure Cosmos DB pro MongoDB
description: Další informace o použití předvoleb pro čtení MongoDB pomocí rozhraní Azure Cosmos DB API pro MongoDB
services: cosmos-db
author: vidhoonv
ms.author: sclyon
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2018
ms.openlocfilehash: 25c1872a677b05980899307a8de9f9b51fa749f9
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53787831"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Jak globálně distribuovat přečte pomocí rozhraní API služby Azure Cosmos DB pro MongoDB

Tento článek popisuje, jak globálně distribuovat operace čtení se [předvoleb pro čtení MongoDB](https://docs.mongodb.com/manual/core/read-preference/) nastavení pomocí rozhraní API služby Azure Cosmos DB pro MongoDB.

## <a name="prerequisites"></a>Požadavky 
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Projít tento [rychlý Start](tutorial-global-distribution-mongodb.md) najdete pokyny, pomocí webu Azure portal nastavit až účtu Cosmos s globální distribucí a připojte se k němu.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Otevřete okno terminálu Git, jako je třeba Git Bash, a pomocí `cd` přejděte do pracovního adresáře.  

Spuštěním následujících příkazů naklonujte ukázkové úložiště. Založené na vaší platformě, která vás zajímá, použijte jednu z následujících ukázkové úložiště:

1. [Ukázková aplikace .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Ukázková aplikace NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose ukázkové aplikace](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Ukázkové aplikace v Javě](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot ukázkové aplikace](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Spuštění aplikace

V závislosti na platformě používat nainstalujte požadované balíčky a spusťte aplikaci. Pokud chcete nainstalovat závislosti, postupujte podle soubor README v úložišti ukázek aplikací. Například v ukázkové aplikaci NodeJS, pomocí následujících příkazů nainstalujte požadované balíčky a spusťte aplikaci.

```bash
cd mean
npm install
node index.js
```
Aplikace se pokusí připojit ke zdroji MongoDB a selže, protože připojovací řetězec je neplatný. Postupujte podle kroků v souboru README aktualizovat připojovací řetězec `url`. Také aktualizovat `readFromRegion` do oblasti čtení ve vašem účtu Cosmos. Následující pokyny jsou z ukázky Node.js:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Po provedení těchto kroků, ukázkové aplikace se spustí a vytvoří následující výstup:

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

## <a name="read-using-read-preference-mode"></a>Přečtěte si v režimu předvoleb pro čtení

Protokol MongoDB nabízí následující režimy předvoleb pro čtení pro klienty použít:

1. PRIMÁRNÍ
2. PRIMARY_PREFERRED
3. SEKUNDÁRNÍ
4. SECONDARY_PREFERRED
5. NEJBLIŽŠÍ

Odkazovat na podrobné [předvoleb pro čtení MongoDB chování](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) podrobnosti naleznete v dokumentaci na chování každého z nich číst předvoleb režimy. Ve službě Cosmos DB primární mapuje na oblasti pro zápis a sekundární mapuje na oblasti pro čtení.

Na základě běžných scénářů, doporučujeme použít následující nastavení:

1. Pokud **nízké latence čtení** jsou povinné, použijte **NEAREST** předvoleb režimu pro čtení. Toto nastavení směruje operace čtení pro nejbližší dostupné oblasti. Všimněte si, že pokud je nejbližší oblast je oblast pro zápis, pak tyto operace jsou přesměrováni na danou oblast.
2. Pokud **vysoké dostupnosti a geografickou distribuci čtení** jsou požadovány (latence není omezení), pak použít **sekundární upřednostňované** předvoleb režimu pro čtení. Toto nastavení bude směrovat operace čtení do dostupné oblasti pro čtení. Pokud není k dispozici žádná oblast čtení, jsou směrovány požadavky do oblasti pro zápis.

Následující fragment kódu v ukázkové aplikaci ukazuje postup při konfiguraci NEJBLIŽŠÍ předvoleb pro čtení v NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Následující fragment podobně, ukazuje postup při konfiguraci SECONDARY_PREFERRED předvoleb pro čtení v NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Předvolba pro čtení lze také nastavit předáním `readPreference` jako parametr v možnosti připojovacího řetězce identifikátoru URI:

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

Odkazovat na odpovídající úložiště ukázkové aplikace pro jiné platformy, jako například [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) a [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Přečtěte si pomocí značek

Kromě režimu předvoleb pro čtení protokol MongoDB umožňuje použití značek ke směrování operace čtení. V rozhraní API služby Cosmos DB pro MongoDB `region` značka je zahrnuté ve výchozím nastavení jako součást `isMaster` odpovědi:

```json
"tags": {
         "region": "West US"
      }
```

Proto můžete použít položky MongoClient `region` označit spolu s názvem oblast pro přesměrování operací čtení do konkrétních oblastí. Pro účty služby Cosmos, názvy oblastí najdete na webu Azure portal na levé straně v části **Nastavení -> data repliky globálně**. Toto nastavení je užitečné pro dosažení **čtení izolace** -případů, ve které klientská aplikace chtít směrovat operace čtení jenom určité oblasti. Toto nastavení je ideální pro na produkční a analýzy zadejte scénáře, které běží na pozadí a nejsou produkční důležité služby.

Následující fragment kódu v ukázkové aplikaci ukazuje postup při konfiguraci předvoleb pro čtení se značkami v NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Odkazovat na odpovídající úložiště ukázkové aplikace pro jiné platformy, jako například [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) a [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

V tomto článku jste zjistili, jak globálně distribuovat operace čtení použití předvoleb pro čtení s rozhraním API služby Azure Cosmos DB pro MongoDB.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci používat i nadále, odstraňte všechny prostředky vytvořené v tomto článku na webu Azure Portal následujícím postupem:

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku. 
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

* [Importování dat MongoDB do služby Azure Cosmos DB](mongodb-migrate.md)
* [Instalační program globálně distribuovaná databáze s rozhraním API služby Azure Cosmos DB pro MongoDB](tutorial-global-distribution-mongodb.md)
* [Vývoj v místním prostředí pomocí emulátoru služby Azure Cosmos DB](local-emulator.md)
