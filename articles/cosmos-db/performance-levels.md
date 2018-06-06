---
title: Vyřazeno úrovně výkonu Azure Cosmos DB | Microsoft Docs
description: Další informace o S1, S2 a S3 úrovně výkonu dříve k dispozici v Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d1bb7551e6dfb6c42853ab95096f17f5285c69c1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796644"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Vyřazení úrovní výkonu S1, S2 a S3

> [!IMPORTANT] 
> Úrovně výkonu S1, S2 a S3 popsané v tomto článku se postupně vyřazuje z provozu a nadále již nebudou k dispozici pro nové účty Azure Cosmos DB.
>

Tento článek obsahuje přehled úrovní výkonu S1, S2 a S3 a popisuje, jak kolekcí, které používají tyto úrovně výkonu lze do jednoho migrována dělené kolekce. Po přečtení tohoto článku, budete moct odpovězte si na následující otázky:

- [Proč se úrovní výkonu S1, S2 a S3 se postupně vyřazuje z provozu?](#why-retired)
- [Jak kolekce tvořené jedním oddílem a dělené kolekce srovnání na S1, S2, úrovně výkonu S3?](#compare)
- [Co je třeba provést k zajištění nepřetržitého přístupu k mým datům?](#uninterrupted-access)
- [Jak se po dokončení migrace změní mé kolekce?](#collection-change)
- [Jak bude Moje fakturace změnit po I se migrovat do kolekce tvořené jedním oddílem?](#billing-change)
- [Co když je potřeba víc než 10 GB úložiště?](#more-storage-needed)
- [Můžete změnit mezi S1, S2 a S3 úrovně výkonu než plánované migrace?](#change-before)
- [Jak provedu migraci z S1, S2, S3 úrovněmi výkonu kolekce tvořené jedním oddílem na vlastní?](#migrate-diy)
- [Jak se ovlivněny vám EA zákazníka?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Proč se výkonu S1, S2 a S3 úrovně postupně vyřazuje z provozu?

Úrovně výkonu S1, S2 a S3 nenabízejí flexibilitu, která poskytuje standardní nabídku Azure Cosmos DB. Kapacita propustnosti i úložiště s S1, S2, úrovně výkonu S3, byly předem nastavené a nenabízí pružnost. Azure Cosmos DB teď nabízí přizpůsobit propustnost a úložiště nabízí mnohem větší flexibilitu v schopnost škálovat podle potřeby.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Jak kolekce tvořené jedním oddílem a dělené kolekce srovnání na S1, S2, úrovně výkonu S3?

Následující tabulka porovnává možnosti propustnost a úložiště, které jsou dostupné v kolekce tvořené jedním oddílem, dělené kolekce a S1, S2, úrovně výkonu S3. Tady je příklad oblasti USA – východ 2:

|   |Dělené kolekce|Kolekce tvořené jedním oddílem|S1|S2|S3|
|---|---|---|---|---|---|
|Maximální propustnost|Unlimited|10 tis. RU/s|250 RU/s|1 tis. RU/s|2.5 tis. RU/s|
|Minimální propustnost|2.5 tis. RU/s|400 RU/s|250 RU/s|1 tis. RU/s|2.5 tis. RU/s|
|Maximální velikost úložiště|Unlimited|10 GB|10 GB|10 GB|10 GB|
|Cena (každý měsíc)|Propustnost: $6 / 100 RU/s<br><br>Úložiště: $ 0,25/GB|Propustnost: $6 / 100 RU/s<br><br>Úložiště: $ 0,25/GB|25 USD|50 USD|100 USD|

Jste si již EA zákazníka? Pokud ano, najdete v části [jak mě I ovlivněná jsem EA zákazníka?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Co je třeba provést k zajištění nepřetržitého přístupu k mým datům?

Pokud máte kolekci S1, S2 nebo S3, měli byste migrovat kolekce do kolekce tvořené jedním oddílem prostřednictvím kódu programu [pomocí .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Jak se po dokončení migrace změní mé kolekce?

Pokud máte kolekci S1, můžete migrovat kolekce tvořené jedním oddílem s propustností 400 RU/s. 400 RU/s je k dispozici kolekce tvořené jedním oddílem nejnižší propustnost. Ale náklady pro 400 RU/s v kolekci jednoho oddílu je přibližně stejný, jako měla s S1 kolekce a 250. RU/s – platícího tak nejsou platícího pro velmi 150 RU/s dostupné.

Pokud máte kolekci S2, můžete migrovat kolekce tvořené jedním oddílem s 1 tis. RU/s. Zobrazí se žádná změna na vaší propustnost úroveň.

Pokud máte kolekci S3, můžete migrovat kolekce tvořené jedním oddílem s 2,5 tis. RU/s. Zobrazí se žádná změna na vaší propustnost úroveň.

V každé z těchto případech po dokončení migrace kolekce, bude možné přizpůsobit odpovídající úrovni vašeho propustnost, nebo ji škálovat nahoru a dolů tak, aby uživatelům poskytnout přístup s nízkou latencí. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Jak bude Moje fakturace změnit po I migrovat do kolekce tvořené jedním oddílem?

Za předpokladu, že budete mít 10 S1 kolekcí, 1 GB úložiště pro každou, v oblasti USA – východ a migrace těchto 10 S1 kolekcí do 10 kolekce tvořené jedním oddílem v 400 RU za sekundu (minimální úroveň). Pokud necháte 10 kolekce tvořené jedním oddílem pro úplný měsíc, bude vaše faktura vypadat takto:

![Jak S1 ceny pro 10 kolekce porovnává 10 kolekce pomocí ceny pro kolekce tvořené jedním oddílem](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>Co když je potřeba víc než 10 GB úložiště?

Jestli máte kolekci s úrovní výkonu S1, S2 nebo S3, nebo mít jeden oddíl kolekce, které mají všechny 10 GB úložiště, které jsou k dispozici, že nástroj pro migraci dat DB Cosmos Azure můžete migrovat data do oddílů kolekce s prakticky neomezené úložiště. Informace o výhodách dělenou kolekci najdete v tématu [dělení a škálování v Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Můžete změnit mezi S1, S2 a S3 úrovně výkonu než plánované migrace?

Pouze existující účty s S1, S2 a S3 výkon lze změnit a změnit úroveň úrovně výkonu prostřednictvím kódu programu [pomocí .NET SDK](#migrate-diy). Pokud změníte z S1, S3 nebo S3 do kolekce tvořené jedním oddílem, nelze vrátit k úrovní výkonu S1, S2 nebo S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Jak provedu migraci z S1, S2, S3 úrovněmi výkonu kolekce tvořené jedním oddílem na vlastní?

Můžete migrovat z úrovní výkonu S1, S2 a S3 kolekce tvořené jedním oddílem prostřednictvím kódu programu [pomocí .NET SDK](#migrate-diy). Můžete provést sami před plánované migrace, abyste mohli využívat výhod propustnost flexibilní možnosti s kolekce tvořené jedním oddílem.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrace do kolekce tvořené jedním oddílem pomocí .NET SDK

Tato část se vztahuje pouze změna shromažďování výkonu úrovně pomocí [SQL .NET API](sql-api-sdk-dotnet.md), ale proces je podobný pro naše dalších sadách SDK.

Zde je fragment kódu pro změnu propustnost kolekce do 5 000 jednotek žádosti za sekundu:
    
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

Navštivte [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) zobrazení další příklady a další informace o našich nabídka metody:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Jak se ovlivněny vám EA zákazníka?

Zákazníci EA bude cena chránit až do konce jejich aktuální kontrakt.

## <a name="next-steps"></a>Další postup
Další informace o cenách a správě dat pomocí Azure Cosmos DB najdete v těchto zdrojích:

1.  [Segmentace dat v databázi Cosmos](sql-api-partition-data.md). Vysvětlení rozdílu kontejneru tvořené jedním oddílem a oddílů kontejnery, jakož i tipy k implementaci strategie dělení bezproblémově škálování.
2.  [Ceny cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Další informace o náklady na zřizování propustnost a využívání úložiště.
3.  [Požadované jednotky](request-units.md). Pochopení spotřeby propustnosti na typy jiné operace, například pro čtení, zápisu, dotazů.
