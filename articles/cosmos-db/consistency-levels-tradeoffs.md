---
title: Dostupnost a výkon kompromisy pro různé úrovně konzistence ve službě Azure Cosmos DB
description: Dostupnost a výkon kompromisy pro různé úrovně konzistence ve službě Azure Cosmos DB.
keywords: konzistence, výkon, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: af17815d2dcf36909ba9b2109f0f9939c79508c0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848693"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Dostupnost a výkon kompromisy pro různé úrovně konzistence ve službě Azure Cosmos DB

Distribuované databáze, které závisí na replikaci pro vysokou dostupnost a nízkou latencí musíte dělat kompromisy. Nevýhody jsou mezi konzistence čtení a dostupnosti, latence a propustnosti. 

Azure Cosmos DB blíží konzistence dat jako celé spektrum od volby. Tento přístup zahrnuje více možností než dvěma extrémy silné a konečné konzistence. Můžete vybrat z pěti jasně definované modely na spektra konzistence. Od nejsilnější do nejslabší, modely jsou:

- Silné 
- Omezená neaktuálnost 
- Relace 
- Konzistentní předpona 
- Nahodilé 

Každý model poskytuje dostupnost a výkon kompromisy a je zajištěná komplexní smlouvou SLA.

## <a name="consistency-levels-and-latency"></a>Úrovně konzistence a latence

- Latence čtení pro všechny úrovně konzistence je vždycky zaručená být kratší než 10 milisekund na 99. percentilu. Latence čtení je zajištěná smlouva SLA. Průměrná latence na 50. percentil, čtení, je obvykle 2 MS nebo nižší. Účty služby Azure Cosmos span několik oblastí, jež jsou nakonfigurovány s silná konzistence se výjimky této záruky.

-  Latence zápisu pro zbývající úrovně konzistence je vždycky zaručená být kratší než 10 milisekund na 99. percentilu. Latence zápisu je zajištěná smlouva SLA. Zápis průměrnou latenci, na 50. percentil, je obvykle 5 MS nebo nižší.

Některé účty služby Azure Cosmos může mít několik oblastí nakonfigurovanou silnou konzistenci. Latence zápisu je v tomto případě musí být menší než dvěma časy odezvy doba plus 10 milisekund na 99. percentilu. Požadavku mezi dvěma oblastmi nejvíce je přiřazená k vašemu účtu Azure Cosmos. Se rovná hodnotě požadavku mezi dvěma nejvíce oblastí, přidružených k účtu Azure Cosmos. Tato možnost je aktuálně ve verzi preview. 

Přesné čekací doba požadavku je funkce rychlostí světla vzdálenosti a topologii sítě Azure. Sítě Azure neposkytuje žádné latence smlouvy o úrovni služeb pro požadavku mezi všechny dvou oblastech Azure. Pro váš účet Azure Cosmos latenci replikace se zobrazují na webu Azure Portal. Na webu Azure portal můžete použít ke sledování latence replikace mezi různými oblastmi, které jsou spojené s vaším účtem.

## <a name="consistency-levels-and-throughput"></a>Úrovně konzistence a propustnosti

- Pro stejný počet jednotek žádostí zadejte asi dvakrát propustnost čtení při porovnání s silná, ohraničená odolnost relace, konzistentní Předpona a úrovně konzistence typu případné.

- Pro daný typ operace zápisu, jako jsou insert, replace, funkcí upsert a delete zápis propustnost za jednotky žádosti je shodné pro všechny úrovně konzistence.

## <a name="next-steps"></a>Další postup

Další informace o globální distribuci a obecné konzistence kompromisů v distribuovaných systémech. Viz následující články:

* [Konzistence kompromisy v návrhu systémy moderní distribuované databáze](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Vysoká dostupnost](high-availability.md)
* [SLA služby Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
