---
title: Výběr správné úrovně konzistence pro vaši aplikaci Azure Cosmos DB
description: Výběr správné úrovně konzistence pro vaši aplikaci v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: cea157e272a2bf464141e592b8e742f314a83233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441923"
---
# <a name="choose-the-right-consistency-level"></a>Volba vhodné úrovně konzistence 

Distribuované databáze spoléhající na replikaci pro vysokou dostupnost, nízkou latenci nebo obojí, aby základní kompromis mezi konzistence čtení vs dostupnost, latence a propustnost. Většina komerčně dostupných distribuovaných databází žádá vývojáře, aby si vybrali mezi dvěma modely extrémní konzistence: *silnou* konzistenci a *konečnou* konzistenci. Azure Cosmos DB umožňuje vývojářům vybrat si z pěti dobře definovaných modelů konzistence: *silná*, *ohraničená neaktuálnost*, *relace*, *konzistentní předpona* a *případná*. Každý z těchto modelů konzistence je dobře definovaný, intuitivní a lze jej použít pro konkrétní reálné scénáře. Každý z pěti modelů konzistence poskytuje přesné [kompromisy v oblasti dostupnosti a výkonu](consistency-levels-tradeoffs.md) a je podpořen komplexními sadami SLA. Následující jednoduché aspekty vám pomohou správně se rozhodnout v mnoha běžných scénářích.

## <a name="sql-api-and-table-api"></a>Rozhraní API SQL A ROZHRANÍ API pro tabulky

Zvažte následující body, pokud je vaše aplikace vytvořena pomocí rozhraní SQL API nebo table api:

- Pro mnoho reálných scénářů je konzistence relace optimální a je to doporučená možnost. Další informace naleznete v [tématu How-to manage session token pro vaši aplikaci](how-to-manage-consistency.md#utilize-session-tokens).

- Pokud vaše aplikace vyžaduje silnou konzistenci, doporučujeme použít úroveň konzistence ohraničené neaktuálnosti.

- Pokud potřebujete přísnější záruky konzistence než ty, které poskytuje konzistence relace a jednociferné milisekundy latence pro zápisy, je doporučeno použít úroveň konzistence ohraničené neaktuálnost.  

- Pokud vaše aplikace vyžaduje konečnou konzistenci, doporučujeme použít konzistentní úroveň konzistence předpony.

- Pokud potřebujete méně přísné záruky konzistence než ty, které poskytuje konzistence relace, doporučujeme použít konzistentní úroveň konzistence předpony.

- Pokud potřebujete nejvyšší dostupnost a nejnižší latenci, použijte úroveň případné konzistence.

- Pokud potřebujete ještě vyšší odolnost dat bez snížení výkonu, můžete vytvořit vlastní úroveň konzistence v aplikační vrstvě. Další informace naleznete v [tématu Jak implementovat vlastní synchronizaci ve vašich aplikacích](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra, MongoDB a Gremlin API

- Podrobnosti o mapování mezi "Úroveň konzistence čtení" nabízené v Apache Cassandra a Cosmos DB úrovně konzistence, naleznete v [tématu úrovně konzistence a Cosmos DB API](consistency-levels-across-apis.md#cassandra-mapping).

- Podrobnosti o mapování mezi "Read Concern" úrovně konzistence MongoDB a Azure Cosmos DB, najdete v [tématu úrovně konzistence a Cosmos DB API](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Záruky konzistentnosti v praxi

V praxi můžete často získat silnější záruky konzistence. Záruky konzistence pro operaci čtení odpovídají čerstvosti a řazení stavu databáze, který požadujete. Konzistence čtení je vázána na řazení a šíření operací zápisu a aktualizace.  

* Pokud je úroveň konzistence nastavena na **ohraničenou neaktuálnost**, Cosmos DB zaručuje, že klienti vždy číst hodnotu předchozího zápisu, s prodlevou ohraničené okno neaktuálnosti.

* Pokud je úroveň konzistence nastavena na **hodnotu silnou**, je okno neaktuálnosti ekvivalentní nule a klienti mají zaručeno, že budou číst nejnovější potvrzenou hodnotu operace zápisu.

* Pro zbývající tři úrovně konzistence okno neaktuálnost je do značné míry závislá na zatížení. Například pokud neexistují žádné operace zápisu v databázi, operace čtení s **případné**, **relace**, nebo konzistentní úrovně **konzistence předpony** je pravděpodobné, že přinese stejné výsledky jako operace čtení se silnou úroveň konzistence.

Pokud váš účet Azure Cosmos je nakonfigurovaný s úrovní konzistence než silné konzistence, můžete zjistit pravděpodobnost, že vaši klienti získat silné a konzistentní čtení pro vaše úlohy při pohledu na *probabilistically ohraničené staleness* (PBS) metrika. Tato metrika je vystavena na webu Azure Portal, další informace najdete v [tématu Monitor Probabilistically Ohraničené staleness (PBS) metrika](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Pravděpodobnostní ohraničená zatuchlost ukazuje, jak konečná je vaše konečná konzistence. Tato metrika poskytuje přehled o tom, jak často můžete získat silnější konzistenci než úroveň konzistence, kterou jste aktuálně nakonfigurovali na vašem účtu Azure Cosmos. Jinými slovy můžete zobrazit pravděpodobnost (měřeno v milisekundách) získání silně konzistentní čtení pro kombinaci zápisu a čtení oblastí.

## <a name="next-steps"></a>Další kroky

Další informace o úrovních konzistence naleznete v následujících článcích:

* [Mapování úrovně konzistence mezi porty Cosmos DB](consistency-levels-across-apis.md)
* [Kompromisy dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Správa tokenu relace pro vaši aplikaci](how-to-manage-consistency.md#utilize-session-tokens)
* [Monitorování metriky Pravděpodobnostně omezená neaktuálnost (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
