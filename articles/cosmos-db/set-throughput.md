---
title: Zřízení propustnost pro Azure Cosmos DB | Microsoft Docs
description: Zjistěte, jak nastavit zřízená propustnost pro containsers, kolekce, grafy a tabulky Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sngun
ms.openlocfilehash: 925167c6b4a7f173726ec094c2847a16ca3d0ef4
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Nastavování a získávání propustnost pro Azure Cosmos DB kontejnery a databáze

Propustnost můžete nastavit pro kontejner Azure Cosmos DB nebo sadu kontejnerů pomocí portálu Azure nebo pomocí sady SDK klienta. Při zřizování propustnost pro sadu kontejnery těchto kontejnerech sdílet zřízené propustnosti. Zřizování propustnost pro jednotlivé kontejnery bude zaručit rezervace propustnost pro tento konkrétní kontejner. Na druhé straně zřizování propustnost pro databázi můžete sdílet propustnost mezi všechny kontejnery, které patří do dané databáze. V databázi Azure Cosmos DB může mít sadu kontejnery, které sdílejí propustnost a také kontejnery, které mají vyhrazené propustnost. 

Podle zřízené propustnosti, bude Azure Cosmos DB přidělit fyzické oddíly ho s růstem hostovat vaše kontejnery a rozdělení nebo rebalances data napříč oddíly.

Při přiřazování RU za sekundu na úrovni jednotlivých kontejneru, kontejnery se dá vytvořit jako *pevné* nebo *neomezená*. Kontejnery s pevnou velikostí mají omezení maximální velikosti 10 GB a propustnosti 10 000 RU/s. Pokud chcete vytvořit kontejner neomezená, musíte zadat minimální propustnost 1000 RU/s a [klíč oddílu](partition-data.md). Vzhledem k tomu, aby se daly rozdělit mezi více oddílů mohou mít vaše data, je nutné vybrat klíč oddílu, který má vysokou kardinalitou (100 na miliony odlišné hodnoty). Výběrem klíč oddílu s mnoha jedinečných hodnot je zajistit, že kontejner a tabulka/grafika a žádosti o je možné rozšířit jednotně pomocí Azure Cosmos DB. 

Při přiřazování RU za sekundu napříč sadu kontejnery, kontejnery, které patří do této skupiny jsou považovány za *neomezená* kontejnery a musíte zadat klíč oddílu.

![Zřizování jednotek žádosti pro jednotlivé kontejnery a sadu kontejnery](./media/request-units/provisioning_set_containers.png)

Tento článek vás provede kroky potřebné ke konfiguraci propustnosti na různých úrovních pro účet Azure Cosmos DB. 

## <a name="provision-throughput-by-using-azure-portal"></a>Propustnost zřídit pomocí portálu Azure

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Zřízení propustnost pro kontejner (kolekce nebo grafu nebo tabulky)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  
2. Levé navigaci, vyberte **všechny prostředky** a najít váš účet Azure Cosmos DB.  
3. Můžete nakonfigurovat propustnost při vytváření kontejneru (kolekce, graf, tabulku) nebo propustnost aktualizace pro existující kontejner.  
4. Chcete-li přiřadit propustnost při vytváření kontejneru, otevřete **Průzkumníku dat** a vyberte **nové kolekce** (nový graf, nové tabulky pro jiná rozhraní API)  
5. Vyplňte formulář **přidat kolekce** okno. Pole v tomto okně jsou popsané v následující tabulce:  

   |**Nastavení**  |**Popis**  |
   |---------|---------|
   |ID databáze  |  Zadejte jedinečný název pro identifikaci vaší databáze. Databáze je logický kontejner jednu nebo více kolekcí. Názvy databází musí mít délku 1 až 255 znaků a nesmí obsahovat znaky /, \\, #, ? ani koncové mezery. |
   |ID kolekce  | Zadejte jedinečný název pro identifikaci vaší kolekce. ID kolekcí mají stejné požadavky na znaky jako názvy databází. |
   |Kapacita úložiště   | Tato hodnota představuje kapacitu úložiště databáze. Při zřizování propustnost pro jednotlivé kolekce, kapacity úložiště může být **Fixed (10 GB)** nebo **neomezený**. Neomezené úložiště kapacity vyžaduje, abyste nastavit klíč oddílu pro vaše data.  |
   |Propustnost   | Každý kolekce a databáze může mít propustnost v jednotek žádosti za sekundu.  Kapacitu dlouhodobého úložiště minimální propustnost je 400 jednotek žádosti za sekundu (RU/s), neomezené úložiště kapacity minimální propustnost je nastavena na 1000 RU/s.|

6. Po zadání hodnoty těchto polí, vyberte **OK** uložte nastavení.  

   ![Sada propustnost pro kolekce](./media/set-throughput/set-throughput-for-container.png)

7. Pokud chcete aktualizovat propustnost pro existující kontejner, rozbalte databázi a kontejneru a pak klikněte na **nastavení**. V novém okně zadejte novou hodnotu propustnosti a pak vyberte **Uložit**.  

   ![Aktualizace propustnost pro kolekce](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>Zřízení propustnost pro sadu kontejnerů nebo na úrovni databáze

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  
2. Levé navigaci, vyberte **všechny prostředky** a najít váš účet Azure Cosmos DB.  
3. Můžete nakonfigurovat propustnost při vytváření databáze nebo aktualizace propustnost pro existující databázi.  
4. Chcete-li přiřadit propustnost při vytváření databáze, otevřete **Průzkumníku dat** a vyberte **novou databázi**  
5. Vyplnění **id databáze** hodnotu, zkontrolujte **zřídit propustnost** řádku a nakonfigurovat hodnotu propustnosti. Databáze může být zřízen hodnotou minimální propustnost 50 000 RU/s.  

   ![Nastavit propustnost s novou možnost databáze](./media/set-throughput/set-throughput-with-new-database-option.png)

6. Pokud chcete aktualizovat propustnost pro existující databázi, rozbalte databázi a kontejneru a pak klikněte na **škálování**. V novém okně zadejte novou hodnotu propustnosti a pak vyberte **Uložit**.  

   ![Propustnost aktualizací pro databázi](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Zřízení propustnost pro sadu kontejnery stejně jako u jednotlivých kontejner v databázi

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  
2. Levé navigaci, vyberte **všechny prostředky** a najít váš účet Azure Cosmos DB.  
3. Vytvoření databáze a přiřadit propustnost. Otevřete **Průzkumníku dat** a vyberte **novou databázi**  
4. Vyplnění **id databáze** hodnotu, zkontrolujte **zřídit propustnost** řádku a nakonfigurovat hodnotu propustnosti. Databáze může být zřízen hodnotou minimální propustnost 50 000 RU/s.  

   ![Nastavit propustnost s novou možnost databáze](./media/set-throughput/set-throughput-with-new-database-option.png)

5. Dále vytvořte kolekci v databázi, kterou jste vytvořili v výše krok. Pokud chcete vytvořit kolekci, klikněte pravým tlačítkem na databázi a vyberte **nové kolekce**.  

6. V **přidat kolekce** okno, zadejte název kolekce a klíč oddílu. Volitelně můžete zřídit propustnost pro tento konkrétní kontejner, pokud se rozhodnete přiřadit hodnotu propustnosti, propustnost přiřazené k databázi se sdílí do kolekce.  

   ![Volitelně můžete nastavit propustnost kontejneru](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Informace týkající se zřizování propustnost

Tady jsou některé aspekty, které vám pomohou rozhodnout, strategie rezervace propustnost.

Vezměte v úvahu zřizování propustnosti na úrovni databáze, (který je pro sadu kontejnery) v následujících případech:

* Pokud máte tucet nebo více počet kontejnerů, které by mohly sdílet propustnost v rámci některé nebo všechny z nich.  

* Když provádíte migraci z jednoho klienta databáze, která je určená pro spuštění na IaaS hostované virtuální počítače nebo na místní (pro příklad, NoSQL nebo relačních databází) k databázi Cosmos Azure a mají mnoho kontejnerů.  

* Pokud budete chtít zvážit neplánované špičky v úlohách pomocí ve fondu propustnosti na úrovni databáze.  

* Místo nastavení propustnosti na jednotlivé kontejneru vás zajímá při získávání agregovanou propustnost mezi sadu kontejnery v databázi.

Vezměte v úvahu zřizování propustnost na kontejner jednotlivých v následujících případech:

* Pokud máte menší počet kontejnerů Azure Cosmos DB.  

* Pokud chcete získat zaručenou propustnost do daného kontejneru zajišťoval SLA.

## <a name="throughput-ranges"></a>Propustnost rozsahů

Následující tabulka uvádí k dispozici pro kontejnery propustnost:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Jeden oddíl kontejneru</strong></p></td>
            <td valign="top"><p><strong>Oddílů kontejneru</strong></p></td>
            <td valign="top"><p><strong>Sadu kontejnery</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Minimální propustnost</p></td>
            <td valign="top"><p>400 jednotek žádosti za sekundu</p></td>
            <td valign="top"><p>1 000 jednotek žádosti za sekundu</p></td>
            <td valign="top"><p>50 000 jednotek žádosti za sekundu</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maximální propustnost</p></td>
            <td valign="top"><p>10 000 jednotek žádosti za sekundu</p></td>
            <td valign="top"><p>Unlimited</p></td>
            <td valign="top"><p>Unlimited</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Nastavit propustnost pomocí rozhraní API pro SQL pro .NET

Zde je fragment kódu pro vytvoření kontejneru s 3 000 jednotek žádosti za sekundu pro kontejner jednotlivých pomocí sady .NET SDK rozhraní SQL API:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Zde je fragment kódu pro zřizování 100 000 žádostí jednotek za sekundu napříč sadu kontejnery pomocí rozhraní SQL API .NET SDK:

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 }).Result;

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

Azure Cosmos DB funguje ve model rezervace propustnost. To znamená, že se účtují pro množství propustnost *vyhrazené*, bez ohledu na to, kolik z této propustnost je aktivně *používá*. Jako vaše aplikace je zatížení, data a využití vzory změnu, je možné snadno škálovat nahoru a dolů počet vyhrazené RUs prostřednictvím sady SDK nebo pomocí [portálu Azure](https://portal.azure.com).

Každý kontejner, nebo sadu kontejnery, je namapovaný na `Offer` prostředků v Azure DB Cosmos, který má metadata o zřízené propustnosti. Vyhledávání odpovídající prostředek nabídka pro kontejner a poté aktualizace pomocí novou hodnotu propustnosti, můžete změnit přidělené propustnost. Zde je fragment kódu pro změnu propustnost kontejner do 5 000 jednotek žádosti za druhé pomocí sady .NET SDK:

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

Neexistuje žádný vliv na dostupnost vaší kontejneru nebo sadu kontejnery, když změníte propustnost. Nové vyhrazenou propustností je obvykle efektivní během několika sekund na použití nové propustnost.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Chcete-li nastavit propustnost pomocí rozhraní API SQL pro jazyk Java

Následující fragment kódu načte aktuální propustnost a změní na 500 RU/s. Úplný příklad, najdete v článku [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) souboru na Githubu. 

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

## <a id="GetLastRequestStatistics"></a>Získat propustnost pomocí příkazu GetLastRequestStatistics MongoDB API

Rozhraní API MongoDB podporuje vlastního příkazu *getLastRequestStatistics*, pro načítání poplatky požadavku pro danou operaci.

Například v prostředí Mongo provést operaci, kterou chcete ověřit žádost zdarma pro.
```
> db.sample.find()
```

Potom spusťte příkaz *getLastRequestStatistics*.
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

Jednou z možností k odhadování množství vyhrazenou propustností požadované aplikací je záznam zřizování jednotky žádosti přidružené spuštěným typických operací pro položku reprezentativní používá vaše aplikace a pak odhad počtu operace předpokládáte provést každou sekundu.

> [!NOTE]
> Pokud máte typy položek, které se výrazně liší z hlediska velikosti a počtu indexované vlastnosti, potom si poznamenejte poplatků jednotek žádosti příslušné operace spojené s každou *typ* typické položky.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Získat propustnost pomocí portálu metriky rozhraní API MongoDB

Nejjednodušší způsob, jak získat dobrý odhad požadavku poplatky jednotky pro vaši databázi MongoDB rozhraní API je použití [portál Azure](https://portal.azure.com) metriky. S *počet požadavků, které* a *požadavek poplatků* grafy, můžete získat odhad kolik jednotek žádosti každý je náročné operace a kolik jednotek žádosti, které budou využívat relativně k jinému.

![Rozhraní API MongoDB portálu metriky][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Překročení omezení vyhrazenou propustností v rozhraní API MongoDB
Aplikace, které překračují zřízená propustnost pro kontejner nebo sadu kontejnery bude míra limited, dokud spotřebu klesne pod zřízená propustnost. Když dojde k omezení míry, back-end se ukončí požadavek s `16500` kód chyby - `Too Many Requests`. Ve výchozím nastavení, rozhraní API MongoDB automaticky opakovat až 10krát před vrácením `Too Many Requests` kód chyby. Pokud se zobrazuje řada `Too Many Requests` kódy chyb, možná budete chtít buď přidejte logiku opakovaných pokusů v zpracování rutiny chyb aplikace nebo [zvýšit zřízené propustnosti pro kontejner](set-throughput.md).

## <a name="throughput-faq"></a>Propustnost – nejčastější dotazy

**Můžete nastavit Moje propustnosti na méně než 400 RU/s**

400 RU/s je k dispozici minimální propustnost kontejnerům Cosmos DB tvořené jedním oddílem (je 1000 RU/s minimum pro kontejnery oddílů). Žádost jednotky jsou nastaveny v intervalech 100 RU/s, ale propustnost nelze nastavit na 100 RU/s nebo jakoukoli hodnotu menší než 400 RU/s. Pokud hledáte nákladově efektivní metodu pro vývoj a testování Cosmos DB, můžete použít bezplatnou [emulátoru DB Cosmos Azure](local-emulator.md), které můžete nasadit místně bez nákladů. 

**Jak lze nastavit pomocí rozhraní API MongoDB propustnost?**

Nedojde k rozšíření rozhraní API MongoDB nastavit propustnost. Doporučuje se používat rozhraní SQL API, jak je znázorněno v [nastavení propustnost pomocí rozhraní SQL API pro .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Další postup

* Další informace o odhad jednotky propustnosti a požadavku najdete v tématu [požadavku jednotky & odhadnout propustnost v Azure Cosmos DB](request-units.md)

* Další informace o zřizování a probíhající planetu měřítku s Cosmos DB, najdete v části [dělení a škálování s Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
