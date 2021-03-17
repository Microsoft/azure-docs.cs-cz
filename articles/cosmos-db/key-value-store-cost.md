---
title: Poplatky za jednotky žádosti pro Azure Cosmos DB jako úložiště hodnot klíčů
description: Přečtěte si o nákladech jednotky žádosti Azure Cosmos DB pro jednoduché operace zápisu a čtení, když se používají jako úložiště klíč/hodnota.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 9354ae0a22ef2e8ab4ee6a57563d3f3c4c8e4547
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339294"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB jako úložiště hodnot klíčů – přehled nákladů
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB je globálně distribuovaná databázová služba pro více modelů, která umožňuje snadnou tvorbu vysoce dostupných a rozsáhlých aplikací ve velkém měřítku. Ve výchozím nastavení Azure Cosmos DB automaticky a efektivně indexovat všechna data, která ingestují. To umožňuje rychlé a konzistentní dotazy [SQL](./sql-query-getting-started.md) (a [JavaScript](stored-procedures-triggers-udfs.md)) na data. 

Tento článek popisuje náklady na Azure Cosmos DB pro jednoduché operace zápisu a čtení, když se používají jako úložiště klíčů a hodnot. Mezi operace zápisu patří vložení, nahrazení, odstranění a upsertuje datových položek. Kromě zaručení smlouvy SLA 99,999% dostupnosti pro všechny účty ve více oblastech Azure Cosmos DB nabízí zaručenou <ou latenci pro čtení a zápisy (indexovaných) na 99 percentil. 

## <a name="why-we-use-request-units-rus"></a>Proč používáme jednotky požadavků (ru)

Azure Cosmos DB výkon vychází z množství zřízené propustnosti vyjádřené v [jednotkách žádosti](request-units.md) (ru/s). Zřizování je druhé členitosti a je koupeno v RU/s ([Nezaměňovat se po hodinové fakturaci](https://azure.microsoft.com/pricing/details/cosmos-db/)). Ru by měla být považována za logickou abstrakci (měnu), která zjednodušuje zřizování požadované propustnosti pro aplikaci. Uživatelé nemusejí myslet na rozdíl mezi propustností čtení a zápisu. Model jedné měny ru vytváří efektivitu pro sdílení zřízené kapacity mezi čtením a zápisy. Tento model zřízené kapacity umožňuje službě poskytovat **předvídatelné a konzistentní propustnost, zaručenou nízkou latenci a vysokou dostupnost**. A konečně, zatímco pro znázornění propustnosti se používá model RU, má každý zřízený RU také definovaný objem prostředků (například paměť, jádra/CPU a IOPS).

Jako globálně distribuovaný databázový systém je Cosmos DB jedinou službou Azure, která poskytuje komplexní SLA, která pokrývá latenci, propustnost, konzistenci a vysokou dostupnost. Propustnost, kterou zřizujete, se použije u každé oblasti přidružené k vašemu účtu Cosmos. Pro čtení Cosmos DB nabízí více jasně definovaných [úrovní konzistence](consistency-levels.md) , ze kterých si můžete vybrat. 

Následující tabulka ukazuje počet ru potřebných k provedení operací čtení a zápisu na základě datové položky o velikosti 1 KB a 100 aktualizací KB s výchozím automatickým indexováním vypnuto. 

|Velikost položky|1 čtení|1 zápis|
|-------------|------|-------|
|1 kB|1 RU|5 ru|
|100 KB|10 RU|50 ru|

## <a name="cost-of-reads-and-writes"></a>Náklady na čtení a zápisy

Pokud zřídíte 1 000 RU/s, tato množství se zařadí 3 600 000 RU/hod a náklady se budou za hodinu (v USA a Evropě) finančně $0,08. Pro datovou položku velikosti 1 KB to znamená, že můžete pomocí zřízené propustnosti spotřebovat 3 600 000 čtení nebo 720 000 zápisů (3 600 000 RU/5). V normalizovaní na milion čtení a zápisů by náklady byly $0,022/milion čtení ($0,08/3,6) a $0.111/milionů zápisů ($0,08/0,72). Cena za milion se bude činit minimální, jak je uvedeno v následující tabulce.

|Velikost položky|Náklady na 1 000 000 čtení|Náklady na 1 000 000 zápisy|
|-------------|-------|--------|
|1 kB|$0,022|$0,111|
|100 KB|$0,222|$1,111|


Většina základního objektu BLOB nebo objekt ukládá služby poplatek $0,40 za milion transakcí čtení a $5 za milion transakcí zápisu. Pokud se používá optimálně, Cosmos DB můžou být až 98% levnější než tato další řešení (pro 1 KB transakcí).

## <a name="next-steps"></a>Další kroky

* K odhadu propustnosti vašich úloh použijte [kalkulačku ru](https://cosmos.azure.com/capacitycalculator/) .