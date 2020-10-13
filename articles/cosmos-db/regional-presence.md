---
title: Regionální přítomnost s Azure Cosmos DB
description: V tomto článku se dozvíte o regionálních Azure Cosmos DB a různých cloudových prostředích.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: e2242e8febad5d55813721266f1286250af47111
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082881"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Regionální přítomnost s Azure Cosmos DB

Azure Cosmos DB je základní služba v Azure a ve výchozím nastavení je vždy dostupná ve [všech oblastech, kde je Azure k dispozici](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all).

:::image type="content" source="./media/regional-presence/regional-presence.png" alt-text="Oblasti, kde je Azure Cosmos DB k dispozici" lightbox="./media/regional-presence/regional-presence.png" border="false":::

Cosmos DB je k dispozici ve všech pěti různých cloudových prostředích Azure dostupných zákazníkům:

* **Veřejný cloud Azure** , který je k dispozici globálně.

* **Azure Čína 21Vianet** je k dispozici prostřednictvím jedinečného partnerství mezi společnostmi Microsoft a 21Vianet, jedním z největších poskytovatelů internetových služeb v Číně.

* **Azure Německo** poskytuje služby v rámci modelu důvěryhodného data, který zajišťuje, že zákaznická data zůstanou v Německu pod kontrolou T-Systems International GmbH, dceřinou společností pro německé Telekom, která funguje jako důvěryhodný datový zplnomocněnec.

* **Azure Government** je k dispozici ve čtyřech oblastech v USA státním úřadům USA a jejich partnerům. 

* **Azure Government pro Ministerstvo obrany (DOD)** je k dispozici ve dvou oblastech v USA do ministerstva obrany USA.

## <a name="regional-presence-with-global-distribution"></a>Regionální přítomnost s globální distribucí

Všechna rozhraní API vystavená Azure Cosmos DB (včetně SQL, MongoDB, Cassandra, Gremlin a Table) jsou ve výchozím nastavení k dispozici ve všech oblastech Azure. Například můžete mít rozhraní API MongoDB a Cassandra vystavená Azure Cosmos DB nejen ve všech globálních oblastech Azure, ale také v rámci svrchovaných cloudů, jako je Čína, Německo, státní správa a ministerstvo obrany (DoD).

Azure Cosmos DB je [globálně distribuovaná](distribute-data-globally.md) databázová služba. K vašemu účtu Azure Cosmos můžete přidružit libovolný počet oblastí Azure a vaše data se automaticky a transparentně replikují. Můžete kdykoli přidat nebo odebrat oblast pro účet Azure Cosmos. Díky možnosti globální distribuce klíč a protokolu replikace s více hlavními servery nabízí Azure Cosmos DB k dispozici méně než 10 MS čtení a zpoždění zápisu na 99 percentilu, 99,999 čtení a zápis a možnost elasticky škálovat zřízenou propustnost pro čtení a zápisy ve všech oblastech přidružených k vašemu účtu Azure Cosmos. Azure Cosmos DB také nabízí pět jasně definovaných modelů konzistence a můžete zvolit, aby pro vaše data byl použit konkrétní model konzistence. A konečně Azure Cosmos DB je jediná databázová služba v oboru, která poskytuje komplexní [smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) zahrnující zřízenou propustnost, latenci na 99 percentil, vysokou dostupnost a konzistenci. Výše uvedené funkce jsou dostupné ve všech cloudech Azure.

Podpora globální replikace je vymezená v rámci cloudového prostředí Azure. Například účty Azure Cosmos DB v *Azure Public* se můžou replikovat do jakékoli oblasti Azure v rámci *Azure Public* – ale nedají se replikovat do oblastí Azure v *Azure Čína 21Vianet*.

## <a name="next-steps"></a>Další kroky

Nyní se můžete dozvědět o základních konceptech Azure Cosmos DB v následujících článcích:

* [Globální distribuce dat](distribute-data-globally.md)
* [Jak spravovat účet Azure Cosmos DB](manage-account.md)
* [Zřízení propustnosti pro kontejnery a databáze Azure Cosmos](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
