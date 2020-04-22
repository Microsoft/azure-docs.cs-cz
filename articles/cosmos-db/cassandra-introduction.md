---
title: Úvod do rozhraní API Cassandra v Azure Cosmos DB
description: Zjistěte, jak můžete pomocí služby Azure Cosmos DB "zvedat a posouvat" stávající aplikace a vytvářet nové aplikace pomocí ovladačů Cassandra a CQL.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 05/21/2019
ms.openlocfilehash: 70dbceb51ed030124d1b793d77c6bc287da91065
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687639"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Úvod do rozhraní API Cassandra v Azure Cosmos DB

Rozhraní API Cassandra v Azure Cosmos DB je možné použít jako úložiště dat pro aplikace napsané pro [Apache Cassandra](https://cassandra.apache.org). To znamená, že s použitím stávajících [ovladačů Apache](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver), které jsou kompatibilní s CQLv4, teď může vaše existující aplikace pro Cassandru komunikovat s rozhraním API Cassandra v Azure Cosmos DB. V mnoha případech můžete přepnout z použití Apache Cassandra pomocí Azure Cosmos DB cassandra API, stačí změnit připojovací řetězec. 

Rozhraní Cassandra API umožňuje interakci s daty uloženými v Azure Cosmos DB pomocí cassandra dotazovací jazyk (CQL) , Cassandra založené nástroje (jako cqlsh) a Cassandra klienta ovladače, které jste již obeznámeni s.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Jaké jsou výhody používání rozhraní rozhraní API pro Apache Cassandra pro Azure Cosmos DB?

**Žádná správa provozu**: Jako plně spravovaná cloudová služba přebírá rozhraní API Cassandra v Azure Cosmos DB zatížení správy a monitorování nespočetné řady nastavení napříč operačním systémem, JVM a soubory yaml a jejich vzájemných interakcí. Azure Cosmos DB sleduje propustnost, latenci, úložiště, dostupnost a konfigurovatelná upozornění.

**Open source standard**: Přesto, že je plně spravovanou službou, Cassandra API stále podporuje velkou plochu nativního [drátového protokolu Apache Cassandra](cassandra-support.md), což vám umožní vytvářet aplikace na široce používaném a cloudově agnostik open source standardu.

**Správa výkonu**: Azure Cosmos DB garantuje pro čtení a zápis nízkou latenci na základě smluv SLA na úrovni 99. percentilu. Uživatelé se nemusí starat o provozní režii, aby byl zajištěný vysoký výkon a nízká latence u operací čtení a zápisu. To znamená, že uživatelé nepotřebují řešit plánování komprimace, správu značek odstraněných položek a ruční nastavování Bloomových filtrů a replik. Azure Cosmos DB odstraní zatížení, které představuje správa těchto záležitostí, a umožní vám se zaměřit na logiku aplikace.

**Možnost využívat stávající kód a nástroje**: Azure Cosmos DB poskytuje kompatibilitu na úrovni přenosového protokolu se stávajícími sadami SDK a nástroji Cassandra. Tato kompatibilita zajišťuje, že můžete použít stávající základ kódu s rozhraním API Cassandra v Azure Cosmos DB s nepatrnými změnami.

**Propustnost a pružnost úložiště**: Azure Cosmos DB poskytuje ve všech oblastech zaručenou propustnost a zřízenou propustnost může škálovat pomocí webu Azure Portal, PowerShellu nebo operací rozhraní příkazového řádku. Můžete [elasticky škálovat](manage-scale-cassandra.md) úložiště a propustnost pro vaše tabulky podle potřeby s předvídatelným výkonem.

**Globální distribuce a dostupnost**: Azure Cosmos DB poskytuje schopnost globálně distribuovat data napříč všemi oblastmi Azure a obsluhovat data místně a přitom zajišťovat přístup k datům s nízkou latencí a vysokou dostupnost. Azure Cosmos DB zajišťuje vysokou dostupnost 99,99 % v rámci oblasti a dostupnost čtení a zapisování 99,999 % ve více oblastech při nulové režii provozu. Další informace najdete v článku o [globální distribuci dat](distribute-data-globally.md). 

**Volba konzistence**: Pro dosažení optimálního poměru mezi konzistencí a výkonem si můžete u Azure Cosmos DB vybrat z pěti jasně definovaných úrovní konzistence. Úrovně konzistence jsou: silná, omezená neaktuálnost, relace, konzistentní předpona a případné. Tyto dobře definované, praktické a intuitivní úrovně konzistence umožňují vývojářům zvolit přesný poměr mezi konzistencí, dostupností a latencí. Další informace najdete v článku o [úrovních konzistence](consistency-levels.md). 

**Podniková úroveň**: V Azure Cosmos DB mají uživatelé k dispozici [certifikáty souladu](https://www.microsoft.com/trustcenter), které jim zajišťují bezpečné používání této platformy. Azure Cosmos DB také poskytuje šifrování v klidovém stavu a za provozu, firewall protokolu IP a protokoly auditu pro aktivity roviny řízení.

**Event Sourcing**: Cassandra API poskytuje přístup k protokolu trvalé změny, [kanálu změn](cassandra-change-feed.md), který může usnadnit získávání událostí přímo z databáze. V Apache Cassandra, jediným ekvivalentem je změna sběru dat (CDC), což je pouze mechanismus pro označení konkrétní tabulky pro archivní, stejně jako odmítnutí zápisy do těchto tabulek, jakmile je dosaženo konfigurovatelné velikosti na disku pro protokol CDC (tyto funkce jsou redundantní v Cosmos DB jako příslušné aspekty jsou automaticky řízeny).

## <a name="next-steps"></a>Další kroky

* Můžete rychle začít s vytvářením následujících aplikací v konkrétním jazyce určených k vytváření a správě dat rozhraní API Cassandra:
  - [Aplikace v Node.js](create-cassandra-nodejs.md)
  - [Aplikace v .NET](create-cassandra-dotnet.md)
  - [Aplikace v Pythonu](create-cassandra-python.md)

* Můžete začít s [vytvářením účtu, databáze a tabulky Cassandra API](create-cassandra-api-account-java.md) pomocí aplikace Java.

* [Načtěte ukázková data do tabulky rozhraní API Cassandra](cassandra-api-load-data.md) pomocí aplikace v Javě.

* [Zadejte dotaz na data z účtu rozhraní API Cassandra](cassandra-api-query-data.md) pomocí aplikace v Javě.

* Další informace o funkcích Apache Cassandra podporovaných rozhraním API Cassandra v Azure Cosmos DB najdete v článku [Podpora Cassandry](cassandra-support.md).

* Přečtěte si [nejčastější dotazy](cassandra-faq.md).
