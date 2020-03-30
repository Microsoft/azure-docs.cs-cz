---
title: Vyřazené úrovně výkonu Azure Cosmos DB
description: Další informace o úrovních výkonu S1, S2 a S3, které byly dříve dostupné v Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 40735f91e2ca58cc42f723c7993686d92f0e5ff0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623332"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Vyřazení úrovní výkonu S1, S2 a S3 z provozu

> [!IMPORTANT] 
> Úrovně výkonu S1, S2 a S3 popsané v tomto článku jsou vyřazeny a už nejsou k dispozici pro nové účty Azure Cosmos DB.
>

Tento článek obsahuje přehled úrovní výkonu S1, S2 a S3 a popisuje, jak lze migrovat kolekce, které používají tyto úrovně výkonu, do kolekcí s jedním oddílem. Po přečtení tohoto článku budete moci odpovědět na následující otázky:

- [Proč jsou úrovně výkonu S1, S2 a S3 vyřazeny?](#why-retired)
- [Jak kolekce jednoho oddílu a rozdělené kolekce porovnat s Úrovně výkonu S1, S2, S3?](#compare)
- [Co musím udělat, abych zajistil nepřetržitý přístup ke svým datům?](#uninterrupted-access)
- [Jak se moje kolekce změní po migraci?](#collection-change)
- [Jak se změní moje fakturace po migraci do kolekcí jednoho oddílu?](#billing-change)
- [Co když potřebuji více než 20 GB úložného prostoru?](#more-storage-needed)
- [Lze před plánovanou migrací přepínat mezi úrovněmi výkonu S1, S2 a S3?](#change-before)
- [Jak mohu migrovat z úrovně výkonu S1, S2, S3 na kolekce jednoho oddílu na vlastní pěst?](#migrate-diy)
- [Jak se na mě mohou zamítnout, když jsem zákazníkem EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Proč jsou úrovně výkonu S1, S2 a S3 vyřazeny?

Úrovně výkonu S1, S2 a S3 nenabízejí flexibilitu, kterou poskytuje standardní nabídka Azure Cosmos DB. S úrovněmi výkonu S1, S2, S3 byla přednastavena propustnost i kapacita úložiště a nenabízela pružnost. Azure Cosmos DB teď nabízí možnost přizpůsobit propustnost a úložiště a nabízí mnohem větší flexibilitu ve schopnosti škálovat podle toho, jak se mění vaše potřeby.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Jak kolekce jednoho oddílu a rozdělené kolekce porovnat s Úrovně výkonu S1, S2, S3?

Následující tabulka porovnává propustnost a možnosti úložiště, které jsou k dispozici v kolekcích jednoho oddílu, rozdělených kolekcí a Úrovně výkonu S1, S2, S3. Zde je příklad pro oblast US East 2:

|   |Dělená kolekce|Kolekce jednoho oddílu|S1|S2|S3|
|---|---|---|---|---|---|
|Maximální propustnost|Unlimited|10K RU/s|250 ŽP/s|1 K Ru/s|2.5 K Ru/s|
|Minimální propustnost|2.5 K Ru/s|400 RU/s|250 ŽP/s|1 K Ru/s|2.5 K Ru/s|
|Maximální úložiště|Unlimited|20 GB|20 GB|20 GB|20 GB|
|Cena (měsíčně)|Propustnost: $6 / 100 RU/s<br><br>Úložiště: $0.25/GB|Propustnost: $6 / 100 RU/s<br><br>Úložiště: $0.25/GB|25 USD|50 USD|100 USD|

Jste zákazníkem EA? Pokud ano, přečtěte si, [jak mě to ovlivnilo, pokud jsem zákazníkem EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Co musím udělat, abych zajistil nepřetržitý přístup ke svým datům?

Pokud máte kolekci S1, S2 nebo S3, měli byste přesunout kolekci do kolekce jednoho oddílu programově [pomocí sady .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Jak se moje kolekce změní po migraci?

Pokud máte kolekci S1, můžete je migrovat do kolekce jednoho oddílu s propustností 400 RU/s. 400 RU/s je nejnižší propustnost k dispozici s kolekcemi jednoho oddílu. Náklady na 400 RU/s v kolekci jednoho oddílu jsou však přibližně stejné, jako jste platili svou sbírkou S1 a 250 RU/s – takže neplatíte za dalších 150 RU/s, které máte k dispozici.

Pokud máte kolekci S2, můžete migrovat do jedné kolekce oddílů s 1 K RU/s. Neuvidíte žádnou změnu úrovně propustnost.

Pokud máte kolekci S3, můžete migrovat do jedné kolekce oddílů s 2.5 K RU/s. Neuvidíte žádnou změnu úrovně propustnost.

V každém z těchto případů po migraci kolekce, budete moci přizpůsobit úroveň propustnosti nebo škálování nahoru a dolů podle potřeby poskytnout přístup s nízkou latencí pro vaše uživatele. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Jak se změní moje fakturace po migraci do kolekce jednoho oddílu?

Za předpokladu, že máte 10 kolekcí S1, 1 GB úložiště pro každou z nich v oblasti USA – východ a tyto kolekce 10 S1 migrujete do 10 kolekcí jednoho oddílu na 400 RU/s (minimální úroveň). Váš účet bude vypadat takto, pokud budete mít 10 kolekce jednoho oddílu po celý měsíc:

![Jak S1 ceny pro 10 kolekcí v porovnání s 10 kolekce pomocí cen pro jednu kolekci oddílů](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>Co když potřebuji více než 20 GB úložného prostoru?

Ať už máte kolekci s úrovní výkonu S1, S2 nebo S3 nebo máte kolekci jednoho oddílu, z nichž všechny mají k dispozici úložiště 20 GB, můžete použít nástroj migrace dat Azure Cosmos DB k migraci dat do rozdělené kolekce s prakticky neomezené úložiště. Informace o výhodách rozdělené kolekce najdete [v tématu dělení a škálování v Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Lze před plánovanou migrací přepínat mezi úrovněmi výkonu S1, S2 a S3?

Pomocí sady [.NET SDK](#migrate-diy)lze změnit pouze existující účty s výkonem S1, S2 a S3 a programově měnit úrovně výkonu . Pokud změníte z S1, S3 nebo S3 na kolekci jednoho oddílu, nemůžete se vrátit na úrovně výkonu S1, S2 nebo S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Jak mohu migrovat z úrovně výkonu S1, S2, S3 na kolekce jednoho oddílu na vlastní pěst?

Pomocí sady .NET SDK můžete programově migrovat z úrovní výkonu S1, S2 a S3 do kolekcí jednoho oddílu pomocí sady [.NET SDK](#migrate-diy). Můžete to provést sami před plánovanou migrací, abyste mohli využívat možnosti flexibilní propustnost, které jsou k dispozici s kolekcemi jednoho oddílu.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrace do kolekcí jednoho oddílu pomocí sady .NET SDK

Tato část se týká pouze změny úrovně výkonu kolekce pomocí [rozhraní SQL .NET API](sql-api-sdk-dotnet.md), ale proces je podobný pro naše ostatní sady SDK.

Zde je fragment kódu pro změnu propustnost kolekce na 5 000 jednotek požadavku za sekundu:
    
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

Navštivte [msdn](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) zobrazit další příklady a dozvědět se více o našich metodách nabídky:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**NahraditOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Jak se na mě mohou zamítnout, když jsem zákazníkem EA?

Zákazníci EA budou chráněni cenou až do konce své stávající smlouvy.

## <a name="next-steps"></a>Další kroky
Další informace o cenách a správě dat pomocí Azure Cosmos DB najdete v těchto zdrojích informací:

1.  [Dělení dat v Cosmos DB](sql-api-partition-data.md). Pochopit rozdíl mezi kontejner u jednoho oddílu a dělené kontejnery, stejně jako tipy na implementaci strategie dělení bez problémů.
2.  [Ceny Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Přečtěte si o nákladech na zřizování propustností a využívání úložiště.
3.  [Jednotky požadavků](request-units.md). Pochopit spotřebu propustnost pro různé typy operací, například čtení, zápis, dotaz.
