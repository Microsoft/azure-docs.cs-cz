---
title: Výběr správné konzistence úroveň pro vaši aplikaci, která používá službu Azure Cosmos DB | Dokumentace Microsoftu
description: Výběr správné konzistence úroveň pro vaši aplikaci ve službě Azure Cosmos DB.
keywords: konzistence, výkon, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 2fc21ffde5f3f4b84b2ed025cba5d228e592343f
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823414"
---
# <a name="choose-the-right-consistency-level-for-your-application"></a>Vyberte úroveň konzistence správné pro vaši aplikaci

Distribuované databáze spoléhat na replikaci pro vysokou dostupnost, s nízkou latencí nebo obojí, ujistěte se, základní kompromis mezi konzistence čtení a dostupnosti, latence a propustnosti. Většina komerčně dostupný distribuovaných databází požádejte vývojáři zvolit mezi těmito dvěma modely extreme konzistence: silnou konzistenci a konečné konzistence. Azure Cosmos DB umožňuje vývojářům volit pět jasně definované modely konzistence: silná, omezená neaktuálnost, relace, konzistentní Předpona a konečný výsledek. Každá z těchto modelů konzistence je dobře definovaný a intuitivní a je možné u konkrétních scénářů reálného světa. Zadejte každou pět modelů konzistence [dostupnost a výkon kompromisy](consistency-levels-tradeoffs.md) a je zajištěná komplexní smlouvy SLA. Následující pokyny pro jednoduché můžete učinit správnou volbu v mnoha běžným scénářům.

## <a name="sql-api-and-table-api"></a>SQL API a rozhraní API tabulky

Zvažte následující body, pokud vaše aplikace je sestavena pomocí rozhraní SQL API služby Cosmos DB nebo pomocí rozhraní Table API

- Pro většinu scénářů reálného světa konzistence typu relace je optimální a je doporučenou možnost. Další informace najdete v článku, [postupy spravovat tokenu relace pro aplikaci](how-to-manage-consistency.md#utilize-session-tokens).

- Pokud vaše aplikace vyžaduje silnou konzistenci, doporučuje se použít úroveň konzistence omezená neaktuálnost.

- Pokud potřebujete přísnější záruky konzistence než ty zadaný konzistence typu relace a jeden milisekund latencí pro zápis, je doporučeno používat omezená neaktuálnost úrovně konzistence.  

- Pokud vaše aplikace vyžaduje konzistenci typu případné, doporučuje se použít úroveň konzistentní předpona konzistence.

- Pokud budete potřebovat méně striktní konzistenci záruky než těch, které jsou poskytované konzistence typu relace, doporučuje se použít úroveň konzistentní předpona konzistence.

- Pokud potřebujete nejvyšší možnou dostupnost a nejnižší latenci, použijte úroveň konzistence typu případné.

## <a name="cassandra-mongodb-and-gremlin-api"></a>Cassandra, MongoDB a Gremlin API

- Podrobnosti o mapování mezi "Úrovně konzistence čtení" nabízí úrovně konzistence Apache Cassandra a Cosmos DB, najdete v tématu [úrovně konzistence a rozhraní API Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).

- Podrobnosti o mapování mezi "Pro čtení problém" úrovní konzistence MongoDB a Azure Cosmos DB najdete v tématu [úrovně konzistence a rozhraní API Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Záruky konzistence v praxi

V praxi se může zobrazit silnější záruky konzistence. Záruky konzistence pro operace čtení odpovídají aktuálnosti a pořadí stavu databáze, která požadujete. Konzistence čtení se váže k řazení a šíření operace zápisu/aktualizace.  

* Pokud je úroveň konzistence nastavená na **omezená neaktuálnost**, Cosmos DB zaručuje, že klienti vždy načíst hodnotu předchozí zápis s lag ohraničena neaktuálnost okno.

* Pokud je úroveň konzistence nastavená na **silné**okno neaktuálnost je rovna nule a klienti jsou zaručeno, že přečte nejnovější potvrzená hodnota operace zápisu.

* Pro zbývající tři konzistence úrovně okno neaktuálnost je do značné míry závisí na velikosti pracovní zátěže. Například, pokud neexistují žádné operace zápisu na databázi, operace čtení se **konečné**, **relace**, nebo **konzistentní předpona** úrovně konzistence je pravděpodobně poskytne stejné výsledky jako operace čtení se úroveň silná konzistence.

Pokud váš účet služby Cosmos DB má nakonfigurovanou úrovně konzistence, než je silná konzistence, můžete najít na více systémů pravděpodobnost, že vaši klienti mohou získat silné a konzistentních čtení pro vaše úlohy pohledem na pravděpodobnostní omezená Neaktuálnost (PBS) Metrika. Tato metrika je zveřejněný na webu Azure Portal najdete další informace najdete v tématu [metriku monitorování Probabilistically omezená Neaktuálnost (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Pravděpodobnostní omezená neaktuálnost ukáže, jak konečné konzistenci typu případné. Tato metrika poskytuje přehled o tom, jak často, získáte silnější konzistence než úroveň konzistence, kterou jste právě nakonfigurovali na účtu služby Cosmos DB. Jinými slovy můžete vidět pravděpodobnost (měřeno v milisekundách) získávání silně konzistentních čtení pro kombinaci zápisu a oblasti čtení.

## <a name="next-steps"></a>Další postup

Další informace o úrovních konzistence v následujících článcích:

* [Mapování úrovní konzistence napříč API služby Cosmos DB](consistency-levels-across-apis.md)
* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Jak spravovat tokenu relace pro aplikaci](how-to-manage-consistency.md#utilize-session-tokens)
* [Sledovat metriku Probabilistically omezená Neaktuálnost (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)