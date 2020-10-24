---
title: Vyřazení úrovní výkonu Azure Cosmos DB
description: Přečtěte si o úrovních výkonu S1, S2 a S3, které byly dříve dostupné v Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: da74fde761d32802966d1f477beaf6138c77020e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475392"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Vyřazení úrovní výkonu S1, S2 a S3 z provozu

> [!IMPORTANT] 
> Úrovně výkonu S1, S2 a S3 popsané v tomto článku jsou vyřazeny a již nejsou k dispozici pro nové účty Azure Cosmos DB.

Tento článek obsahuje přehled úrovní výkonu S1, S2 a S3 a popisuje, jak se kolekce, které používají tyto úrovně výkonu, můžou migrovat do kolekcí s jedním děleným oddílem. Po přečtení tohoto článku budete moci zodpovědět následující otázky:

- [Proč dochází k vyřazení úrovní výkonu S1, S2 a S3?](#why-retired)
- [Jak se porovnávají kolekce s jedním oddílem a dělené kolekce s úrovněmi výkonu S1, S2 a S3?](#compare)
- [Co potřebuji k zajištění nepřerušovaného přístupu k datům?](#uninterrupted-access)
- [Jak se změní moje kolekce po migraci?](#collection-change)
- [Jak se změní fakturace po migraci do kolekcí s jedním oddílem?](#billing-change)
- [Co když potřebuji víc než 20 GB úložiště?](#more-storage-needed)
- [Můžu během plánované migrace změnit úroveň výkonu S1, S2 a S3?](#change-before)
- [Návody migrujete z úrovní výkonu S1, S2, S3 na samostatné kolekce na vlastní oddíly?](#migrate-diy)
- [Jak mám dopad na to, když jsem zákazníkem se smlouvou EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Proč jsou vyřazení úrovní výkonu S1, S2 a S3?

Úroveň výkonu S1, S2 a S3 nenabízí flexibilitu, kterou nabízí standardní Azure Cosmos DB. U úrovní výkonu S1, S2 a S3 byla předem nastavena propustnost i kapacita úložiště a nenabídla se tak pružnost. Azure Cosmos DB teď nabízí možnost přizpůsobit svou propustnost a úložiště, nabízí vám mnohem větší flexibilitu ve vaší schopnosti škálování podle vašich potřeb.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Jak se porovnávají kolekce s jedním oddílem a dělené kolekce s úrovněmi výkonu S1, S2 a S3?

V následující tabulce jsou porovnávány možnosti propustnosti a úložiště dostupné v kolekcích s jedním oddílem, dělené kolekce a S1, S2 a S3 úrovně výkonu. Tady je příklad USA – východ 2 oblasti:

| Název kvóty  |Dělená kolekce|Kolekce s jedním oddílem|S1|S2|S3|
|---|---|---|---|---|---|
|Maximální propustnost|Unlimited|10 000 RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Minimální propustnost|2,5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Maximální velikost úložiště|Unlimited|20 GB|20 GB|20 GB|20 GB|
|Cena (měsíčně)|Propustnost: $6/100 RU/s<br><br>Úložiště: $0,25/GB|Propustnost: $6/100 RU/s<br><br>Úložiště: $0,25/GB|$25 USD|$50 USD|$100 USD|

Jste zákazníkem EA? Pokud ano, přečtěte si, [Jak mám dopad na to, pokud jsem zákazníkem se smlouvou EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Co potřebuji k zajištění nepřerušovaného přístupu k datům?

Pokud máte kolekci S1, S2 nebo S3, měli byste kolekci migrovat do jedné kolekce oddílů programově [pomocí sady .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Jak se změní moje kolekce po migraci?

Pokud máte kolekci S1, můžete je migrovat do jedné kolekce oddílů s propustností 400 RU/s. 400 RU/s je nejnižší propustnost dostupná pro kolekce s jedním oddílem. Náklady na 400 RU/s v kolekci s jedním oddílem jsou ale přibližně stejné jako u vaší kolekce S1 a 250 RU/s, takže nebudete platit za další 150, které máte k dispozici.

Pokud máte kolekci S2, můžete je migrovat do jedné kolekce oddílů s 1 K RU/s. Zobrazí se žádná změna úrovně propustnosti.

Pokud máte kolekci S3, můžete je migrovat do jedné kolekce oddílů pomocí 2,5 K RU/s. Zobrazí se žádná změna úrovně propustnosti.

V každém z těchto případů budete moct po migraci kolekce přizpůsobit úroveň propustnosti nebo je podle potřeby škálovat nahoru a dolů, aby se uživatelům zajistil přístup s nízkou latencí. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Jak se změní fakturace po migraci do kolekcí s jedním oddílem?

Za předpokladu, že máte 10 kolekcí S1, 1 GB úložiště pro každý, v USA – východ oblasti a migrujete tyto 10 kolekcí S1 do 10 kolekcí s jedním oddílem při 400 RU/s (minimální úroveň). Pokud udržujete 10 kolekcí s jedním oddílem po celý měsíc, vaše vyúčtování bude vypadat takto:

:::image type="content" source="./media/performance-levels/s1-vs-standard-pricing.png" alt-text="Jak ceny S1 pro 10 kolekcí porovnávají s 10 kolekcemi za použití cen pro jednu kolekci oddílů" border="false":::

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>Co když potřebuji víc než 20 GB úložiště?

Bez ohledu na to, jestli máte kolekci s úrovní výkonu S1, S2 nebo S3 nebo pokud máte kolekci s jedním oddílem, máte k dispozici více než 20 GB úložiště, můžete použít nástroj pro migraci dat Azure Cosmos DB k migraci dat do dělené kolekce s prakticky neomezeným úložištěm. Informace o výhodách dělené kolekce najdete [v tématu dělení a škálování v Azure Cosmos DB](partitioning-overview.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Můžu během plánované migrace změnit úroveň výkonu S1, S2 a S3?

Pouze existující účty s výkonem S1, S2 a S3 lze měnit a měnit [pomocí sady .NET SDK na](#migrate-diy)úrovni výkonu programově. Pokud přejdete z S1, S3 nebo S3 na jednu kolekci oddílů, nemůžete se vrátit na úroveň výkonu S1, S2 nebo S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Návody migrujete z úrovní výkonu S1, S2, S3 na samostatné kolekce na vlastní oddíly?

[Pomocí sady .NET SDK](#migrate-diy)můžete z úrovní výkonu S1, S2 a S3 migrovat do kolekcí s jedním oddílem programově. Můžete to provést na vlastní před plánovanou migrací, abyste využili výhod flexibilních možností propustnosti dostupných v kolekcích s jedním oddílem.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrace do kolekcí s jedním oddílem pomocí sady .NET SDK

Tato část se zabývá jenom změnou úrovně výkonu kolekce pomocí [rozhraní API SQL .NET](sql-api-sdk-dotnet.md), ale tento postup je podobný pro naše jiné sady SDK.

Tady je fragment kódu pro změnu propustnosti kolekce na 5 000 jednotek žádostí za sekundu:
    
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

Podívejte se na [MSDN](/dotnet/api/microsoft.azure.documents.client.documentclient) , kde najdete další příklady a další informace o našich způsobech nabídky:

* [**ReadOfferAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync)
* [**ReadOffersFeedAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.readoffersfeedasync)
* [**ReplaceOfferAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync)
* [**CreateOfferQuery**](/previous-versions/azure/dn975114(v=azure.100))

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Jak mám dopad na to, když jsem zákazníkem se smlouvou EA?

Zákazníci se smlouvou EA budou mít cenu chráněnou až do konce stávající smlouvy.

## <a name="next-steps"></a>Další kroky
Pokud chcete získat další informace o cenách a správě dat pomocí Azure Cosmos DB, Prozkoumejte tyto materiály:

1.  [Vytváření oddílů dat v Cosmos DB](partitioning-overview.md). Pochopte rozdíl mezi kontejnerem s jedním oddílem a kontejnery rozdělenými na oddíly a také tipy k implementaci strategie dělení pro bezproblémové škálování.
2.  [Cosmos DB ceny](https://azure.microsoft.com/pricing/details/cosmos-db/). Seznamte se s náklady na zřizování propustnosti a spotřebou úložiště.
3.  [Jednotky žádosti](request-units.md). Pochopte spotřebu propustnosti pro různé typy operací, například čtení, zápis a dotazování.