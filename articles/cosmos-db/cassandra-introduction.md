---
title: Úvod do rozhraní API Cassandra v Azure Cosmos DB
description: Zjistěte, jak pomocí služby Azure Cosmos DB provést migraci stávajících aplikací metodou „lift and shift“ a vytvářet nové aplikace využívající rozhraní API Cassandra s použitím ovladačů Cassandra a CQL, které už znáte.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9d948164177b7acd2df722078ae590f409c8e795
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036507"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Úvod do rozhraní API Cassandra v Azure Cosmos DB

Rozhraní API Cassandra v Azure Cosmos DB je možné použít jako úložiště dat pro aplikace napsané pro [Apache Cassandra](https://cassandra.apache.org/). To znamená, že s použitím stávajících [ovladačů Apache](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver), které jsou kompatibilní s CQLv4, teď může vaše existující aplikace pro Cassandru komunikovat s rozhraním API Cassandra v Azure Cosmos DB. V mnoha případech se dá přepínat mezi použitím Apache Cassandra a použitím rozhraní API Cassandra v Azure Cosmos DB jednoduše změnou připojovacího řetězce. 

Rozhraní API Cassandra umožňuje pracovat s daty uloženými v Azure Cosmos DB pomocí jazyka Cassandra Query Language (CQL), nástrojů založených na Cassandře (např. cqlsh) a ovladačů klienta Cassandry, které už znáte.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Jaké jsou výhody používání rozhraní rozhraní API pro Apache Cassandra pro Azure Cosmos DB?

**Žádné operace správy**: Jako plně spravovaná Cloudová služba Azure Cosmos DB Cassandra API eliminuje režijní náklady na správu a monitorování velkého počtu nastavení v rámci operačního systému, JVM a soubory yaml a jejich interakce. Azure Cosmos DB sleduje propustnost, latenci, úložiště, dostupnost a konfigurovatelná upozornění.

**Správa výkonu**: Azure Cosmos DB nabízí garantovanou nízkou latencí čte a zapisuje na 99. percentilu, zajištěná smluv SLA. Uživatelé se nemusí starat o provozní režii, aby byl zajištěný vysoký výkon a nízká latence u operací čtení a zápisu. To znamená, že uživatelé nepotřebují řešit plánování komprimace, správu značek odstraněných položek a ruční nastavování Bloomových filtrů a replik. Azure Cosmos DB odstraní zatížení, které představuje správa těchto záležitostí, a umožní vám se zaměřit na logiku aplikace.

**Možnost používat existující kód a nástroje**: Azure Cosmos DB poskytuje přenosový protokol úrovně kompatibility s existujících sad Cassandra SDK a nástroje. Tato kompatibilita zajišťuje, že můžete použít stávající základ kódu s rozhraním API Cassandra v Azure Cosmos DB s nepatrnými změnami.

**Propustnost a úložiště elasticitu**: Azure Cosmos DB poskytuje zaručena propustnost napříč všemi oblastmi a může se škálovat zřízená propustnost pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku operace. Můžete pružně škálovat úložiště a propustnost pro tabulky podle potřeby s předvídatelným výkonem.

**Globální distribuce a dostupnost**: Azure Cosmos DB poskytuje schopnost globálně distribuovat data napříč všemi oblastmi Azure a data místně přitom zajistit přístup k datům s nízkou latencí a vysokou dostupnost. Azure Cosmos DB zajišťuje vysokou dostupnost 99,99 % v rámci oblasti a dostupnost čtení a zapisování 99,999 % ve více oblastech při nulové režii provozu. Další informace najdete v článku o [globální distribuci dat](distribute-data-globally.md). 

**Volby konzistence**: Azure Cosmos DB poskytuje řadu pět jasně definovaných úrovní konzistence pro dosažení optimálního kompromisy mezi konzistencí a výkonem. Úrovně konzistence jsou: silná, omezená neaktuálnost, relace, konzistentní předpona a případné. Tyto dobře definované, praktické a intuitivní úrovně konzistence umožňují vývojářům zvolit přesný poměr mezi konzistencí, dostupností a latencí. Další informace najdete v článku o [úrovních konzistence](consistency-levels.md). 

**Podnikové**: Azure cosmos DB poskytuje [certifikací dodržování předpisů](https://www.microsoft.com/trustcenter) zajistit uživatelé bezpečně používat platformu. Azure Cosmos DB také poskytuje šifrování v klidovém stavu a za provozu, firewall protokolu IP a protokoly auditu pro aktivity roviny řízení.

## <a name="next-steps"></a>Další postup

* Můžete rychle začít s vytvářením následujících aplikací v konkrétním jazyce určených k vytváření a správě dat rozhraní API Cassandra:
  - [Aplikace v Node.js](create-cassandra-nodejs.md)
  - [Aplikace .NET](create-cassandra-dotnet.md)
  - [Aplikace v Pythonu](create-cassandra-python.md)

* Začněte [vytvořením účtu rozhraní API Cassandra, databáze a tabulky](create-cassandra-api-account-java.md) pomocí aplikace v Javě.

* [Načtěte ukázková data do tabulky rozhraní API Cassandra](cassandra-api-load-data.md) pomocí aplikace v Javě.

* [Zadejte dotaz na data z účtu rozhraní API Cassandra](cassandra-api-query-data.md) pomocí aplikace v Javě.

* Další informace o funkcích Apache Cassandra podporovaných rozhraním API Cassandra v Azure Cosmos DB najdete v článku [Podpora Cassandry](cassandra-support.md).

* Přečtěte si [nejčastější dotazy](faq.md#cassandra).
