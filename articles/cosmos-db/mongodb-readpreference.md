---
title: Pomocí Azure Cosmos DB MongoDB rozhraní API pro MongoDB čtení předvoleb | Microsoft Docs
description: Další informace o použití předvoleb MongoDB pro čtení pomocí rozhraní API služby Azure DB Cosmos MongoDB
services: cosmos-db
documentationcenter: ''
author: vidhoonv
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: ''
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 02/26/2018
ms.author: viviswan
ms.openlocfilehash: cc9f22bc5e7b54ed03fbf83b58dda0aff0114737
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-globally-distribute-reads-using-read-preference-with-the-azure-cosmos-db-mongodb-api"></a>Jak se bude distribuovat globálně čtení pomocí rozhraní API služby Azure DB Cosmos MongoDB předvoleb pro čtení 

Tento článek ukazuje, jak se bude globálně distribuovat pomocí operací čtení [MongoDB čtení předvoleb](https://docs.mongodb.com/manual/core/read-preference/) nastavení s rozhraním API pro databázi Azure Cosmos MongoDB. 

## <a name="prerequisites"></a>Požadavky 
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Pokus [rychlý Start](tutorial-global-distribution-mongodb.md) článku pokyny, jak nastavit účet Azure Cosmos DB s globální distribuční a připojte se pomocí rozhraní API MongoDB pomocí portálu Azure.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Otevřete okno terminálu Git, jako je třeba Git Bash, a pomocí `cd` přejděte do pracovního adresáře.  

Spuštěním následujících příkazů naklonujte ukázkové úložiště. Podle vaší platformy, které vás zajímají, použijte jednu z následující ukázka úložiště:

1. [Ukázková aplikace .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Ukázková aplikace NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose ukázkové aplikace](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Ukázková aplikace Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot ukázkové aplikace](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Spuštění aplikace

V závislosti na platformě použít nainstalujte požadované balíčky a spusťte aplikaci. Chcete-li nainstalovat závislosti, postupujte podle v souboru README součástí úložiště ukázkové aplikace. Například v NodeJS ukázkovou aplikaci, použijte následující příkazy pro instalaci požadovaných balíčků a spusťte aplikaci.

```bash
cd mean
npm install
node index.js
```
Aplikace se pokusí připojit ke zdroji MongoDB a selže, protože připojovací řetězec je neplatný. Postupujte podle kroků v souboru README aktualizovat připojovací řetězec `url`. Také aktualizovat `readFromRegion` čtení oblasti v účtu Azure Cosmos DB. Následující pokyny jsou z ukázkové NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos DB account's values. 
```

Po následujícím postupem, ukázkové aplikace běží a vytvoří následující výstup:

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

## <a name="read-using-read-preference-mode"></a>Přečtěte si pomocí režimu pro čtení předvoleb

MongoDB nabízí následující režimy předvoleb pro čtení pro klienty použít:

1. PRIMÁRNÍ
2. PRIMARY_PREFERRED
3. SEKUNDÁRNÍ
4. SECONDARY_PREFERRED
5. NEJBLIŽŠÍ

Najdete v podrobné [MongoDB čtení předvoleb chování](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) dokumentace podrobnosti o chování každé z nich přečíst předvoleb režimy. V Azure DB Cosmos mapuje primární oblasti zápisu a sekundární mapy do oblasti pro čtení.

Podle toho, běžné scénáře, doporučujeme použít následující nastavení:

1. Pokud **čte s nízkou latencí** jsou potřeba, použijte **NEAREST** předvoleb režimu pro čtení. Toto nastavení nasměruje operací čtení na nejbližší dostupné oblasti. Všimněte si, že pokud nejbližší oblast je oblast zápisu, pak tyto operace jsou směrované na danou oblast.
2. Pokud **vysoké dostupnosti a geografická distribuci čtení** jsou požadovány (latence není omezení), pak použít **sekundární upřednostňovaný** předvoleb režimu pro čtení. Toto nastavení nasměruje operace čtení do oblasti k dispozici pro čtení. Pokud je k dispozici žádné oblasti pro čtení, jsou směrovány požadavky pro oblast zápisu.

Následující fragment kódu z ukázkové aplikace ukazuje, jak nakonfigurovat NEJBLIŽŠÍ předvoleb pro čtení v NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Podobně následující fragment kódu ukazuje, jak nakonfigurovat předvoleb pro čtení SECONDARY_PREFERRED v NodeJS:

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

Kromě režimu pro čtení předvoleb MongoDB umožňuje použití značek k přímé operace čtení. V Azure DB Cosmos pro MongoDB API `region` značky je zahrnutá ve výchozím nastavení jako součást `isMaster` odpovědi:

```json
"tags": {
         "region": "West US"
      }
```

Proto můžete použít MongoClient `region` značky společně s název oblasti pro přesměrování operace čtení do určitých oblastí. Pro účty Azure Cosmos DB oblast názvy naleznete na portálu Azure na levé straně v části **Nastavení -> data repliky globálně**. Toto nastavení je užitečné k dosažení **číst izolace** -případy, ve které klientskou aplikaci chcete přímé operace čtení pouze v určité oblasti. Toto nastavení je ideální pro na produkční/analytics zadejte scénáře, které běží na pozadí a nejsou produkční důležité služby.

Následující fragment kódu z ukázkové aplikace ukazuje, jak nakonfigurovat značky v NodeJS předvoleb pro čtení:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Odkazovat na odpovídající úložiště ukázkové aplikace pro jiné platformy, jako například [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) a [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

V tomto článku když jste se naučili globálně distribuci operace čtení předvoleb pro čtení pomocí rozhraní API Azure Cosmos DB MongoDB.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nechcete pokračovat v používání této aplikace, odstraňte všechny prostředky, které jsou vytvořené v tomto článku na portálu Azure pomocí následujících kroků:

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku. 
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

* [Importování dat MongoDB do služby Azure Cosmos DB](mongodb-migrate.md)
* [Nastavení globálně replikované Azure Cosmos DB účtu a jeho použití s MongoDB rozhraní API](tutorial-global-distribution-mongodb.md)
* [Místní vývoj s využitím emulátoru](local-emulator.md)
