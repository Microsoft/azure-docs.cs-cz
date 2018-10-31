---
title: Globální distribuce dat pomocí služby Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o globálním měřítku geografickou replikaci, více hlavních databází, převzetí služeb při selhání a obnovení dat pomocí globálních databází ze služby Azure Cosmos DB, globálně distribuovaná a vícemodelová databázová služba.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 68ac603964593892dfcbc3488b4e6f2c91a0eb92
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238270"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Distribuce globálních dat pomocí služby Azure Cosmos DB

Řada dnešních aplikací spustit nebo mít záměrů, aby se spouštěla v globálním měřítku. Jsou vždy na a jsou přístupné uživatelům na celém světě. Správa globální distribuce dat, která používá tyto aplikace poskytuje vysoký výkon a vysoké dostupnosti je obtížné problém. Cosmos DB je globálně distribuovaná databázová služba, která má bylo sestaveno zdola nahoru pro splnění těchto výzev.

Je základní služby Azure cosmos DB a je k dispozici ve všech [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/) ve výchozím nastavení. Microsoft provozuje datových center Azure ve více než 50 oblastech po celém světě a rozšířit se a plnit rostoucí požadavky zákazníků i nadále. Microsoft provozuje Cosmos DB služeb 24 hodin denně, 7, takže se můžete soustředit na aplikace. Při vytváření účtu služby Cosmos DB je rozhodnout, které položky nakoupené by měly být nasazeny v.

Cosmos DB zákazníci můžou nakonfigurovat své databáze bude globálně distribuovaná a portálu k dispozici od 1 do oblastí Azure více než 50. Snížit latenci, by měl umístit data blíž k umístění pro vaše uživatele, tak rozhodování o tom, kolik oblastí a v jaké oblasti se má spustit závisí na globálního dosahu vaší aplikace a kde jsou vaši uživatelé. Všechna data v rámci svého účtu Cosmos cosmos DB transparentně replikuje do všech oblastí, nakonfigurované. Cosmos DB poskytuje jeden systémový obraz do databáze Cosmos a kontejnery, tak, aby vaše aplikace může číst a zapisovat místně. Pomocí služby Cosmos DB můžete přidat nebo odebrat oblasti spojené s vaším účtem v každém okamžiku. Vaše aplikace nemusí pozastavená nebo znovu nasadit a pokračuje k zajištění vysoké dostupnosti, obsluha dat probíhá po celou dobu, díky možnostmi multihomingu, které služba poskytuje.

## <a name="key-benefits-of-global-distribution"></a>Klíčové výhody globální distribuce

**Vytváření globálních aplikací typu aktivní aktivní, snadno**: S několika hlavními databázemi funkcí je zapisovatelný (kromě toho, že čitelné) každé oblasti a umožňuje neomezený elastický zápisu škálovatelnost pro čtení 99,999 % a zápis dostupnost po celém světě, a garantované Rychlé čtení a zápisy obsluhovat méně než 10 milisekund na 99. percentilu.  

Pomocí služby Azure Cosmos DB API pro vícenásobné navádění, vaše aplikace vždycky ví je nejbližší oblast a odesílá požadavky pro tuto oblast. Nejbližší oblast je identifikován bez změny konfigurace. Jak přidat a odebrat oblasti ze svého účtu Cosmos DB, není nutné znovu nasadit vaše aplikace a pokračuje k zajištění vysoké dostupnosti.

**Vytvářejte aplikace s velmi rychlou odezvou**: vaše aplikace můžou být snadno navržené pro téměř v reálném čase operací čtení a zápisu s latencí v řádu milisekund, pro všechny oblasti jste zvolili pro vaši databázi.  Azure Cosmos DB interně zpracovává replikaci dat mezi oblastmi způsobem, který zaručuje úroveň konzistence, zvolená pro účet Cosmos.

Vylepšení výkonu, které jsou součástí schopnost provádět zápisy (místní) ve více oblastech budou využívat mnoho aplikací. Některé aplikace, jako jsou ty, které vyžadují silnou konzistenci, budou chtít trychtýře všechny operace zápisu do jedné oblasti. Cosmos DB podporuje konfigurace, jedné oblasti a více oblastí.

**Vytvářejte aplikace s vysokou dostupností**: spuštění databáze ve více oblastech zlepšuje dostupnost databáze. Pokud jedna oblast nedostupný, ostatní oblasti automaticky zpracovává žádosti o aplikace. Azure Cosmos DB nabízí 99,999 % čtení a zápis dostupnost pro databáze ve více oblastech.

**Kontinuita podnikových procesů během regionální výpadky**: Azure Cosmos DB podporuje [automatické převzetí služeb při selhání](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account) během oblastního výpadku. Kromě toho během oblastního výpadku, Cosmos DB nadále udržovat latence, dostupnosti, konzistence a propustnost smlouvy o úrovni služeb. K zajištění, že má vysokou dostupnost celé aplikace, služby Azure Cosmos DB nabízí rozhraní API ruční převzetí služeb při selhání k simulaci místního výpadku. Pomocí tohoto rozhraní API můžete k podrobnostem běžná obchodní kontinuity podnikových procesů a být připravené.

**Globální čtení a zápis škálovatelnost**: S několika hlavními databázemi funkce, které se můžete Elasticky škálovat pro čtení a zápis propustnost po celém světě. Více hlavních schopností zaručuje, že propustnost, které vaše aplikace nakonfiguruje na databázi Azure Cosmos DB nebo kontejner je dodáno ve všech oblastech a je chráněn [finančně zajištěné smlouvy SLA](https://aka.ms/acdbsla).

**Více, jasně definované modely konzistence**: protokolu replikace služby Azure Cosmos DB je určená k pět jasně definovaných, praktických a intuitivních konzistence modely spolu vymazat kompromis mezi konzistencí a výkonem. Tyto modely konzistence umožňují vytváření globálně distribuovaných aplikací správné s lehkostí a elegancí.

## <a id="Next Steps"></a>Další kroky

Přečtěte si další informace o globální distribuci v následujících článcích:

* [Globální distribuce - pod pokličkou](global-dist-under-the-hood.md)
* [Konfigurování klientů pro vícenásobné navádění](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Postup přidání nebo odebrání oblastí z databáze](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak vytvořit zásadu konflikt vlastní řešení pro účty rozhraní SQL API](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)