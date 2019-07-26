---
title: Místní přítomnost pomocí služby Azure Cosmos DB
description: Tento článek vysvětluje, o místní přítomnost služby Azure Cosmos DB a různé cloudové prostředí.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 775ca8c57a9a444df2f7d0a4df4224b7eba9d677
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384037"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Místní přítomnost pomocí služby Azure Cosmos DB

Azure Cosmos DB je základní služba v Azure a ve výchozím nastavení je vždy dostupná ve všech oblastech, kde je Azure k dispozici. V současné době je k dispozici v Azure [54 oblastech](https://azure.microsoft.com/global-infrastructure/regions/) po celém světě. 

[![Oblasti, kde je Azure Cosmos DB k dispozici](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB je k dispozici ve všech pěti různých Azure cloudových prostředích dostupný pro zákazníky:

* **Veřejné Azure** cloudu, což je dostupná globálně.

* **Azure Čína 21Vianet** je k dispozici prostřednictvím jedinečného partnerství mezi společnostmi Microsoft a 21Vianet, jedním z největších poskytovatelů internetových služeb v Číně.

* **Služba Azure Germany** poskytuje služby v rámci modelu zplnomocněnec dat, který zajišťuje tohoto zákazníka data zůstanou v Německu pod kontrolou T-Systems International GmbH – pobočka společnosti Deutsche Telecom, který funguje jako důvěryhodný datový zplnomocněnec.

* **Azure Government** je k dispozici ve čtyřech oblastech ve Spojených státech amerických pro instituce státní správy USA a jejich partnery. 

* **Azure Government pro Ministerstvo obrany (DOD)** je k dispozici ve dvou oblastech v USA do ministerstva obrany USA.

## <a name="regional-presence-with-global-distribution"></a>Místní přítomnost s globální distribucí

Všechna rozhraní API vystavená Azure Cosmos DB (včetně SQL, MongoDB, Cassandra, Gremlin a Table) jsou ve výchozím nastavení k dispozici ve všech oblastech Azure. Například můžete mít rozhraní API MongoDB a Cassandra vystavená Azure Cosmos DB nejen ve všech globálních oblastech Azure, ale také v rámci svrchovaných cloudů, jako je Čína, Německo, státní správa a ministerstvo obrany (DoD).

Azure Cosmos DB je [globálně distribuovaná](distribute-data-globally.md) databázová služba. Váš účet Azure Cosmos můžete přidružit libovolného počtu oblastí Azure a vaše data se automaticky a transparentně replikuje. Můžete přidat nebo odebrat oblasti ke svému účtu Azure Cosmos v každém okamžiku. Možnosti globální distribuce na klíč a protokol zvládli více replikace služby Azure Cosmos DB nabízí méně než 10 ms čtení a zápis latence na 99. percentilu, 99,999 čtení a zápis dostupnosti a schopností pružného škálování zřízené propustnost pro čte a zapisuje napříč všemi oblastmi spojenými s vaším účtem Azure Cosmos. Azure Cosmos DB nabízí také pět jasně definované modely konzistence a můžete také použít konkrétní konzistence model k vašim datům. A konečně Azure Cosmos DB je jediná databázová služba v oboru, která poskytuje komplexní [smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) zahrnující zřízenou propustnost, latenci na 99 percentil, vysokou dostupnost a konzistenci. Výše uvedené funkce jsou dostupné ve všech cloudech Azure.

## <a name="next-steps"></a>Další postup

Nyní se můžete dozvědět o základních konceptech Azure Cosmos DB v následujících článcích:

* [Distribuce globálních dat](distribute-data-globally.md)
* [Jak spravovat účet služby Azure Cosmos DB](manage-account.md)
* [Zřizování propustnosti pro kontejnery Azure Cosmos a databáze](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
