---
title: Globální distribuce dat pomocí Azure Cosmos DB
description: Přečtěte si o globálním geografickou replikaci, vícenásobné replikaci, převzetí služeb při selhání a obnovení dat pomocí globálních databází z Azure Cosmos DB globálně distribuovaná databázová služba pro více modelů.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 84cd201cd758293082a61a87528332c7d8c58811
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264301"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Globální distribuce dat pomocí Azure Cosmos DB – přehled

V dnešních aplikacích je nutné, aby byly vysoce reagovaly a vždy online. Aby bylo možné dosáhnout nízké latence a vysoké dostupnosti, je třeba instance těchto aplikací nasadit v datových centrech, které jsou blízko jejich uživatelů. Tyto aplikace jsou obvykle nasazeny v několika datových centrech a jsou označovány jako globálně distribuované. Globálně distribuované aplikace potřebují globálně distribuovanou databázi, která může transparentně replikovat data kdekoli na světě a umožnit tak aplikacím pracovat na kopii dat, která se blíží uživatelům. 

Azure Cosmos DB je globálně distribuovaná databázová služba, která je navržená tak, aby poskytovala nízkou latenci, elastickou škálovatelnost propustnosti, jasně definovanou sémantiku pro konzistenci dat a vysokou dostupnost. Krátce, pokud vaše aplikace potřebuje zaručit rychlou dobu odezvy kdekoliv na světě, pokud je potřeba, aby byla vždycky online, a potřebuje neomezenou a pružnou škálovatelnost propustnosti a úložiště, měli byste sestavit aplikaci na Azure Cosmos DB.

Své databáze můžete nakonfigurovat tak, aby byly globálně distribuované a dostupné v libovolné oblasti Azure. Pokud chcete snížit latenci, umístěte data blízko do místa, kde jsou vaši uživatelé. Výběr požadovaných oblastí závisí na globálním dosahu vaší aplikace a na umístění uživatelů. Cosmos DB transparentně replikuje data do všech oblastí přidružených k vašemu účtu Cosmos. Poskytuje jedinou systémovou bitovou kopii vaší globálně distribuované databáze a kontejnerů Azure Cosmos, které může vaše aplikace číst a zapisovat do místního počítače. 

Pomocí Azure Cosmos DB můžete kdykoli přidat nebo odebrat oblasti přidružené k vašemu účtu. K přidání nebo odebrání oblasti se nevyžaduje pozastavení ani opětovné nasazení vaší aplikace. I nadále bude mít vysoce dostupný čas v důsledku možností více domovských stránek, které služba nativně poskytuje.

![Vysoce dostupná topologie nasazení](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Klíčové výhody globální distribuce

**Sestavování globálních aktivních – aktivních aplikací.** Díky novému protokolu replikace s více hlavními servery podporuje každá oblast i zápis a čtení. Funkce s více hlavními servery také umožňuje:

- Neomezený pružný zápis a škálovatelnost pro čtení. 
- 99,999% dostupnost čtení a zápisu všech po celém světě.
- Garantuje čtení a zápisy poskytované za méně než 10 milisekund v 99 percentilu.

Pomocí Azure Cosmos DB rozhraní API pro více domovských míst vaše aplikace je vědomá nejbližší oblasti a může odesílat žádosti do této oblasti. Nejbližší oblast je identifikována bez jakýchkoli změn konfigurace. Při přidávání a odebírání oblastí do a z účtu Azure Cosmos není nutné aplikaci znovu nasadit ani pozastavit, stále je vysoce dostupná.

**Vytvářejte vysoce reagující aplikace.** Vaše aplikace může provádět čtení a zápis téměř v reálném čase ve všech oblastech, které jste zvolili pro vaši databázi. Azure Cosmos DB interně zpracovává replikaci dat mezi oblastmi a zárukami na úrovni konzistence zvolené úrovně.

**Vytvářejte vysoce dostupné aplikace.** Spuštění databáze ve více oblastech po celém světě zvyšuje dostupnost databáze. Pokud je jedna oblast nedostupná, jiné oblasti automaticky zpracovávají požadavky aplikací. Azure Cosmos DB nabízí 99,999% dostupnost čtení a zápisu pro databáze ve více oblastech.

**Udržujte kontinuitu podnikových aplikací během regionálních výpadků.** Azure Cosmos DB podporuje [automatické převzetí služeb při selhání](how-to-manage-database-account.md#automatic-failover) při regionálním výpadku. Během regionálního výpadku Azure Cosmos DB nadále udržovat latenci, dostupnost, konzistenci a SLA propustnost. Aby bylo možné zajistit vysokou dostupnost celé aplikace, Cosmos DB nabízí rozhraní API pro ruční převzetí služeb při selhání pro simulaci oblasti výpadku regionu. Pomocí tohoto rozhraní API můžete provádět běžné cvičení provozní kontinuity.

**Globální škálování a propustnost čtení a zápisu** Každé oblasti můžete povolit zapisovatelné a elasticky škálovat čtení a zápisy po celém světě. Propustnost, kterou vaše aplikace konfiguruje v databázi Azure Cosmos nebo v kontejneru, je zaručená pro doručování ve všech oblastech přidružených k vašemu účtu Azure Cosmos. Zajištěná propustnost je zaručena podle [finančně zálohovaných SLA](https://aka.ms/acdbsla).

**Vyberte si z několika dobře definovaných modelů konzistence.** Protokol replikace Azure Cosmos DB nabízí pět dobře definovaných, praktických a intuitivních modelů konzistence. Každý model má kompromisy mezi konzistencí a výkonem. Tyto modely konzistence slouží k snadnému sestavování globálně distribuovaných aplikací.

## <a id="Next Steps"></a>Další kroky

Přečtěte si další informace o globální distribuci v následujících článcích:

* [Globální distribuce – pod kapotou](global-dist-under-the-hood.md)
* [Jak v aplikacích nakonfigurovat více hlavních serverů](how-to-multi-master.md)
* [Konfigurace klientů pro vícedomé služby](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Přidat nebo odebrat oblasti z Azure Cosmos DB účtu](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Vytvoření vlastních zásad řešení konfliktů pro účty rozhraní SQL API](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programovatelné modely konzistence v Cosmos DB](consistency-levels.md)
* [Vyberte úroveň konzistence správné pro vaši aplikaci](consistency-levels-choosing.md)
* [Úrovně konzistence napříč API služby Azure Cosmos DB](consistency-levels-across-apis.md)
* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)

