---
title: Zřizování propustnosti pro službu Azure Cosmos DB | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zřízená propustnost pro containsers, kolekce, grafů a tabulek Azure Cosmos DB.
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: andrl
ms.openlocfilehash: 2f6720e39856366e4bca387effdc2a0624d85826
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247990"
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Nastavení a zjištění propustnosti pro kontejnery služby Azure Cosmos DB a databázi

Propustnost můžete nastavit pro kontejner služby Azure Cosmos DB nebo sadu kontejnerů pomocí webu Azure portal nebo pomocí klientské sady SDK. Tento článek popisuje kroky potřebné ke konfiguraci propustnosti na různých členitostmi pro účet služby Azure Cosmos DB.

## <a name="provision-throughput-by-using-azure-portal"></a>Propustnost zřízení pomocí webu Azure portal

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Zřizování propustnosti pro kontejner (kolekci/graf a tabulka)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  
2. V levém navigačním podokně, vyberte **všechny prostředky** a vyhledejte svůj účet služby Azure Cosmos DB.  
3. Propustnost můžete nakonfigurovat při vytváření kontejneru (kolekce, graf, tabulka) nebo propustnost aktualizace pro existující kontejner.  
4. Chcete-li přiřadit propustnost při vytváření kontejneru, otevřete **Průzkumník dat** okna a vyberte **novou kolekci** (nový graf, novou tabulku pro jiná rozhraní API)  
5. Vyplňte formulář **přidat kolekci** okno. Pole v tomto okně jsou popsány v následující tabulce:  

   |**Nastavení**  |**Popis**  |
   |---------|---------|
   |ID databáze  |  Zadejte jedinečný název pro identifikaci vaší databáze. Databáze je logický kontejner jedné nebo více kolekcí. Názvy databází musí mít délku 1 až 255 znaků a nesmí obsahovat znaky /, \\, #, ? ani koncové mezery. |
   |ID kolekce  | Zadejte jedinečný název pro identifikaci vaší kolekce. ID kolekcí mají stejné požadavky na znaky jako názvy databází. |
   |Kapacita úložiště   | Tato hodnota představuje kapacitu úložiště databáze. Při zřizování propustnosti pro jednotlivé kolekce, kapacita úložiště může být **pevná (10 GB)** nebo **Unlimited**. Neomezenou kapacitou úložiště vyžaduje nastavení klíče oddílu pro vaše data.  |
   |Propustnost   | Každá kolekce a databáze může mít propustnost v jednotkách žádostí za sekundu.  Pro kapacitu dlouhodobého úložiště minimální propustnost je 400 jednotek žádostí za sekundu (RU/s), neomezené úložiště kapacity, minimální propustnost je nastavena na 1000 RU/s.|

6. Po zadání hodnoty pro tato pole, vyberte **OK** uložte nastavení.  

   ![Nastavení propustnosti pro kolekce](./media/set-throughput/set-throughput-for-container.png)

7. Pokud chcete aktualizovat propustnost pro existující kontejner, rozbalte databázi a kontejner a pak klikněte na tlačítko **nastavení**. V novém okně zadejte novou hodnotu propustnosti a pak vyberte **Uložit**.  

   ![Aktualizovat propustnost pro kolekce](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>Zřizování propustnosti pro skupinu kontejnerů nebo na úrovni databáze

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  
2. V levém navigačním podokně, vyberte **všechny prostředky** a vyhledejte svůj účet služby Azure Cosmos DB.  
3. Propustnost můžete nakonfigurovat při vytváření databáze nebo aktualizace propustnost pro existující databázi.  
4. Chcete-li přiřadit propustnost při vytváření databáze, otevřete **Průzkumník dat** okna a vyberte **nová databáze**  
5. Zadejte **id databáze** hodnotu, zaškrtněte **zřídit propustnost** řádku a nakonfigurovat hodnoty propustnosti.  

   ![Nastavte propustnost s novou možností databáze](./media/set-throughput/set-throughput-with-new-database-option.png)

6. Pokud chcete aktualizovat propustnost pro existující databázi, rozbalte databázi a kontejner a pak klikněte na tlačítko **škálování**. V novém okně zadejte novou hodnotu propustnosti a pak vyberte **Uložit**.  

   ![Aktualizovat propustnost pro databázi](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Zřídit propustnost pro sadu kontejnery stejně jako u jednotlivých kontejnerů v databázi

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  
2. V levém navigačním podokně, vyberte **všechny prostředky** a vyhledejte svůj účet služby Azure Cosmos DB.  
3. Vytvoření databáze a přiřadit propustnost. Otevřít **Průzkumník dat** okna a vyberte **nová databáze**  
4. Zadejte **id databáze** hodnotu, zaškrtněte **zřídit propustnost** řádku a nakonfigurovat hodnoty propustnosti.  

   ![Nastavte propustnost s novou možností databáze](./media/set-throughput/set-throughput-with-new-database-option.png)

5. Vedle vytváření kolekcí v databázi, kterou jste vytvořili v nad krok. Chcete-li vytvořit kolekci, klikněte pravým tlačítkem na databázi a vybrat **novou kolekci**.  

6. V **přidat kolekci** okně zadejte název kolekce a klíč oddílu. Volitelně můžete zřídit propustnost pro tento konkrétní kontejner, pokud se rozhodnete přiřadit hodnotu propustnosti, propustnost přiřazené do databáze se sdílí do kolekce.  

   ![Volitelně můžete nastavit propustnosti kontejneru](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Důležité informace o zřizování propustnosti

Tady jsou některé důležité informace, které vám pomůžou při rozhodování o strategii rezervované propustnosti.

### <a name="considerations-when-provisioning-throughput-at-the-database-level"></a>Důležité informace o zřizování propustnosti na úrovni databáze

Vezměte v úvahu zřizování propustnosti na úrovni databáze, (to znamená pro skupinu kontejnerů) v následujících případech:

* Pokud máte deseti nebo další počet kontejnerů, které by mohly sdílet propustnosti mezi některé nebo všechny z nich.  

* Když provádíte migraci z databáze jednoho tenanta, který je navržený pro spouštění na virtuální počítače hostované na IaaS nebo místní (pro příklad, NoSQL nebo relační databáze) ke službě Azure Cosmos DB a mít spousta kontejnerů.  

* Pokud chcete pomocí propustnosti ve fondu na úrovni databáze, vezměte v úvahu neplánované špiček v zatížení.  

* Namísto nastavení propustnosti na kontejner vás zajímá agregovanou propustnost mezi sadu kontejnerů v databázi.

### <a name="considerations-when-provisioning-throughput-at-the-container-level"></a>Důležité informace o zřizování propustnosti na úrovni kontejneru

Zvažte zřízení propustnost za kontejner v následujících případech:

* Pokud máte menší počet kontejnerů Azure Cosmos DB.  

* Pokud chcete získat garantovanou propustnost na daném kontejneru zajištěné smlouvou SLA.

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Nastavte propustnost s využitím rozhraní SQL API pro .NET

### <a name="set-throughput-at-the-container-level"></a>Nastavení propustnosti na úrovni kontejneru
Tady je fragment kódu pro vytvoření kontejneru s 3 000 jednotek žádostí za sekundu pro kontejner pomocí sady .NET SDK pro rozhraní SQL API:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

### <a name="set-throughput-for-a-set-of-containers-at-the-database-level"></a>Nastavte propustnost pro sadu kontejnerů na úrovni databáze

Tady je fragment kódu pro zřizování 100 000 jednotek požadavků za sekundu mezi sadu kontejnerů pomocí rozhraní SQL API .NET SDK:

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = await client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 });

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```

Azure Cosmos DB funguje na modelu rezervace pro propustnost. To znamená, bude se vám účtovat množství propustnost *vyhrazené*, bez ohledu na to, jak velká část, že propustnost je aktivně *použít*. Jako vaši aplikaci prvku změnou vzorů zatížení, dat a využití je možné snadno škálovat nahoru a dolů počet vyhrazené ru prostřednictvím sad SDK nebo pomocí [webu Azure Portal](https://portal.azure.com).

Každý kontejner nebo sadu kontejnerů, je namapována na `Offer` prostředků v Azure Cosmos DB, který má metadata o zřízenou propustnost. Můžete změnit přidělené propustnost pomocí vyhledávání odpovídající prostředek nabídky pro kontejner a pak aktualizuje s novou hodnotou propustnost. Tady je fragment kódu pro změnu propustnosti kontejneru 5 000 jednotek žádostí za druhé pomocí sady .NET SDK. Po změně propustnost, měli byste aktualizovat žádné stávající Azure portálu windows pro změněné propustnosti se zobrazí. 

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Neexistuje žádný vliv na dostupnost vašeho kontejneru, nebo sadu kontejnerů, když změníte propustnost. Obvykle novou vyhrazenou propustností platí za několik sekund na použití nové propustnost.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Nastavení propustnosti pomocí rozhraní SQL API pro Javu

Následující fragment kódu načte aktuální propustnost a změní ji na 500 RU/s. Kompletní kód ukázku najdete v tématu [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) soubor na Githubu. 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Zjištění propustnosti pomocí portálu metriky rozhraní MongoDB API

Nejjednodušší způsob, jak dobře odhadnete žádosti za jednotky pro vaše rozhraní API MongoDB databáze, je použít [webu Azure portal](https://portal.azure.com) metriky. S *počet požadavků, které* a *zátěž žádostí* grafy, můžete získat odhadovaný počet jednotek žádosti ze každé operace spotřebovává a kolik jednotek žádostí, které využívají relativně k mezi sebou.

![Portálu metriky rozhraní MongoDB API][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Překročení limitů vyhrazenou propustností, které v rozhraní MongoDB API
Aplikace, které překračují zřízená propustnost pro kontejner nebo sadu kontejnerů bude míra časově omezené, dokud spotřebu klesne pod zřízenou propustnost. Pokud dojde k omezení frekvence, back-endu skončí na požadavek `16500` kód chyby - `Too Many Requests`. Ve výchozím nastavení, rozhraní MongoDB API automaticky opakuje až 10krát teprve potom se informuje `Too Many Requests` kód chyby. Pokud se vám zobrazuje mnoho `Too Many Requests` kódy chyb, možná budete chtít zvážit buď přidání logiky opakování v rutiny pro zpracování chyb aplikace nebo [zřízenou propustnost pro kontejner](set-throughput.md).

## <a id="GetLastRequestStatistics"></a>Získat zátěž žádostí pomocí rozhraní MongoDB API GetLastRequestStatistics příkazu

Rozhraní MongoDB API podporuje vlastní příkaz *getLastRequestStatistics*, pro načtení poplatky požadavku pro danou operaci.

Například v prostředí Mongo spusťte operaci, kterou chcete ověřit žádost účtovat.
```
> db.sample.find()
```

V dalším kroku spusťte příkaz *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Jedním ze způsobů pro odhad množství vyhrazenou propustností, které jsou požadovány příslušnou aplikací je pro záznam poplatek za jednotky žádosti o souvisejícím se spouštěním typických operací proti reprezentativní položky, které používá vaše aplikace a pak odhadnout počet operace očekáváte, že pokud chcete provést každou sekundu.

> [!NOTE]
> Pokud máte typy položek, které se výrazně liší z hlediska velikosti a počtu indexované vlastnosti, potom si poznamenejte zátěž jednotky žádostí příslušné operace spojené s jednotlivými *typ* typické položky.
> 
> 

## <a name="throughput-faq"></a>Propustnost – nejčastější dotazy

**Může Moje propustnost nastavit na míň než 400 RU/s**

400 RU/s je k dispozici minimální propustnost na jeden oddíl kontejnery služby Cosmos DB (1 000 RU/s je minimum pro dělené kontejnery). Požadovat jednotky jsou nastaveny v intervalech po 100 RU/s, ale propustnost nelze nastavit na 100 RU/s nebo libovolná hodnota menší než 400 RU/s. Pokud hledáte nákladově efektivní metodu pro vývoj a testování služby Cosmos DB, můžete použít bezplatnou [emulátor služby Azure Cosmos DB](local-emulator.md), které můžete nasadit místně bez poplatků. 

**Nastavení propustnosti pomocí rozhraní MongoDB API**

Neexistuje žádné rozhraní MongoDB API služby rozšíření pro nastavení propustnosti. Doporučuje se používat rozhraní SQL API, jak je znázorněno v [nastavení propustnost s použitím rozhraní SQL API pro .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Další postup

* Další informace o odhadu propustnosti a jednotkách požadavků najdete v tématu [požadovat jednotky a odhadu propustnosti ve službě Azure Cosmos DB](request-units.md)

* Další informace o zřizování a průběžné globálním škálování s využitím služby Cosmos DB najdete v tématu [dělení a škálování pomocí služby Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
