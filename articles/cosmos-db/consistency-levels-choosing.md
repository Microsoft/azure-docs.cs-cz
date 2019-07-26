---
title: Výběr správné úrovně konzistence pro vaši aplikaci, která používá Azure Cosmos DB
description: Výběr správné úrovně konzistence vaší aplikace v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 89c81e978c5f3dbbb8fac1ea5e75fc506612308f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384898"
---
# <a name="choose-the-right-consistency-level"></a>Volba vhodné úrovně konzistence 

Distribuované databáze spoléhají na replikaci kvůli vysoké dostupnosti, nízké latenci nebo obojímu, se základními kompromisy mezi tím, co je konzistence čtení a dostupností, latencí a propustností. Většina komerčně dostupných distribuovaných databází požádá vývojáře o výběr mezi dvěma extrémními modely konzistence: *silná* konzistence *a* konečná konzistence. Azure Cosmos DB umožňuje vývojářům vybírat z pěti dobře definovaných modelů konzistence: *silná*, *ohraničená neaktuálnost*, *relace*, *konzistentní předpona* a *konečné*. Každý z těchto modelů konzistence je dobře definovaný, intuitivní a dá se použít pro konkrétní scénáře reálného světa. Každý z pěti modelů konzistence poskytuje přesné [dostupnosti a kompromisy na výkon](consistency-levels-tradeoffs.md) a jsou zajištěny prostřednictvím komplexního SLA. Následující jednoduché pokyny vám pomůžou při rozhodování v mnoha běžných scénářích.

## <a name="sql-api-and-table-api"></a>Rozhraní SQL API a rozhraní API pro tabulky

Pokud je vaše aplikace sestavená pomocí rozhraní SQL API nebo rozhraní API pro tabulky, vezměte v úvahu následující body:

- Pro mnoho scénářů reálného světa je konzistence relace optimální a je to doporučená možnost. Další informace najdete v tématu [Správa tokenu relace pro vaši aplikaci](how-to-manage-consistency.md#utilize-session-tokens).

- Pokud vaše aplikace vyžaduje silnou konzistenci, je doporučeno použít úroveň konzistence s ohraničenou kostarou.

- Pokud potřebujete přísnější záruky konzistence než ty, které poskytuje konzistence relace a latence v řádu s jednou číslicí, je doporučeno použít úroveň konzistence s ohraničenou neaktuálností.  

- Pokud vaše aplikace vyžaduje konečnou konzistenci, doporučujeme použít konzistentní úroveň konzistence předpony.

- Pokud potřebujete méně přísné záruky konzistence než ty, které poskytuje konzistence relací, doporučujeme použít konzistentní úroveň konzistence předpony.

- Pokud potřebujete nejvyšší dostupnost a nejnižší latenci, použijte konečnou úroveň konzistence.

- Pokud potřebujete ještě vyšší trvanlivost dat bez obětování výkonu, můžete vytvořit vlastní úroveň konzistence na aplikační vrstvě. Další informace najdete v tématu [implementace vlastní synchronizace ve vašich aplikacích](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Rozhraní API pro Cassandra, MongoDB a Gremlin

- Podrobnosti o mapování mezi "úroveň konzistence čtení" nabídnutou v Apache Cassandra a Cosmos DB úrovní konzistence najdete v tématu [úrovně konzistence a rozhraní API pro Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).

- Podrobnosti o mapování mezi "obavou při čtení" z MongoDB a Azure Cosmos DB úrovní konzistence najdete v tématu [úrovně konzistence a rozhraní API pro Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Záruky konzistence v praxi

V praxi můžete často získat silnější záruky konzistence. Záruky konzistence pro operaci čtení odpovídají aktuálnosti a objednání stavu databáze, který požadujete. Konzistence čtení je vázána na řazení a šíření operací zápisu a aktualizace.  

* Pokud je úroveň konzistence nastavená na zastaralost, Cosmos DB garantuje, že klienti vždycky čtou hodnotu předchozího zápisu s prodlevou, která je ohraničená oknem zastaralosti.

* Pokud je úroveň konzistence nastavena na hodnotu **Strong**, je okno zastaralosti stejné jako nula a klientům je zaručeno, že si přečtou poslední potvrzenou hodnotu operace zápisu.

* Pro zbývající tři úrovně konzistence je okno zastaralosti závislé na vašich úlohách. Například pokud nejsou k dispozici žádné operace zápisu v databázi, může operace čtení s úrovněmi konzistence s případnými, **relací**nebo **konzistentní předponou** vracet stejné výsledky jako operace čtení se silným stupněm konzistence.

Pokud je váš účet Azure Cosmos nakonfigurovaný s úrovní konzistence jinou než silná konzistence, můžete zjistit pravděpodobnost, že klienti mohou získat silné a konzistentní čtení pro vaše úlohy, a to tak, že se podíváte na *probabilistically ohraničené.* Služba PBS (zastaralá) metrika. Tato metrika se zveřejňuje v Azure Portal. Další informace najdete v tématu [monitorování metriky služby PBS (probabilistically Bounded)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Pravděpodobnostní s ohraničenou neaktuálností ukazuje, jak je to vaše konečná konzistence. Tato metrika poskytuje přehled o tom, jak často můžete získat silnější konzistenci než úroveň konzistence, kterou jste v účtu Azure Cosmos aktuálně nakonfigurovali. Jinými slovy můžete zobrazit pravděpodobnost (měřeno v milisekundách) pro získání silně konzistentních čtení pro kombinaci oblastí zápisu a čtení.

## <a name="next-steps"></a>Další postup

Další informace o úrovních konzistence najdete v následujících článcích:

* [Mapování úrovně konzistence napříč Cosmos DBmi rozhraními API](consistency-levels-across-apis.md)
* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Jak spravovat token relace pro vaši aplikaci](how-to-manage-consistency.md#utilize-session-tokens)
* [Monitorovat metriku probabilistically zastaralosti (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
