---
title: Globální distribuce dat pomocí služby Azure Cosmos DB
description: Další informace o globálním měřítku geografickou replikaci, více hlavních databází, převzetí služeb při selhání a obnovení dat pomocí globálních databází ze služby Azure Cosmos DB, globálně distribuovaná a vícemodelová databázová služba.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: e58a8cd286e4d416dd5f4e6d3fddedf1897fed1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65954171"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Distribuce globálních dat pomocí služby Azure Cosmos DB – přehled

Dnešní aplikace musí být vždy online a s velmi rychlou odezvou. Pokud chcete dosáhnout nízké latence a vysoká dostupnost, instancí těchto aplikací je nutné nasadit v datových centrech, která jsou blízko uživatelům. Tyto aplikace jsou obvykle nasazené v několika datových centrech a jsou volány globálně distribuované. Globálně distribuované aplikace potřebují globálně distribuovanou databázi, která umí transparentně replikovat data kdekoli ve světě povolit aplikace, které chcete použít pro kopírování dat, která se nachází blízko uživatelů. 

Azure Cosmos DB je globálně distribuovaná databázová služba, která zajišťuje nízkou latenci, elastickou škálovatelnost propustnosti, sémantiku jasně definovaných pro konzistenci dat a vysokou dostupnost. Stručně řečeno pokud vaše aplikace potřebuje zaručené rychlou odezvu kdekoli na světě, pokud je potřeba mít vždy online a vyžaduje neomezenou a elastické škálovatelnosti propustnost a úložiště, měli byste vytvořit aplikaci ve službě Azure Cosmos DB.

Můžete nakonfigurovat vaše databáze bude globálně distribuovaná a k dispozici ve všech oblastech Azure. Snížit latenci, umístění dat blízko kde jsou vaši uživatelé. Volba požadované oblasti závisí na globální dosah aplikace, a kde se nachází vaši uživatelé. Cosmos DB transparentně replikuje data do všech oblastí, přidružených k účtu Cosmos. Poskytuje jeden systémový obraz globálně distribuovanou databázi Azure Cosmos a kontejnerů, které vaše aplikace může číst a zapisovat do místně. 

Pomocí služby Azure Cosmos DB můžete přidat nebo odebrat oblasti spojené s vaším účtem v každém okamžiku. K přidání nebo odebrání oblasti se nevyžaduje pozastavení ani opětovné nasazení vaší aplikace. Pokračuje k zajištění vysoké dostupnosti neustále kvůli možnostmi multihomingu, které nativně poskytuje službu.

![Topologie nasazení s vysokou dostupností](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Klíčové výhody globální distribuce

**Vytváření globálních aplikací typu aktivní aktivní.** Každou oblast se používá protokol nové multimasterovou replikací podporuje zápisy a čtení. Taky umožňuje více hlavních možností:

- Neomezené elastické zápis a čtení škálovatelnost. 
- 99,999 % čtení a zápis dostupnost po celém světě.
- Garantované operací čtení a zápisů v méně než 10 milisekund na 99. percentilu.

Vícenásobné navádění služby Azure Cosmos DB pomocí rozhraní API, aplikace si je vědoma je nejbližší oblast a zasílat žádosti pro tuto oblast. Je nejbližší oblast je identifikován bez změny konfigurace. Můžete přidat nebo odebrat oblasti do a z vašeho účtu Azure Cosmos, aplikace nemusí být znovu nasadit nebo pozastavena, pokračuje k zajištění vysoké dostupnosti po celou dobu.

**Vytvářejte aplikace s velmi rychlou odezvou.** Vaše aplikace může provádět téměř v reálném čase čtení a zápisu všech oblastech, které jste zvolili pro vaši databázi. Azure Cosmos DB interně zpracovává replikaci dat mezi oblastmi se úroveň záruky konzistence úrovně, kterou jste vybrali.

**Vytvářejte aplikace s vysokou dostupností.** Spuštění databáze ve více oblastech po celém světě, zvýšíte dostupnost databáze. Pokud jedna oblast nedostupný, ostatní oblasti automaticky zpracovává žádosti o aplikace. Azure Cosmos DB nabízí 99,999 % čtení a zápis dostupnost pro databáze ve více oblastech.

**Zachování kontinuity obchodních procesů během regionální výpadky.** Azure Cosmos DB podporuje [automatické převzetí služeb při selhání](how-to-manage-database-account.md#automatic-failover) během oblastního výpadku. Během oblastního výpadku služby Azure Cosmos DB nadále udržovat smluv SLA latence, dostupnosti, konzistence a propustnost. Abyste měli jistotu, že má vysokou dostupnost celé aplikace, služby Cosmos DB nabízí ruční převzetí služeb rozhraní API k simulaci místního výpadku. Pomocí tohoto rozhraní API, můžete provádět pravidelné Obchodní kontinuity podnikových procesů cvičení.

**Škálování pro čtení a zápis propustnost globálně.** Můžete povolit každou oblast umožňovat zápis a Elasticky škálovat čtení a zápisy po celém světě. Je zaručeno, že propustnost, které vaše aplikace nakonfiguruje na databázi Azure Cosmos nebo kontejner doručena ve všech oblastech, které jsou spojené s vaším účtem Azure Cosmos. Zřízená propustnost je zaručeno, že si podle [finančně zajištěné smlouvy SLA](https://aka.ms/acdbsla).

**Vyberte si z několika jasně definované modely konzistence.** Protokol replikace služby Azure Cosmos DB nabízí pět modelů konzistence dobře definovaných, praktických a intuitivních. Každý model má kompromis mezi konzistencí a výkonem. Pomocí těchto modelů konzistence můžete vytvářet globálně distribuované aplikace s lehkostí a elegancí.

## <a id="Next Steps"></a>Další kroky

Přečtěte si další informace o globální distribuci v následujících článcích:

* [Globální distribuce - pod pokličkou](global-dist-under-the-hood.md)
* [Jak nakonfigurovat více hlavních databází ve svých aplikacích](how-to-multi-master.md)
* [Konfigurace klientů pro multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Přidat nebo odebrat oblasti ze svého účtu Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Vytvořit zásadu konflikt vlastní řešení pro účty rozhraní SQL API](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)