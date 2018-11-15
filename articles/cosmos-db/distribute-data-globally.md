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
ms.openlocfilehash: c90450fa4cc35b460198f5a351a965aee4ea4f4b
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636406"
---
# <a name="build-globally-distributed-applications-with-azure-cosmos-db"></a>Vytváření globálně distribuovaných aplikací pomocí služby Azure Cosmos DB

Řada dnešních aplikací vyžadují provozují v několika datových centrech. Tyto aplikace jsou označovány jako globálně distribuovaných aplikací. Tyto aplikace jsou vždy "on" a jsou přístupné uživatelům po celém světě. Správa globální distribuce dat, která používá tyto aplikace poskytuje nízkou latenci, elastickou škálovatelnost výkonu a vysoké dostupnosti po celém světě, je obtížné problém. Azure Cosmos DB je globálně distribuovaná databázová služba, která je určená k poskytnutí nízkou latenci, elastickou škálovatelnost propustnosti, sémantiku jasně definovaných pro konzistenci dat a vysokou dostupnost. Stručně řečeno pokud se potřeby vaší aplikace zaručit krátkou dobu odezvy kdekoli na světě, pokud vyžaduje, aby se vždy online a vyžaduje neomezenou a elastické škálovatelnosti propustnost a úložiště, měli byste zvážit vytváření aplikací pomocí služby Azure Cosmos DB.

Azure Cosmos DB je základní služby Azure a je k dispozici ve všech [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/) ve výchozím nastavení. Microsoft provozuje datových center Azure v 54 + oblastech po celém světě a nadále rozšiřuje místní přítomnost na rostoucí potřeby zákazníků. Při vytváření účtu Azure Cosmos je rozhodnout, které položky nakoupené by měly být nasazeny v. Microsoft provozuje služby Azure Cosmos DB služeb 24 hodin denně, 7, takže se můžete soustředit na aplikace.

Můžete nakonfigurovat vaše databáze bude globálně distribuovaná a k dispozici ve všech oblastech Azure. Snížit latenci, měli byste umístit data blíž k kde jsou vaši uživatelé. Volba požadované oblasti závisí na globální dosah aplikace, a kde se nachází vaši uživatelé. Data v rámci svého účtu Azure Cosmos DB transparentně replikuje do všech oblastí, které jsou spojené s vaším účtem. Poskytuje jeden systémový obraz globálně distribuovanou databázi Azure Cosmos a kontejnerů, které vaše aplikace může číst a zapisovat do místně. Pomocí služby Azure Cosmos DB můžete přidat nebo odebrat oblasti spojené s vaším účtem v každém okamžiku. Vaše aplikace nemusí pozastavená nebo znovu nasadit do přidat nebo odebrat oblasti. Pokračuje k zajištění vysoké dostupnosti neustále kvůli možnostmi multihomingu, které služba poskytuje.

## <a name="key-benefits-of-global-distribution"></a>Klíčové výhody globální distribuce

**Vytváření globálních aplikací typu aktivní aktivní**: S několika hlavními databázemi schopností každou oblast je oblast pro zápis (kromě toho, že čitelné). Více hlavních databází funkce také záruky – zápis neomezené elastické škálovatelnosti, 99,999 % čtení a zápis dostupnost všech kolem světa a zaručenou čtení/zápisy obsluhovat méně než 10 milisekund na 99. percentilu.  

Tím, že pomocí služby Azure Cosmos DB API pro vícenásobné navádění, vaše aplikace je seznámen je nejbližší oblast a kterou může odesílat požadavky pro tuto oblast. Nejbližší oblast je identifikován bez změny konfigurace. Jak přidat a odebrat oblasti ze svého účtu Azure Cosmos DB, není nutné znovu nasadit vaše aplikace a pokračuje k zajištění vysoké dostupnosti.

**Vytvářejte aplikace s velmi rychlou odezvou**: vaše aplikace můžou být snadno navržené pro téměř v reálném čase operací čtení a zápisu s latencí v řádu milisekund na všech oblastech, které jste zvolili pro vaši databázi.  Azure Cosmos DB interně zpracovává replikaci dat mezi oblastmi tak, že je zaručeno, že úrovně konzistence, zvolená pro účet Azure Cosmos.

Vylepšení výkonu, které jsou součástí schopnost provádět zápisy (místní) ve více oblastech budou využívat mnoho aplikací. Některé aplikace, které vyžadují silnou konzistenci raději trychtýře všechny operace zápisu do jedné oblasti. Pro podporu těchto aplikací, Azure Cosmos DB podporuje jedné oblasti a konfigurací ve více oblastech.

**Vytvářejte aplikace s vysokou dostupností**: spuštění databáze ve více oblastech, zvýšíte dostupnost databáze. Pokud jedna oblast nedostupný, ostatní oblasti automaticky zpracovává žádosti o aplikace. Azure Cosmos DB nabízí 99,999 % čtení a zápis dostupnost pro databáze ve více oblastech.

**Kontinuita podnikových procesů během regionální výpadky**: Azure Cosmos DB podporuje [automatické převzetí služeb při selhání](how-to-manage-database-account.md#automatic-failover) během oblastního výpadku. Kromě toho během oblastního výpadku služby Azure Cosmos DB nadále udržovat latence, dostupnosti, konzistence a propustnost smlouvy o úrovni služeb. K zajištění, že má vysokou dostupnost celé aplikace, služby Azure Cosmos DB nabízí rozhraní API ruční převzetí služeb při selhání k simulaci místního výpadku. Pomocí tohoto rozhraní API můžete provádět pravidelné Obchodní kontinuity podnikových procesů cvičení.

**Globální čtení a zápis škálovatelnost**: S několika hlavními databázemi funkce, které se můžete Elasticky škálovat pro čtení a zápis propustnost po celém světě. Více hlavních schopností zaručuje, že propustnost, které vaše aplikace nakonfiguruje na databázi Azure Cosmos DB nebo kontejner je dodáno ve všech oblastech a je chráněn [finančně zajištěné smlouvy SLA](https://aka.ms/acdbsla).

**Více, jasně definované modely konzistence**: protokol replikace služby Azure Cosmos DB je určená k pět modelů konzistence dobře definovaných, praktických a intuitivních. Každý model konzistence má kompromis mezi konzistencí a výkonem. Tyto modely konzistence umožňují vytváření globálně distribuovaných aplikací s lehkostí a elegancí.

## <a id="Next Steps"></a>Další kroky

Přečtěte si další informace o globální distribuci v následujících článcích:

* [Globální distribuce - pod pokličkou](global-dist-under-the-hood.md)
* [Konfigurování klientů pro vícenásobné navádění](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Jak přidat/odebrat oblasti ze svého účtu Azure Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak vytvořit zásadu konflikt vlastní řešení pro účty rozhraní SQL API](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)