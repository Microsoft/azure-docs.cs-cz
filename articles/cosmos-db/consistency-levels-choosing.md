---
title: Výběr správné konzistence úroveň pro vaši aplikaci | Dokumentace Microsoftu
description: Výběr správné konzistence úroveň pro vaši aplikaci ve službě Azure Cosmos DB.
keywords: konzistence, výkon, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 4b438320ac75918d10880a4bcdccadca4eebec32
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244182"
---
# <a name="choose-the-right-consistency-level-for-your-application"></a>Vyberte úroveň konzistence správné pro vaši aplikaci

Distribuované databáze spoléhat na replikaci pro vysokou dostupnost, s nízkou latencí nebo obojí, ujistěte se, základní kompromis mezi konzistence čtení a dostupnosti, latence a propustnosti. Většina komerčně dostupný distribuovaných databází požádejte vývojáři zvolit mezi těmito dvěma modely extreme konzistence: silnou konzistenci a konečné konzistence. Azure Cosmos DB umožňuje vývojářům vybrat mezi pět jasně definované modely konzistence: silná, omezená neaktuálnost, relace, konzistentní Předpona a konečný výsledek. Každá z těchto modelů konzistence je dobře definovaný a intuitivní a je možné u konkrétních scénářů reálného světa. Každá z pět modelů konzistence poskytuje jasné dostupnost a výkon kompromisy a je zajištěná komplexní smlouvy SLA. Následující pokyny pro jednoduché můžete učinit správnou volbu v mnoha běžným scénářům.

## <a name="sql-api-or-table-api"></a>Rozhraní SQL API nebo rozhraní Table API

- Pro většinu scénářů reálného světa konzistence typu relace je optimální a je doporučenou možnost. Další informace najdete v tématu [postupy spravovat tokenu relace pro aplikaci](how-to-manage-consistency.md#utilize-session-tokens).
- Pokud vaše aplikace vyžaduje silnou konzistenci, doporučuje se, že používáte úroveň konzistence omezená neaktuálnost.
- Pokud potřebujete přísnější záruky konzistence, než poskytuje jeden milisekund latence a konzistence typu relace pro zápisy, doporučuje se, že používáte úroveň konzistence omezená neaktuálnost.  
- Pokud vaše aplikace vyžaduje konzistenci typu případné, doporučuje se, že používáte úroveň konzistentní předpona konzistence.
- Pokud budete potřebovat méně striktní konzistenci záruky, než poskytuje konzistence typu relace, doporučujeme že použít úroveň konzistentní předpona konzistence.
- Pokud potřebujete nejvyšší možnou dostupnost a nejnižší latenci, použijte úroveň konzistence typu případné.

## <a name="cassandra-mongodb-or-gremlin-api"></a>Cassandra, MongoDB nebo Gremlin API

- Podrobnosti o mapování mezi "Pro čtení úrovně konzistence" úrovní konzistence Apache Cassandra a Cosmos DB najdete v tématu [úrovně konzistence a rozhraní API Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).
- Podrobnosti o mapování mezi "Pro čtení problém" úrovní konzistence MongoDB a Azure Cosmos DB najdete v tématu [úrovně konzistence a rozhraní API Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="you-may-get-stronger-consistency-guarantees-in-practice"></a>Můžete být silnější záruky konzistence v praxi

Záruky konzistence pro operace čtení odpovídají aktuálnosti a stav databáze se požadované pořadí. Konzistence čtení se váže k řazení a šíření operace zápisu (update).  

Pokud je úroveň konzistence nastavená na **omezená neaktuálnost**, Cosmos DB zaručuje, že klienti vždy načíst hodnotu předchozí zápis s lag ohraničena neaktuálnost okno.

Pokud je úroveň konzistence nastavená na **silné**, okno neaktuálnost je ekvivalentní hodnotě nula a klienti je zaručeno, že přečte potvrzená hodnota zápisu.

Pro zbývající tři konzistence úrovně okno neaktuálnost je do značné míry závisí na velikosti pracovní zátěže. Například, pokud neexistují žádné zápisy děje na databázi, operace čtení se **konečné**, **relace**, nebo **konzistentní předpona** úrovně konzistence je pravděpodobně poskytne stejné výsledky jako operace čtení se úroveň silná konzistence.

Pokud váš účet služby Cosmos DB je nakonfigurován pomocí libovolné úrovně konzistence, než je silná konzistence, můžete zjistit pravděpodobnost vaši klienti získat silně konzistentních čtení (linearizable) pro vaše workload(s) zobrazením pravděpodobnostní ohraničená Metrika neaktuálnost (PBS) vystavené na webu Azure Portal [tady uvidíte týkajících se použití metrika PBS](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric). Pravděpodobnostní omezená neaktuálnost ukáže, jak konečné konzistenci typu případné. Tato metrika poskytuje přehled o tom, jak často získáte silnější konzistence než úroveň konzistence, které jste nakonfigurovali v účtu služby Cosmos DB. Jinými slovy zobrazí se pravděpodobnost (znázorněné v milisekundách), získávání silně konzistentních čtení pro zápis i oblastí čtení.

## <a name="next-steps"></a>Další postup

Další informace o úrovních konzistence v následujících článcích:

* [Mapování úrovní konzistence napříč API služby Cosmos DB](consistency-levels-across-apis.md)
* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Jak spravovat tokenu relace pro aplikaci](how-to-manage-consistency.md#utilize-session-tokens)
* [Jak monitorovat metriky Probabilistically omezená Neaktuálnost (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)