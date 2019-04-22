---
title: Místní přítomnost pomocí služby Azure Cosmos DB
description: Tento článek vysvětluje, o místní přítomnost služby Azure Cosmos DB a různé cloudové prostředí.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 851da72a308b164b9a01b7ccfa3d541fc06810ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762187"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Místní přítomnost pomocí služby Azure Cosmos DB

Azure Cosmos DB je podkladovou službu v Azure a ve výchozím nastavení, je vždy k dispozici ve všech oblastech, kde je Azure k dispozici. V současné době je k dispozici v Azure [54 oblastech](https://azure.microsoft.com/global-infrastructure/regions/) po celém světě. 

[![Oblastech, kde je k dispozici služby Azure Cosmos DB](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB je k dispozici ve všech pěti různých Azure cloudových prostředích dostupný pro zákazníky:

* **Veřejné Azure** cloudu, což je dostupná globálně.

* **Azure China 21Vianet** je k dispozici díky jedinečnému partnerství mezi společnostmi Microsoft a 21Vianet, jedním z země největších poskytovatelů internetových služeb v Číně.

* **Služba Azure Germany** poskytuje služby v rámci modelu zplnomocněnec dat, který zajišťuje tohoto zákazníka data zůstanou v Německu pod kontrolou T-Systems International GmbH – pobočka společnosti Deutsche Telecom, který funguje jako důvěryhodný datový zplnomocněnec.

* **Azure Government** je k dispozici ve čtyřech oblastech ve Spojených státech amerických pro instituce státní správy USA a jejich partnery. 

* **Azure Government pro Ministerstvo obrany (DoD)** je k dispozici ve dvou oblastech v USA ministerstva obrany USA.

## <a name="regional-presence-with-global-distribution"></a>Místní přítomnost s globální distribucí

Ve všech oblastech Azure ve výchozím nastavení jsou k dispozici všechna rozhraní API vystavený službou Azure Cosmos DB (včetně SQL, MongoDB, Cassandra, Gremlin a tabulky). Například můžete mít MongoDB a vystavit rozhraní Cassandra API ve službě Azure Cosmos DB nejen ve všech globálními oblastmi Azure, ale také v suverénních cloudech, jako je Čína, Německo, Government a ministerstva obrany oblastech (Military, DoD).

Azure Cosmos DB je [globálně distribuované](distribute-data-globally.md) databázové služby. Váš účet Azure Cosmos můžete přidružit libovolného počtu oblastí Azure a vaše data se automaticky a transparentně replikuje. Můžete přidat nebo odebrat oblasti ke svému účtu Azure Cosmos v každém okamžiku. Možnosti globální distribuce na klíč a protokol zvládli více replikace služby Azure Cosmos DB nabízí méně než 10 ms čtení a zápis latence na 99. percentilu, 99,999 čtení a zápis dostupnosti a schopností pružného škálování zřízené propustnost pro čte a zapisuje napříč všemi oblastmi spojenými s vaším účtem Azure Cosmos. Azure Cosmos DB nabízí také pět jasně definované modely konzistence a můžete také použít konkrétní konzistence model k vašim datům. A konečně, Azure Cosmos DB je jediná služba databáze v tomto odvětví, která poskytuje komplexní [smlouva o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) včetně zřízené propustnosti, latence na 99. percentilu, vysokou dostupnost, a konzistence. Výše uvedené možnosti jsou dostupné ve všech cloudech Azure.

## <a name="next-steps"></a>Další postup

Nyní se můžete dozvědět o základních konceptech služby Azure Cosmos DB najdete v následujících článcích:

* [Distribuce globálních dat](distribute-data-globally.md)
* [Jak spravovat účet služby Azure Cosmos DB](manage-account.md)
* [Zřizování propustnosti pro kontejnery Azure Cosmos a databáze](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
