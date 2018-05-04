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
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 0a53bb0a23fae386abbe71de944b073cbb93d502
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers"></a>Nastavování a získávání propustnost pro Azure Cosmos DB kontejnery

Propustnost lze nastavit pro Azure Cosmos DB kontejnerů na portálu Azure nebo pomocí sady SDK klienta. 

Následující tabulka uvádí k dispozici pro kontejnery propustnost:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Jeden oddíl kontejneru</strong></p></td>
            <td valign="top"><p><strong>Oddílů kontejneru</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Minimální propustnost</p></td>
            <td valign="top"><p>400 jednotek žádosti za sekundu</p></td>
            <td valign="top"><p>1 000 jednotek žádosti za sekundu</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maximální propustnost</p></td>
            <td valign="top"><p>10 000 jednotek žádosti za sekundu</p></td>
            <td valign="top"><p>Unlimited</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Chcete-li nastavit propustnost pomocí portálu Azure

1. V novém okně, otevřete [portál Azure](https://portal.azure.com).
2. Na levém panelu klikněte na tlačítko **Azure Cosmos DB**, nebo klikněte na tlačítko **všechny služby** v dolní části, posuňte se k **databáze**a pak klikněte na tlačítko **Azure Cosmos DB**.
3. Vyberte svůj účet Cosmos DB.
4. V novém okně klikněte na **Průzkumníku dat** v navigační nabídce.
5. V novém okně rozbalte databázi a kontejneru a pak klikněte na tlačítko **škálování & nastavení**.
6. V novém okně zadejte novou hodnotu propustnost v **propustnost** pole a pak klikněte na **Uložit**.

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-net"></a>Chcete-li nastavit propustnost pomocí rozhraní SQL API pro .NET

Následující fragment kódu načte aktuální propustnost a změní na 500 RU/s. Úplný příklad najdete v článku [CollectionManagement](https://github.com/Azure/azure-documentdb-dotnet/blob/95521ff51ade486bb899d6913880995beaff58ce/samples/code-samples/CollectionManagement/Program.cs#L188-L216) projektu na Githubu.

```csharp
// Fetch the offer of the collection whose throughput needs to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 500 request units per second
offer = new OfferV2(offer, 500);

// Now persist these changes to the collection by replacing the original offer resource
await client.ReplaceOfferAsync(offer);
```

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Chcete-li nastavit propustnost pomocí rozhraní API SQL pro jazyk Java

Následující fragment kódu načte aktuální propustnost a změní na 500 RU/s. Úplný příklad, najdete v článku [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) souboru na Githubu. 

```Java
// find offer associated with this collection
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

Například v prostředí Mongo provést operaci chcete ověřit žádost zdarma pro.
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

Myslete na to, jedné metody odhadnout velikost vyhrazenou propustností požadované aplikací je záznam zřizování jednotky žádosti přidružené spuštěným typických operací pro položku reprezentativní používá vaše aplikace a pak odhadnout počet operací, které předpokládáte provést každou sekundu.

> [!NOTE]
> Pokud máte typy položek, které se výrazně liší z hlediska velikosti a počtu indexované vlastnosti, potom si poznamenejte poplatků jednotek žádosti příslušné operace spojené s každou *typ* typické položky.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Získat propustnost pomocí portálu metriky rozhraní API MongoDB

Nejjednodušší způsob, jak získat dobrý odhad požadavku poplatky jednotky pro vaši databázi MongoDB rozhraní API je použití [portál Azure](https://portal.azure.com) metriky. S *počet požadavků, které* a *požadavek poplatků* grafy, můžete získat odhad kolik jednotek žádosti každý je náročné operace a kolik jednotek žádosti, které budou využívat relativně k jinému.

![Rozhraní API MongoDB portálu metriky][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Překročení omezení vyhrazenou propustností v rozhraní API MongoDB
Aplikace, které překračují zřízená propustnost pro kontejner bude míra limited, dokud spotřebu klesne pod zřízená propustnost. Když dojde k omezení míry, back-end se ukončí ho preventivně požadavek s `16500` kód chyby - `Too Many Requests`. Ve výchozím nastavení, rozhraní API MongoDB automaticky opakovat až 10krát před vrácením `Too Many Requests` kód chyby. Pokud se zobrazuje řada `Too Many Requests` kódy chyb, možná budete chtít buď přidejte logiku opakovaných pokusů v zpracování rutiny chyb aplikace nebo [zvýšit zřízené propustnosti pro kontejner](set-throughput.md).

## <a name="throughput-faq"></a>Propustnost – nejčastější dotazy

**Můžete nastavit Moje propustnosti na méně než 400 RU/s**

400 RU/s je k dispozici minimální propustnost kontejnerům Cosmos DB tvořené jedním oddílem (je 1000 RU/s minimum pro kontejnery oddílů). Žádost jednotky jsou nastaveny v intervalech 100 RU/s, ale propustnost nelze nastavit na 100 RU/s nebo jakoukoli hodnotu menší než 400 RU/s. Pokud hledáte nákladově efektivní metodu pro vývoj a testování Cosmos DB, můžete použít bezplatnou [emulátoru DB Cosmos Azure](local-emulator.md), které můžete nasadit místně bez nákladů. 

**Jak lze nastavit pomocí rozhraní API MongoDB througput?**

Nedojde k rozšíření rozhraní API MongoDB nastavit propustnost. Doporučuje se používat rozhraní SQL API, jak je znázorněno v [nastavení propustnost pomocí rozhraní SQL API pro .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Další postup

Další informace o zřizování a probíhající planetu měřítku s Cosmos DB, najdete v části [dělení a škálování s Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png