---
title: Použití předvoleb pro čtení MongoDB pomocí rozhraní API MongoDB služby Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o použití předvoleb pro čtení MongoDB pomocí rozhraní MongoDB API Azure Cosmos DB
services: cosmos-db
author: vidhoonv
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: ''
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: sclyon
ms.openlocfilehash: 90c8d73e32f4c99c6871ce9cdb7839cd1d380b9b
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055251"
---
# <a name="how-to-globally-distribute-reads-using-read-preference-with-the-azure-cosmos-db-mongodb-api"></a>Jak globálně distribuovat čtení použití předvoleb pro čtení s MongoDB API služby Azure Cosmos DB 

Tento článek popisuje, jak globálně distribuovat pomocí operace čtení [předvoleb pro čtení MongoDB](https://docs.mongodb.com/manual/core/read-preference/) nastavení s rozhraním API MongoDB služby Azure Cosmos DB. 

## <a name="prerequisites"></a>Požadavky 
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Projít tento [rychlý Start](tutorial-global-distribution-mongodb.md) článku instrukce k používání na webu Azure portal k nastavení účtu Azure Cosmos DB s globální distribucí a připojte se pomocí rozhraní MongoDB API.

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
Aplikace se pokusí připojit ke zdroji MongoDB a selže, protože připojovací řetězec je neplatný. Postupujte podle kroků v souboru README aktualizovat připojovací řetězec `url`. Také aktualizovat `readFromRegion` do oblasti čtení ve vašem účtu Azure Cosmos DB. Následující pokyny jsou z ukázky Node.js:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos DB account's values. 
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

MongoDB nabízí následující režimy předvoleb pro čtení pro klienty použít:

1. Primární
2. PRIMARY_PREFERRED
3. SEKUNDÁRNÍ
4. SECONDARY_PREFERRED
5. NEJBLIŽŠÍ

Odkazovat na podrobné [předvoleb pro čtení MongoDB chování](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) podrobnosti naleznete v dokumentaci na chování každého z nich číst předvoleb režimy. Ve službě Azure Cosmos DB primární mapuje na oblasti pro zápis a sekundární mapuje na oblasti pro čtení.

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

Odkazovat na odpovídající úložiště ukázkové aplikace pro jiné platformy, jako například [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) a [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Přečtěte si pomocí značek

Kromě režimu předvoleb pro čtení MongoDB umožňuje použití značek ke směrování operace čtení. Ve službě Azure Cosmos DB pro rozhraní API MongoDB `region` značka je zahrnuté ve výchozím nastavení jako součást `isMaster` odpovědi:

```json
"tags": {
         "region": "West US"
      }
```

Proto můžete použít položky MongoClient `region` označit spolu s názvem oblast pro přesměrování operací čtení do konkrétních oblastí. Pro účty služby Azure Cosmos DB, názvy oblastí najdete na webu Azure portal na levé straně v části **Nastavení -> data repliky globálně**. Toto nastavení je užitečné pro dosažení **čtení izolace** -případů, ve které klientská aplikace chtít směrovat operace čtení jenom určité oblasti. Toto nastavení je ideální pro na produkční a analýzy zadejte scénáře, které běží na pozadí a nejsou produkční důležité služby.

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

V tomto článku jste zjistili, jak globálně distribuovat operace čtení použití předvoleb pro čtení s rozhraním API MongoDB služby Azure Cosmos DB.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci používat i nadále, odstraňte všechny prostředky vytvořené v tomto článku na webu Azure Portal následujícím postupem:

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku. 
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

* [Importování dat MongoDB do služby Azure Cosmos DB](mongodb-migrate.md)
* [Nastavení globálně replikovaného účtu služby Azure Cosmos DB a jeho použití s rozhraním API MongoDB](tutorial-global-distribution-mongodb.md)
* [Místní vývoj s využitím emulátoru](local-emulator.md)
