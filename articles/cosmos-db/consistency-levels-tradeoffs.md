---
title: Dostupnost a výkon kompromisy pro různé úrovně konzistence | Dokumentace Microsoftu
description: Dostupnost a výkon kompromisy pro různé úrovně konzistence ve službě Azure Cosmos DB.
keywords: konzistence, výkon, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 061a7c223d0e1c6fa7384d7defe9f84f470236ce
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244070"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels"></a>Dostupnost a výkon kompromisy pro různé úrovně konzistence

Distribuované databáze spoléhat na replikaci pro vysokou dostupnost, s nízkou latencí nebo obojí, ujistěte se, základní kompromis mezi konzistence čtení vs. availability1, latency2 a propustnost. Azure Cosmos DB blíží konzistence dat jako celé spektrum od volby místo dvěma extrémy silné a konečné konzistence. Cosmos DB umožňuje vývojářům vybrat mezi pět jasně definované modely konzistence ze spektra konzistence (nejsilnější do nejslabší) – **silné**, **omezená neaktuálnost**, **relace** , **konzistentní předpona**, a **konečné**. Každá z pět modelů konzistence poskytuje jasné dostupnost a výkon kompromisy a je zajištěná komplexní smlouvy SLA.

## <a name="consistency-levels-and-latency"></a>Úrovně konzistence a latence

- **Latence čtení** pro všechny úrovně konzistence je vždycky zaručená být kratší než 10 milisekund na 99. percentilu a je zajištěná smlouva SLA. Průměr (na 50. percentil), přečtěte si, latence je obvykle 2 MS nebo nižší.
- S výjimkou účty Cosmos pokrývající několik oblastí a nakonfigurovanou silnou konzistenci **latence zápisu** pro všechny úrovně konzistence je vždycky zaručená být kratší než 10 milisekund na 99. percentilu a tímto modulem stojí SMLOUVA SLA. Latence zápisu průměrné (na 50. percentil), je obvykle 5 MS nebo nižší.
- Pro účty Cosmos s několika oblastech nakonfigurovanou silnou konzistenci (aktuálně ve verzi Preview) **latence zápisu** je zaručeno, že bude menší než < (2 * požadavku) + 10 milisekund na 99. percentilu. Požadavku mezi nejvíce kterékoli dvě oblasti přidružené k účtu Cosmos. Přesné čekací doba požadavku je funkce rychlost z světla vzdálenosti a přesně topologii sítí Azure. Sítě Azure neposkytuje žádné latence smlouvy o úrovni služeb pro požadavku mezi všechny dvou oblastech Azure. Latence replikace cosmos DB se zobrazují na webu Azure portal pro váš účet Cosmos umožňuje sledovat latenci replikace mezi různými oblastmi spojenými s vaším účtem Cosmos.

## <a name="consistency-levels-and-throughput"></a>Úrovně konzistence a propustnosti

- Pro stejné množství RU relace, konzistentní Předpona a konečné konzistence zajišťuje přibližně 2 X čtení propustnosti ve srovnání s silná, ohraničená odolnost.
- Pro daný typ operace zápisu (například Insert, Replace, Upsert, odstranění atd.) propustnost zápisu ru je shodné pro všechny úrovně konzistence.

## <a name="consistency-levels-and-durability"></a>Úrovně konzistence a odolnosti

Předtím, než je potvrzení operace zápisu do klienta data odolném potvrzení na podle kvorum replik v rámci oblasti přijímá zápisy. Kromě toho Pokud kontejner má nakonfigurovanou konzistentní zásady indexování, index je aktualizovat také synchronně replikované a odolném potvrzení kvorum replik, než je potvrzení zápisu ke klientovi. 

Následující tabulka shrnuje okno potenciální ztráty dat v případě havárie místní účty Cosmos pokrývající několik oblastí.

| **Úrovně konzistence** | **Okno potenciální ztráty dat v případě regionálního** |
| - | - |
| Silné | Nula |
| Omezená neaktuálnost | Omezit na "neaktuálnost okno" nakonfiguroval pro vývojáře na účtu Cosmos |
| Relace | Až 5 sekund |
| Konzistentní předpona | Až 5 sekund |
| Nahodilé | Až 5 sekund |

## <a name="next-steps"></a>Další postup

Dále můžete další informace o globální distribuci a obecné konzistence kompromisů v distribuovaných systémech pomocí následujících článcích:

* [Konzistence kompromisy v návrhu systémy moderní distribuované databáze](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Vysoká dostupnost](high-availability.md)
* [Smlouva SLA cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
