---
title: Regionální přítomnost s Azure Cosmos DB
description: Tento článek vysvětluje místní přítomnost Azure Cosmos DB a různých cloudových prostředích.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753230"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Regionální přítomnost s Azure Cosmos DB

Azure Cosmos DB je základní služba v Azure a ve výchozím nastavení je vždy dostupná ve všech oblastech, kde je Azure k dispozici. V současné době je Azure k dispozici v [54 oblastech](https://azure.microsoft.com/global-infrastructure/regions/) po celém světě. 

[![Oblasti, kde je k dispozici Azure Cosmos DB](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB je k dispozici ve všech pěti různých cloudových prostředích Azure dostupných zákazníkům:

* Veřejný cloud **Azure,** který je dostupný globálně.

* **Azure China 21Vianet** je k dispozici prostřednictvím jedinečného partnerství mezi společností Microsoft a 21Vianet, jedním z největších poskytovatelů internetu v Číně.

* **Azure Germany** poskytuje služby v rámci modelu správce dat, který zajišťuje, že zákaznická data zůstanou v Německu pod kontrolou společnosti T-Systems International GmbH, dceřiné společnosti Deutsche Telecom, která působí jako německý datový správce.

* **Azure Government** je k dispozici ve čtyřech oblastech ve Spojených státech pro vládní agentury USA a jejich partnery. 

* **Azure Government for Department of Defense (DoD)** je k dispozici ve dvou oblastech ve Spojených státech ministerstvu obrany USA.

## <a name="regional-presence-with-global-distribution"></a>Regionální přítomnost s globální distribucí

Všechna rozhraní API vystavená službou Azure Cosmos DB (včetně SQL, MongoDB, Cassandra, Gremlin a Table) jsou ve výchozím nastavení dostupná ve všech oblastech Azure. Například můžete mít MongoDB a Cassandra rozhraní API vystavené Azure Cosmos DB nejen ve všech globálních oblastech Azure, ale také v suverénních cloudech, jako je Čína, Německo, vláda a ministerstvo obrany (DoD).

Azure Cosmos DB je [globálně distribuovaná](distribute-data-globally.md) databázová služba. K účtu Azure Cosmos můžete přidružit libovolný počet oblastí Azure a vaše data se automaticky a transparentně replikují. Oblast můžete přidat nebo odebrat do svého účtu Azure Cosmos kdykoli. Díky možnosti globální distribuce na klíč a víceúčelovému replikačnímu protokolu nabízí Azure Cosmos DB méně než 10 ms latence čtení a zápisu na 99 percentilu, 99.999 dostupnost čtení a zápisu a schopnost pružně škálovat zřízená propustnost pro čtení a zápisy ve všech oblastech přidružených k vašemu účtu Azure Cosmos. Azure Cosmos DB, nabízí také pět dobře definované modely konzistence a můžete použít konkrétní model konzistence pro vaše data. Nakonec Azure Cosmos DB je jediná databázová služba v oboru, která poskytuje komplexní [smlouvu o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) zahrnující zřízenou propustnost, latenci na 99 percentilu, vysokou dostupnost a konzistenci. Výše uvedené funkce jsou k dispozici ve všech cloudech Azure.

## <a name="next-steps"></a>Další kroky

Teď se můžete dozvědět o základních konceptech Azure Cosmos DB s následujícími články:

* [Globální distribuce dat](distribute-data-globally.md)
* [Jak spravovat účet Azure Cosmos DB](manage-account.md)
* [Zřizovací propustnost pro kontejnery a databáze Azure Cosmos](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
