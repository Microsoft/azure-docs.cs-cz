---
title: Vyřazeno úrovně výkonu služby Azure Cosmos DB
description: Další informace o S1, S2 a S3 úrovní výkonu dané dříve k dispozici ve službě Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 06fa98ae4acc2252d8866858ed0e2194ed84ff79
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034382"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Po vyřazení úrovní výkonu S1, S2 a S3

> [!IMPORTANT] 
> Úrovní výkonu S1, S2 a S3 popisovaných v tomto článku se vyřazuje a nadále již nebudou k dispozici pro nové účty služby Azure Cosmos DB.
>

Tento článek poskytuje přehled úrovní výkonu S1, S2 a S3 a popisuje, jak kolekce, které používají tyto úrovně výkonu se dají migrovaných do jednoho dělené kolekce. Po přečtení tohoto článku, budete moci odpovědět na následující otázky:

- [Proč jsou úrovní výkonu S1, S2 a S3, se vyřazuje?](#why-retired)
- [Jak kolekce jednoho oddílu a dělených kolekcí srovnání na S1, S2, úrovní výkonu S3?](#compare)
- [Co je potřeba provést k zajištění nepřerušovaného přístupu k Moje data?](#uninterrupted-access)
- [Jak se po dokončení migrace změní mé kolekce?](#collection-change)
- [Jak bude Moje vyúčtování změnit poté, co jsem se migrovaly do kolekce jednoho oddílu?](#billing-change)
- [Co když budu potřebovat víc než 10 GB úložiště?](#more-storage-needed)
- [Můžu změnit mezi S1, S2 a S3 úrovně výkonu než plánované migrace?](#change-before)
- [Jak migrovat z S1, S2 nebo S3 úrovněmi výkonu pro kolekce jednoho oddílu na vlastní?](#migrate-diy)
- [Jak jsem vliv pokud jsem zákazník EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Proč jsou výkonu S1, S2 a S3 úrovně vyřazuje?

Úrovní výkonu S1, S2 a S3 flexibilitu, která poskytuje standardní nabídka služby Azure Cosmos DB nabízí. Kapacitu úložiště i propustnost s S1, S2 nebo S3 úrovně výkonu, byly předem nastavené a nenabízí elasticitu. Azure Cosmos DB teď nabízí možnost přizpůsobit si propustnost a úložiště, nabízí mnohem větší flexibilitu v vaši schopnost škálovat podle vašich aktuálních potřeb.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Jak kolekce jednoho oddílu a dělených kolekcí srovnání na S1, S2, úrovní výkonu S3?

Následující tabulka porovnává možnosti propustnost a úložiště, které jsou dostupné v kolekce jednoho oddílu, dělených kolekcí a S1, S2, úrovní výkonu S3. Tady je příklad pro oblast USA – východ 2:

|   |Dělené kolekce|Kolekce jednoho oddílu|S1|S2|S3|
|---|---|---|---|---|---|
|Maximální propustnost|Unlimited|10 tisíc RU/s|250 RU/s|1 tisíc RU/s|2.5 tisíc RU/s|
|Minimální propustnost|2.5 tisíc RU/s|400 RU/s|250 RU/s|1 tisíc RU/s|2.5 tisíc RU/s|
|Maximální velikost úložiště|Unlimited|10 GB|10 GB|10 GB|10 GB|
|Cena (měsíčně)|Propustnost: 6 USD / 100 RU/s<br><br>Storage: $ 0,25/GB služby|Propustnost: 6 USD / 100 RU/s<br><br>Storage: $ 0,25/GB služby|25 USD|50 USD|100 USD|

Jste zákazník EA? Pokud ano, přečtěte si téma [jak jsem jsem to vliv na jsem zákazník EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Co je potřeba provést k zajištění nepřerušovaného přístupu k Moje data?

Pokud máte kolekci S1, S2 nebo S3, měli byste migrovat kolekce do kolekce jednoho oddílu programově [pomocí sady .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Jak se po dokončení migrace změní mé kolekce?

Pokud máte kolekci S1, můžete je migrovat do kolekce jednoho oddílu s propustnost 400 RU/s. 400 RU/s je nejnižší propustnost kolekce jednoho oddílu k dispozici. Náklady na 400 RU/s v kolekce jednoho oddílu je však přibližně stejné jako byly kolekce S1 a 250 RU/s – platit tak nebudou platit za velmi 150 RU/s k dispozici.

Pokud máte kolekci S2, můžete je migrovat do kolekce jednoho oddílu s 1 tisíc RU/s. Zobrazí se žádné změny k úroveň propustnosti.

Pokud máte kolekci S3, můžete je migrovat do kolekce jednoho oddílu s 2,5 tisíc RU/s. Zobrazí se žádné změny k úroveň propustnosti.

V každém z těchto případů se po migraci kolekce, bude možné upravit úroveň propustnosti, nebo ji škálovat nahoru nebo dolů podle potřeby můžete svým uživatelům poskytnout přístup s nízkou latencí. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Jak bude Moje vyúčtování změnit poté, co mohu migrovat do kolekce jednoho oddílu?

Za předpokladu, že budete mít 10 kolekce S1, 1 GB úložiště pro každý v oblasti USA – východ a migrací těchto 10 kolekce S1 na 10 kolekce jednoho oddílu na 400 RU/s (minimální). Pokud uchováváte 10 kolekce jednoho oddílu pro celý měsíc, bude váš účet vypadat takto:

![Jak si S1 ceny pro 10 kolekce vede na 10 kolekce pomocí ceny za kolekce jednoho oddílu](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>Co když budu potřebovat víc než 10 GB úložiště?

Určuje, zda máte kolekci s úrovní výkonu S1, S2 nebo S3, nebo pro kolekci jeden oddíl, všechny z nich mít 10 GB úložiště, které jsou k dispozici, že můžete použít nástroj Azure Cosmos DB Data migrace pro migraci dat do dělené kolekce s prakticky neomezené úložiště. Informace o výhodách do dělené kolekce najdete v tématu [dělení a škálování ve službě Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Můžu změnit mezi S1, S2 a S3 úrovně výkonu než plánované migrace?

Pouze existující účty s výkonu S1, S2 a S3 je možné změnit a měnit úrovně úrovně výkonu prostřednictvím kódu programu [pomocí sady .NET SDK](#migrate-diy). Pokud se změní z S1 S3 a S3 do kolekce jednoho oddílu nelze vrátit úrovní výkonu S1, S2 nebo S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Jak migrovat z S1, S2 nebo S3 úrovněmi výkonu pro kolekce jednoho oddílu na vlastní?

Můžete migrovat z úrovní výkonu S1, S2 a S3 do kolekce jednoho oddílu programově [pomocí sady .NET SDK](#migrate-diy). Můžete to provést sami před plánovanou migraci těžit z možností flexibilní propustnost kolekce jednoho oddílu k dispozici.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrace do kolekce jednoho oddílu s použitím sady .NET SDK

Tato část popisuje pouze změny shromažďování výkonu pomocí na úrovni [SQL .NET API](sql-api-sdk-dotnet.md), ale proces se podobá našich dalších sad SDK.

Tady je fragment kódu pro změna propustnost kolekce na 5 000 jednotek žádostí za sekundu:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Navštivte [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) zobrazit další příklady a další informace o metodách naše nabídky:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Jak jsem vliv pokud jsem zákazník EA?

Cena chráněné až do konce jejich aktuálního smlouvy bude zákazníky se smlouvou EA.

## <a name="next-steps"></a>Další postup
Další informace o cenách a správě dat pomocí služby Azure Cosmos DB najdete v těchto zdrojích:

1.  [Dělení dat v databázi Cosmos DB](sql-api-partition-data.md). Pochopili rozdíl mezi jedním oddílem kontejneru a dělené kontejnery, jakož i tipy k implementaci strategie dělení k jednoduchému škálování.
2.  [Ceny služby cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Další informace o náklady na zřizování propustnosti a využívání úložiště.
3.  [Jednotky žádostí](request-units.md). Vysvětlení využití propustnosti pro typy jiné operace, například číst, zapisovat, dotaz.
