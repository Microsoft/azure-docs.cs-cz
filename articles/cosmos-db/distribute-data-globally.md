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
ms.openlocfilehash: 7f2c242d7040413598864222efdf06843eddc7d9
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959490"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Distribuce globálních dat pomocí služby Azure Cosmos DB

Řada dnešních aplikací spusťte v globálním měřítku. Tyto aplikace jsou vždy on a jejich zpřístupnění uživatelům po celém světě. Správa globální distribuce dat, která používá tyto aplikace poskytuje vysoký výkon a vysoké dostupnosti je obtížné problém. Azure Cosmos DB je globálně distribuovaná databázová služba, která je navrženo pro zajištění vysokého výkonu a vysoké dostupnosti. Z těchto důvodů je nejvhodnější v případě těchto aplikací v reálném čase služby Azure Cosmos DB.

Je základní služby Azure cosmos DB a je k dispozici ve všech [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/) ve výchozím nastavení. Microsoft provozuje datových center Azure ve více než 50 oblastech po celém světě a rozšířit se a plnit rostoucí požadavky zákazníků i nadále. Při vytváření účtu služby Cosmos DB je rozhodnout, které položky nakoupené by měly být nasazeny v. Microsoft provozuje Cosmos DB služeb 24 hodin denně, 7, takže se můžete soustředit na aplikace.

Můžete nakonfigurovat vaše databáze bude globálně distribuovaná a k dispozici ve všech oblastech Azure více než 50. Snížit latenci, měli byste umístit data blíž umístění uživatele. Volba požadované oblasti závisí na globální dosah aplikace, a kde se nachází vaši uživatelé. Data v rámci svého účtu služby cosmos DB transparentně replikuje do všech oblastí, nakonfigurované. Poskytuje jeden systémový obraz do databáze Cosmos a kontejnery, které vaše aplikace může číst a zapisovat místně. Pomocí služby Cosmos DB můžete přidat nebo odebrat oblasti spojené s vaším účtem v každém okamžiku. Vaše aplikace nemusí pozastavená nebo znovu nasadit do přidat nebo odebrat oblasti. Pokračuje k zajištění vysoké dostupnosti neustále kvůli možnostmi multihomingu, které služba poskytuje.

## <a name="key-benefits-of-global-distribution"></a>Klíčové výhody globální distribuce

**Vytváření globálních aplikací typu aktivní aktivní**: S několika hlavními databázemi schopností každou oblast je oblast pro zápis (kromě toho, že čitelné). Více hlavních databází funkce také záruky – zápis neomezené elastické škálovatelnosti, 99,999 % čtení a zápis dostupnost všech kolem světa a zaručenou čtení/zápisy obsluhovat méně než 10 milisekund na 99. percentilu.  

Tím, že pomocí služby Azure Cosmos DB API pro vícenásobné navádění, vaše aplikace je seznámen je nejbližší oblast a kterou může odesílat požadavky pro tuto oblast. Nejbližší oblast je identifikován bez změny konfigurace. Jak přidat a odebrat oblasti ze svého účtu Cosmos DB, není nutné znovu nasadit vaše aplikace a pokračuje k zajištění vysoké dostupnosti.

**Vytvářejte aplikace s velmi rychlou odezvou**: vaše aplikace můžou být snadno navržené pro téměř v reálném čase operací čtení a zápisu s latencí v řádu milisekund na všech oblastech, které jste zvolili pro vaši databázi.  Azure Cosmos DB interně zpracovává replikaci dat mezi oblastmi tak, že je zaručeno, že jste zvolili pro účet Cosmos úroveň konzistence.

Vylepšení výkonu, které jsou součástí schopnost provádět zápisy (místní) ve více oblastech budou využívat mnoho aplikací. Některé aplikace, které vyžadují silnou konzistenci raději trychtýře všechny operace zápisu do jedné oblasti. Pro podporu těchto aplikací, Cosmos DB podporuje jedné oblasti a konfigurací ve více oblastech.

**Vytvářejte aplikace s vysokou dostupností**: spuštění databáze ve více oblastech, zvýšíte dostupnost databáze. Pokud jedna oblast nedostupný, ostatní oblasti automaticky zpracovává žádosti o aplikace. Azure Cosmos DB nabízí 99,999 % čtení a zápis dostupnost pro databáze ve více oblastech.

**Kontinuita podnikových procesů během regionální výpadky**: Azure Cosmos DB podporuje [automatické převzetí služeb při selhání](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account) během oblastního výpadku. Kromě toho během oblastního výpadku, Cosmos DB nadále udržovat latence, dostupnosti, konzistence a propustnost smlouvy o úrovni služeb. K zajištění, že má vysokou dostupnost celé aplikace, služby Azure Cosmos DB nabízí rozhraní API ruční převzetí služeb při selhání k simulaci místního výpadku. Pomocí tohoto rozhraní API můžete provádět pravidelné Obchodní kontinuity podnikových procesů cvičení.

**Globální čtení a zápis škálovatelnost**: S několika hlavními databázemi funkce, které se můžete Elasticky škálovat pro čtení a zápis propustnost po celém světě. Více hlavních schopností zaručuje, že propustnost, které vaše aplikace nakonfiguruje na databázi Azure Cosmos DB nebo kontejner je dodáno ve všech oblastech a je chráněn [finančně zajištěné smlouvy SLA](https://aka.ms/acdbsla).

**Více, jasně definované modely konzistence**: protokol replikace služby Azure Cosmos DB je určená k pět modelů konzistence dobře definovaných, praktických a intuitivních. Každý model konzistence má kompromis mezi konzistencí a výkonem. Tyto modely konzistence umožňují vytváření globálně distribuovaných aplikací s lehkostí a elegancí.

## <a id="Next Steps"></a>Další kroky

Přečtěte si další informace o globální distribuci v následujících článcích:

* [Globální distribuce - pod pokličkou](global-dist-under-the-hood.md)
* [Konfigurování klientů pro vícenásobné navádění](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Postup přidání nebo odebrání oblastí z databáze](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak vytvořit zásadu konflikt vlastní řešení pro účty rozhraní SQL API](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)