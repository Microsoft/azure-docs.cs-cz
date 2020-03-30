---
title: Vyžádání jednotkových poplatků pro Azure Cosmos DB jako úložiště hodnot klíčů
description: Další informace o poplatcích jednotky požadavku azure cosmos DB pro jednoduché operace zápisu a čtení, když se používá jako úložiště klíč/hodnota.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647507"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB jako úložiště hodnot klíčů – přehled nákladů

Azure Cosmos DB je globálně distribuovaná databázová služba s více modely pro snadné vytváření vysoce dostupných rozsáhlých aplikací. Ve výchozím nastavení Azure Cosmos DB automaticky a efektivně indexuje všechna data, která ingestuje. To umožňuje rychlé a konzistentní [dotazy SQL](how-to-sql-query.md) (a [JavaScript)](stored-procedures-triggers-udfs.md)na data. 

Tento článek popisuje náklady na Azure Cosmos DB pro jednoduché operace zápisu a čtení při použití jako úložiště klíč/hodnota. Operace zápisu zahrnují vložení, nahrazení, odstranění a upserts datových položek. Kromě záruky 99,999% dostupnost sla pro všechny účty s více oblastmi, Azure Cosmos DB nabízí zaručené <latence 10 ms pro čtení a pro (indexované) zápisy, na 99 percentilu. 

## <a name="why-we-use-request-units-rus"></a>Proč používáme jednotky požadavků (RU)

Výkon Azure Cosmos DB je založen na množství zřízené propustnosti vyjádřené v [jednotkách požadavků](request-units.md) (RU/s). Zřizování je na druhé rozlišovací schopnost a je zakoupenv RU /s[(nesmí být zaměňována s hodinovou fakturací](https://azure.microsoft.com/pricing/details/cosmos-db/)). Ru by měly být považovány za logickou abstrakci (měna), která zjednodušuje zřizování požadované propustnost pro aplikaci. Uživatelé nemusí přemýšlet o rozlišení mezi propustností pro čtení a zápisu. Model jednotné měny ru vytváří efektivitu sdílet zřízenou kapacitu mezi čtení a zápisy. Tento model zřízené kapacity umožňuje službě poskytovat **předvídatelnou a konzistentní propustnost, zaručenou nízkou latenci a vysokou dostupnost**. Nakonec zatímco model RU se používá k zobrazení propustnost, každý zřízený RU má také definované množství prostředků (např. paměť, jádra/PROCESOR a VOPS).

Jako globálně distribuovaný databázový systém je Cosmos DB jedinou službou Azure, která poskytuje komplexní smlouvy SLA pokrývající latenci, propustnost, konzistenci a vysokou dostupnost. Propustnost, kterou zřídíte, se použije pro každou oblast přidruženou k vašemu účtu Cosmos. Pro čtení Cosmos DB nabízí více, dobře definované [úrovně konzistence](consistency-levels.md) si můžete vybrat. 

V následující tabulce je uveden počet ru potřebných k provádění operací čtení a zápisu na základě datové položky o velikosti 1 KB a 100 kB s vypnutým výchozím automatickým indexováním. 

|Velikost položky|1 Číst|1 Zápis|
|-------------|------|-------|
|1 kB|1 ŽP|5 Ru|
|100 KB|10 RU|50 RU|

## <a name="cost-of-reads-and-writes"></a>Náklady na čtení a zápisy

Pokud zřídíte 1 000 RU/s, činí to 3,6 milionu RU/hodinu a bude to stát 0,08 USD za hodinu (v USA a Evropě). Pro datovou položku velikosti 1 KB to znamená, že můžete spotřebovat 3,6 milionu čtení nebo 0,72 milionu zápisů (3,6 milionu RU / 5) pomocí zřízená propustnost. Normalizováno na milion čtení a zápisů, náklady by byly $0.022 /million čtení ($0.08 / 3.6) a $0.111/million zápisů ($0.08 / 0.72). Náklady na milion se stanou minimálními, jak je uvedeno v následující tabulce.

|Velikost položky|Náklady na 1 milion čtení|Náklady na 1 milion zápisů|
|-------------|-------|--------|
|1 kB|$0.022|$0.111|
|100 KB|$0.222|1,111 USD|


Většina základních služeb blob nebo objekt ukládá poplatky 0,40 dolarů za milion transakcí čtení a 5 dolarů za milion transakcí zápisu. Pokud je cosmos DB použit optimálně, může být až o 98 % levnější než tato jiná řešení (pro transakce 1 KB).

## <a name="next-steps"></a>Další kroky

* Pomocí [kalkulačky RU](https://cosmos.azure.com/capacitycalculator/) můžete odhadnout propustnost pro vaše úlohy.

