---
title: Místní přítomnost pomocí služby Azure Cosmos DB
description: Tento článek vysvětluje, o místní přítomnost služby Azure Cosmos DB a různé cloudové prostředí.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 32e69be95226d06f55d79ff5e245c76c3087efd9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461283"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Místní přítomnost pomocí služby Azure Cosmos DB

V současné době je k dispozici v Azure [54 oblastech](https://azure.microsoft.com/global-infrastructure/regions/) po celém světě. Azure Cosmos DB je podkladovou službu v Azure a je k dispozici ve všech oblastech, kde je Azure k dispozici.

[![Oblastech, kde je k dispozici služby Azure Cosmos DB](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB je k dispozici ve všech pěti různých Azure cloudových prostředích dostupný pro zákazníky:

* **Veřejné Azure** cloudu, což je dostupná globálně.

* **Azure Čína** je díky jedinečnému partnerství mezi společnostmi Microsoft a 21Vianet, jedním z největších poskytovatelé Internetu v zemi k dispozici.

* **Služba Azure Germany** poskytuje služby v rámci modelu zplnomocněnec dat, který zajišťuje tohoto zákazníka data zůstanou v Německu pod kontrolou T-Systems International GmbH – pobočka společnosti Deutsche Telecom, který funguje jako důvěryhodný datový zplnomocněnec.

* **Azure Government** je k dispozici ve čtyřech oblastech ve Spojených státech amerických pro instituce státní správy USA a jejich partnery. 

* **Azure Government pro oddělení nuclear Information,** je k dispozici ve dvou oblastech v USA ministerstva obrany USA.

## <a name="regional-presence-with-global-distribution"></a>Místní přítomnost s globální distribucí

Různých rozhraní API, které jsou vystavené (včetně SQL, MongoDB, Cassandra, Gremlin a Azure Table storage) služby Azure Cosmos DB jsou k dispozici ve všech oblastech Azure. Například můžete mít MongoDB a vystavit rozhraní Cassandra API ve službě Azure Cosmos DB nejen ve všech globálními oblastmi Azure, ale také v Azure suverénních oblastech, jako je Čína, Německo, Government a ministerstva obrany oblastech (Military, DoD).

Azure Cosmos DB je [globálně distribuované](distribute-data-globally.md) databáze. Váš účet Azure Cosmos můžete přidružit libovolného počtu oblastí Azure a vaše data se automaticky a transparentně replikuje. Můžete přidat nebo odebrat oblasti ke svému účtu Azure Cosmos v každém okamžiku. Možnosti globální distribuce na klíč a protokol zvládli více replikace služby Azure Cosmos DB nabízí méně než 10 ms čtení a zápis latence na 99. percentilu, 99,999 čtení a zápis dostupnosti a schopností pružného škálování zřízené propustnost pro čte a zapisuje napříč všemi oblastmi spojenými s vaším účtem Azure Cosmos. Azure Cosmos DB nabízí také pět jasně definované modely konzistence a můžete také použít konkrétní konzistence model k vašim datům. A konečně Azure Cosmos DB je jediná služba databáze v tomto odvětví, která poskytuje komplexní smlouva o úrovni služeb (SLA) včetně zřízená propustnost, latenci na úrovni 99. percentilu, vysokou dostupnost a konzistence.

## <a name="next-steps"></a>Další postup

Teď informace o různých koncepty služby Azure Cosmos DB najdete v následujících článcích:

* [Distribuce globálních dat](distribute-data-globally.md)
* [Jak spravovat účet služby Azure Cosmos DB](manage-account.md)
* [Zřizování propustnosti pro kontejnery Azure Cosmos a databáze](set-throughput.md)
* [Smlouva SLA cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
