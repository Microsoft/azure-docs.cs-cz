---
title: Poplatky za jednotky žádosti pro službu Azure Cosmos DB jako hodnotu klíče úložiště
description: Další informace o za jednotky žádosti o služby Azure Cosmos DB pro jednoduché zápisu a operace čtení, když se používá jako úložiště dvojic klíč/hodnota.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77647507"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB jako úložiště hodnot klíčů – přehled nákladů

Azure Cosmos DB je globálně distribuovaná a vícemodelová databázová služba pro snadné vytváření aplikací s vysokou dostupností, ve velkém měřítku. Ve výchozím nastavení Azure Cosmos DB automaticky a efektivně indexovat všechna data, která ingestují. To umožňuje rychlé a konzistentní dotazy [SQL](how-to-sql-query.md) (a [JavaScript](stored-procedures-triggers-udfs.md)) na data. 

Tento článek popisuje náklady na služby Azure Cosmos DB pro jednoduché zápisu a operace čtení, když se používá jako úložiště dvojic klíč/hodnota. Mezi operace zápisu patří vložení, nahrazení, odstranění a upsertuje datových položek. Kromě zaručení smlouvy SLA 99,999% dostupnosti pro všechny účty ve více oblastech Azure Cosmos DB nabízí zaručenou <ou latenci pro čtení a zápisy (indexovaných) na 99 percentil. 

## <a name="why-we-use-request-units-rus"></a>Proč používáme jednotek žádosti (ru)

Azure Cosmos DB výkon vychází z množství zřízené propustnosti vyjádřené v [jednotkách žádosti](request-units.md) (ru/s). Zřizování je druhé členitosti a je koupeno v RU/s ([Nezaměňovat se po hodinové fakturaci](https://azure.microsoft.com/pricing/details/cosmos-db/)). Ru by měla být považována za logickou abstrakci (měnu), která zjednodušuje zřizování požadované propustnosti pro aplikaci. Uživatelé nemusejí myslet na rozdíl mezi propustností čtení a zápisu. Model jedné měny ru vytvoří efektivitu sdílet zřízená kapacita mezi operací čtení a zápisu. Tento model zřízené kapacity umožňuje službě poskytovat **předvídatelné a konzistentní propustnost, zaručenou nízkou latenci a vysokou dostupnost**. A konečně, zatímco pro znázornění propustnosti se používá model RU, má každý zřízený RU také definovaný objem prostředků (například paměť, jádra/CPU a IOPS).

Jako globálně distribuovaný databázový systém je Cosmos DB jedinou službou Azure, která poskytuje komplexní SLA, která pokrývá latenci, propustnost, konzistenci a vysokou dostupnost. Propustnost, kterou zřizujete, se použije u každé oblasti přidružené k vašemu účtu Cosmos. Pro čtení Cosmos DB nabízí více jasně definovaných [úrovní konzistence](consistency-levels.md) , ze kterých si můžete vybrat. 

Následující tabulka ukazuje počet ru potřebných k provedení operací čtení a zápisu na základě datové položky o velikosti 1 KB a 100 aktualizací KB s výchozím automatickým indexováním vypnuto. 

|Velikost položky|1 pro čtení|Zápis 1|
|-------------|------|-------|
|1 KB|1 RU|5 ru|
|100 KB|10 RU|50 jednotek ru|

## <a name="cost-of-reads-and-writes"></a>Náklady na operace čtení a zápisy

Pokud zřídíte 1 000 RU/s, tato množství se zařadí 3 600 000 RU/hod a náklady se budou za hodinu (v USA a Evropě) finančně $0,08. Pro datovou položku velikosti 1 KB to znamená, že můžete pomocí zřízené propustnosti spotřebovat 3 600 000 čtení nebo 720 000 zápisů (3 600 000 RU/5). V normalizovaní na milion čtení a zápisů by náklady byly $0,022/milion čtení ($0,08/3,6) a $0.111/milionů zápisů ($0,08/0,72). Náklady za milionů stane minimální, jak je znázorněno v následující tabulce.

|Velikost položky|Náklady na 1 000 000 čtení|Náklady na 1 000 000 zápisy|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


Většina základních objektů blob nebo objektu úložiště služby sazba 0,40 $ za milion transakcí čtení a 5 $ za milion zápisu transakce. Pokud se používá optimálně, Cosmos DB můžou být až 98% levnější než tato další řešení (pro 1 KB transakcí).

## <a name="next-steps"></a>Další kroky

* K odhadu propustnosti vašich úloh použijte [kalkulačku ru](https://cosmos.azure.com/capacitycalculator/) .

