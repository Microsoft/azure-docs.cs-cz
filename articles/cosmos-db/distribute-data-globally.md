---
title: Globální distribuce dat pomocí Služby Azure Cosmos DB
description: Seznamte se s geografickou replikací v měřítku planety, vícenásobným serverem, převzetím služeb při selhání a obnovením dat pomocí globálních databází z Azure Cosmos DB, globálně distribuované databázové služby s více modely.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: ef511c203caa11aad4dea1047c982158810c4038
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982119"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Globální distribuce dat pomocí Azure Cosmos DB – přehled.

Dnešní aplikace musí být vysoce citlivé a vždy online. Chcete-li dosáhnout nízké latence a vysoké dostupnosti, instance těchto aplikací je třeba nasadit v datových centrech, které jsou blízko k jejich uživatelům. Tyto aplikace se obvykle nasazují ve více datových centrech a jsou volány globálně distribuované. Globálně distribuované aplikace potřebují globálně distribuovanou databázi, která dokáže transparentně replikovat data po celém světě, aby mohly pracovat s kopií dat, která je blízko uživatelům. 

Azure Cosmos DB je globálně distribuovaná databázová služba, která je navržena tak, aby poskytovala nízkou latenci, elastickou škálovatelnost propustnosti, dobře definovanou sémantiku konzistenci dat a vysokou dostupnost. Stručně řečeno, pokud vaše aplikace potřebuje zaručenou rychlou dobu odezvy kdekoli na světě, pokud je to nutné být vždy online a potřebuje neomezenou a elastickou škálovatelnost propustnosti a úložiště, měli byste vytvořit aplikaci na Azure Cosmos DB.

Databáze můžete nakonfigurovat tak, aby byly globálně distribuované a dostupné v kterékoli oblasti Azure. Chcete-li snížit latenci, umístěte data blízko místa, kde jsou vaši uživatelé. Výběr požadovaných oblastí závisí na globálním dosahu vaší aplikace a na tom, kde se uživatelé nacházejí. Cosmos DB transparentně replikuje data do všech oblastí přidružených k vašemu účtu Cosmos. Poskytuje jednu bitovou kopii systému globálně distribuované databáze Azure Cosmos a kontejnery, které vaše aplikace můžete číst a zapisovat místně. 

Pomocí Azure Cosmos DB můžete kdykoli přidat nebo odebrat oblasti přidružené k vašemu účtu. Aplikace nemusí být pozastavena nebo znovu nasazené přidat nebo odebrat oblast. Je stále velmi dostupná po celou dobu z důvodu více-naváděcí schopnosti, které služba nativně poskytuje.

![Vysoce dostupná topologie nasazení](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Klíčové výhody globální distribuce

**Vytvářejte globální aktivní aktivní aplikace.** S jeho nový multi-master replikační protokol, každá oblast podporuje zápisy a čtení. Funkce více hlavních masterů také umožňuje:

- Neomezená elastická škálovatelnost zápisu a čtení. 
- 99.999% dostupnost čtení a zápisu po celém světě.
- Zaručené čtení a zápisy podávané v méně než 10 milisekund v percentilu 99.

Pomocí Azure Cosmos DB multi-naváděcí API, vaše aplikace si je vědoma nejbližší oblasti a můžete odesílat požadavky do této oblasti. Nejbližší oblast je identifikována bez jakýchkoli změn konfigurace. Při přidávání a odebírání oblastí do a z vašeho účtu Azure Cosmos, vaše aplikace není nutné znovu nasadit nebo pozastavena, bude stále velmi dostupné po celou dobu.

**Vytvářejte vysoce responzivní aplikace.** Aplikace může provádět čtení a zápisy téměř v reálném čase proti všem oblastem, které jste pro databázi zvolili. Azure Cosmos DB interně zpracovává replikaci dat mezi oblastmi se zárukami úrovně konzistence vybrané úrovně.

**Vytvářejte vysoce dostupné aplikace.** Spuštění databáze ve více oblastech po celém světě zvyšuje dostupnost databáze. Pokud jedna oblast není k dispozici, ostatní oblasti automaticky zpracovávají požadavky aplikací. Azure Cosmos DB nabízí 99,999 % dostupnost i pro čtení a zápis pro databáze s více oblastmi.

**Udržujte kontinuitu provozu během regionálních výpadků.** Azure Cosmos DB podporuje [automatické převzetí služeb při selhání](how-to-manage-database-account.md#automatic-failover) během místnívývývý výpadek. Během místnívýpadrnosti azure cosmos DB nadále udržovat svou latenci, dostupnost, konzistenci a propustnost SLA. Chcete-li zajistit, že celá aplikace je vysoce dostupná, Cosmos DB nabízí ruční rozhraní API pro převzetí služeb při selhání simulovat místní výpadek. Pomocí tohoto rozhraní API můžete provádět pravidelné vrtáky kontinuity provozu.

**Globální propustnost čtení a zápisu škálujte.** Můžete povolit, aby každá oblast byla zapisovatelná a elasticky škálovat čtení a zápisy po celém světě. Propustnost, kterou vaše aplikace konfiguruje v databázi Azure Cosmos nebo v kontejneru, se zaručeně bude doručovat do všech oblastí přidružených k vašemu účtu Azure Cosmos. Zřízená propustnost je garantována [finančně zajištěnými sla](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

**Vyberte si z několika dobře definovaných modelů konzistence.** Replikační protokol Azure Cosmos DB nabízí pět dobře definovaných, praktických a intuitivních modelů konzistence. Každý model má kompromis mezi konzistence a výkonu. Tyto modely konzistence slouží k snadnému vytváření globálně distribuovaných aplikací.

## <a name="next-steps"></a><a id="Next Steps"></a>Další kroky

Další informace o globální distribuci naleznete v následujících článcích:

* [Globální distribuce – pod pokličkou](global-dist-under-the-hood.md)
* [Jak nakonfigurovat multimaster ve vašich aplikacích](how-to-multi-master.md)
* [Konfigurace klientů pro multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Přidání nebo odebrání oblastí z účtu Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Vytvoření vlastní zásady řešení konfliktů pro účty rozhraní SQL API](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programovatelné modely konzistence v Cosmos DB](consistency-levels.md)
* [Zvolte správnou úroveň konzistence pro vaši aplikaci](consistency-levels-choosing.md)
* [Úrovně konzistence mezi api DB Služby Azure Cosmos](consistency-levels-across-apis.md)
* [Kompromisy dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md)

