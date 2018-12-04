---
title: Globální distribuce dat pomocí služby Azure Cosmos DB
description: Další informace o globálním měřítku geografickou replikaci, více hlavních databází, převzetí služeb při selhání a obnovení dat pomocí globálních databází ze služby Azure Cosmos DB, globálně distribuovaná a vícemodelová databázová služba.
services: cosmos-db
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.openlocfilehash: 2c217a1a89d3b573bfe2297a263bf55849b5f6e1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843848"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Distribuce globálních dat pomocí služby Azure Cosmos DB

Dnešní aplikace musí být vždy online a s velmi rychlou odezvou. Pokud chcete dosáhnout nízké latence a vysoká dostupnost, instancí těchto aplikací je nutné nasadit v datových centrech, která jsou blízko uživatelům. Tyto aplikace jsou obvykle nasazené v několika datových centrech a jsou volány globálně distribuované. Globálně distribuované aplikace potřebují globálně distribuovanou databázi, která umí transparentně replikovat data kdekoli ve světě povolit aplikace, které chcete použít pro kopírování dat, která se nachází blízko uživatelů. 

Azure Cosmos DB je globálně distribuovaná databázová služba, která zajišťuje nízkou latenci, elastickou škálovatelnost propustnosti, sémantiku jasně definovaných pro konzistenci dat a vysokou dostupnost. Stručně řečeno pokud vaše aplikace potřebuje zaručené rychlou odezvu kdekoli na světě, pokud je potřeba mít vždy online a vyžaduje neomezenou a elastické škálovatelnosti propustnost a úložiště, vezměte v úvahu vytváření aplikací pomocí služby Azure Cosmos DB.

Můžete nakonfigurovat vaše databáze bude globálně distribuovaná a k dispozici ve všech oblastech Azure. Pokud chcete snížit latenci, umístěte data blíž k kde jsou vaši uživatelé. Volba požadované oblasti závisí na globální dosah aplikace, a kde se nachází vaši uživatelé. Data v rámci svého účtu Azure Cosmos DB transparentně replikuje do všech oblastí, které jsou spojené s vaším účtem. Poskytuje jeden systémový obraz globálně distribuovanou databázi Azure Cosmos a kontejnerů, které vaše aplikace může číst a zapisovat do místně. 

Pomocí služby Azure Cosmos DB můžete přidat nebo odebrat oblasti spojené s vaším účtem v každém okamžiku. Vaše aplikace nemusí pozastavená nebo znovu nasadit do přidat nebo odebrat oblasti. Pokračuje k zajištění vysoké dostupnosti neustále kvůli multihomingu možnosti, které služba poskytuje.

## <a name="key-benefits-of-global-distribution"></a>Klíčové výhody globální distribuce

**Vytváření globálních aplikací typu aktivní aktivní.** S několika hlavními databázemi funkcí každou oblast je oblast pro zápis. Je také čitelné. Také zaručuje více hlavní funkce:

- Zápis neomezené elastické škálovatelnosti. 
- 99,999 % čtení a zápis dostupnost po celém světě.
- Garantované operací čtení a zápisů v méně než 10 milisekund na 99. percentilu.

Pomocí služby Azure Cosmos DB multihoming rozhraní API je seznámen je nejbližší oblast vaší aplikace. Pak může odeslat žádosti pro tuto oblast. Je nejbližší oblast je identifikován bez změny konfigurace. Jak přidat a odebrat oblasti ze svého účtu Azure Cosmos DB, vaše aplikace nevyžaduje se znovu nasadit. Aplikace i nadále s vysokou dostupností.

**Vytvářejte aplikace s velmi rychlou odezvou.** Aplikace můžete snadno navržené k téměř v reálném čase operací čtení a zápisu. Latence v řádu milisekund může použít pro všechny oblasti, kterou jste zvolili pro vaši databázi. Azure Cosmos DB interně zpracovává replikaci dat mezi oblastmi. V důsledku toho je zaručena konzistence úroveň, vybraná pro účet služby Azure Cosmos DB.

Mnoho aplikací mít prospěch z vylepšení výkonu, které jsou součástí schopnost provádět zápisy (místní) ve více oblastech. Některé aplikace, které vyžadují silnou konzistenci raději trychtýře všechny operace zápisu do jedné oblasti. V případě těchto aplikací služby Azure Cosmos DB podporuje jedné oblasti a konfigurací ve více oblastech.

**Vytvářejte aplikace s vysokou dostupností.** Spuštění databáze v několika oblastech, zvýšíte dostupnost databáze. Pokud jedna oblast nedostupný, ostatní oblasti automaticky zpracovává žádosti o aplikace. Azure Cosmos DB nabízí 99,999 % čtení a zápis dostupnost pro databáze ve více oblastech.

**Zachování kontinuity obchodních procesů během regionální výpadky.** Azure Cosmos DB podporuje [automatické převzetí služeb při selhání](how-to-manage-database-account.md#automatic-failover) během oblastního výpadku. Během oblastního výpadku služby Azure Cosmos DB nadále udržovat smluv SLA latence, dostupnosti, konzistence a propustnost. Abyste měli jistotu, že má vysokou dostupnost celé aplikace, služby Azure Cosmos DB nabízí ruční převzetí služeb rozhraní API k simulaci místního výpadku. Pomocí tohoto rozhraní API, můžete provádět pravidelné Obchodní kontinuity podnikových procesů cvičení.

**Škálování pro čtení a zápis propustnost globálně.** S několika hlavními databázemi funkcí můžete elastické škálování pro čtení a zápis propustnost po celém světě. Funkci multimasterovou zaručuje propustnost, které vaše aplikace nakonfiguruje na databázi Azure Cosmos DB nebo kontejneru se doručí ve všech oblastech. Propustnost je také chráněn pomocí [finančně zajištěné smlouvy SLA](https://aka.ms/acdbsla).

**Vyberte si z několika jasně definované modely konzistence.** Protokol replikace služby Azure Cosmos DB nabízí pět modelů konzistence dobře definovaných, praktických a intuitivních. Každý model má kompromis mezi konzistencí a výkonem. Pomocí těchto modelů konzistence můžete vytvářet globálně distribuované aplikace s lehkostí a elegancí.

## <a id="Next Steps"></a>Další kroky

Přečtěte si další informace o globální distribuci v následujících článcích:

* [Globální distribuce - pod pokličkou](global-dist-under-the-hood.md)
* [Konfigurace klientů pro multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Přidat nebo odebrat oblasti ze svého účtu Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Vytvořit zásadu konflikt vlastní řešení pro účty rozhraní SQL API](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)