---
title: Poplatky za jednotky žádosti pro službu Azure Cosmos DB jako hodnotu klíče úložiště
description: Další informace o za jednotky žádosti o služby Azure Cosmos DB pro jednoduché zápisu a operace čtení, když se používá jako úložiště dvojic klíč/hodnota.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e81dec7d7556819e60a65a44106426c226c6223d
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035725"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB jako hodnotu klíče úložiště – přehled nákladů

Azure Cosmos DB je globálně distribuovaná a vícemodelová databázová služba pro snadné vytváření aplikací s vysokou dostupností, ve velkém měřítku. Ve výchozím nastavení služby Azure Cosmos DB automaticky indexuje všechna data, která ingestuje efektivně. To umožňuje rychlé a konzistentní [SQL](how-to-sql-query.md) (a [JavaScript](stored-procedures-triggers-udfs.md)) dotazy na všech druhů dat. 

Tento článek popisuje náklady na služby Azure Cosmos DB pro jednoduché zápisu a operace čtení, když se používá jako úložiště dvojic klíč/hodnota. Zápisu operace zahrnují vložení, nahradí, odstraní a upsertuje dokumentů. Kromě zajištění 99,99 % smlouva SLA o dostupnosti pro všechny účty v jedné oblasti a všechny účty ve více oblastech s mírnější konzistencí a 99,999 % dostupnost pro čtení pro všechny účty databáze pro více oblastí, ve službě Azure Cosmos DB nabízí zaručeno, že < 10 ms, latence přečte a < 15 ms latence (indexované), zapíše na 99. percentilu. 

## <a name="why-we-use-request-units-rus"></a>Proč používáme jednotek žádosti (ru)

Výkon služby Azure Cosmos DB je založen na velikost zřízeného [jednotek žádostí](request-units.md) (RU) pro oddíl. Přidělení přístupových práv na druhý členitosti a jste si koupili v ru za sekundu ([by se zaměňovat s fakturací po hodinách](https://azure.microsoft.com/pricing/details/cosmos-db/)). RU by měly být považovány měny, která zjednodušuje zřizování požadované propustnosti pro aplikaci. Naši zákazníci nemají představit rozlišování pro čtení a zápis kapacitních jednotek. Model jedné měny ru vytvoří efektivitu sdílet zřízená kapacita mezi operací čtení a zápisu. Tento model zřízená kapacita umožňuje službě a zajištění konzistentní a předvídatelné propustnosti, zaručené nízké latence a vysoká dostupnost. Nakonec RU využíváme k modelu propustnost, ale každý zřízené RU také obsahuje definovaný počet prostředky (paměť, Core). RU/s není pouze vstupně-výstupních operací.

Jako globálně distribuovaný databázový systém služby Cosmos DB je pouze Azure služba, která poskytuje smlouvu SLA na latence, propustnosti a konzistence kromě vysoké dostupnosti. Propustnost, kterou zřídíte platí pro každou z oblastí, přidružených k účtu databáze Cosmos DB. Pro čtení, Cosmos DB nabízí více správně definovaných [úrovně konzistence](consistency-levels.md) si můžete vybírat. 

Následující tabulka uvádí, že počet ru požadované k provedení pro čtení a zápisu transakce na základě velikosti dokumentu o velikosti 1 KB a 100 kB.

|Velikost položky|1 pro čtení|Zápis 1|
|-------------|------|-------|
|1 KB|1 RU|5 ru|
|100 KB|10 RU|50 jednotek ru|

## <a name="cost-of-reads-and-writes"></a>Náklady na operace čtení a zápisy

Pokud zřídíte 1 000 RU/s, toto množství RU za hodinu a se 3.6-m náklady 0,08 USD za hodinu (v USA a Evropa). Velikost 1 KB dokumentu, to znamená, že můžete využívat čtení 3.6-m nebo m 0,72 zapíše (3.6 až milion RU / 5) pomocí zřízené propustnosti. Normalizovány na milion operací čtení a zápisu, náklady by byly $0,022 /m čtení (0,08 USD / 3.6) a bude je tam 0.111 $/ m (0,08 USD / 0,72). Náklady za milionů stane minimální, jak je znázorněno v následující tabulce.

|Velikost položky|Čtení 1-m|1 milion zápisu|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


Většina základních objektů blob nebo objektu úložiště služby sazba 0,40 $ za milion transakcí čtení a 5 $ za milion zápisu transakce. Pokud použijete optimálně, Cosmos DB může být až 98 % levnější než tato řešení (pro 1 KB transakce).

## <a name="next-steps"></a>Další postup

Nenechte si ujít nové články týkající se optimalizace zřizování prostředků Azure Cosmos DB. Do té doby můžete použít naši [RU Kalkulačka](https://www.documentdb.com/capacityplanner).

