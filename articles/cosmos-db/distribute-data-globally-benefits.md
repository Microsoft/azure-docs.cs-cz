---
title: Klíčové výhody globální distribuce služby Azure Cosmos DB
description: Přečtěte si o několika hlavními databázemi, klíčové výhody služby Azure Cosmos DB nabízí geografickou replikaci, více hlavních databází a použití případech, kdy je užitečné.
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 11d70a648bfc1882753688e5352835b04cee6b9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968338"
---
# <a name="distribute-data-globally-with-azure-cosmos-db"></a>Globální distribuce dat pomocí služby Azure Cosmos DB

Tento článek popisuje klíčové výhody globální distribuce dat a některé scénáře v reálném čase, které vyžadují globální data distribuce.

## <a name="key-benefits"></a>Klíčové výhody

Toto jsou klíčové výhody, které jsou k dispozici pro účty, které využívají možnosti distribuce globálních dat služby Azure Cosmos DB:

* **Latence v řádu** – Azure Cosmos DB poskytuje < 10 ms pro čtení a zápis latence na 99. percentilu, zaručeny [finančně zajištěné smlouvy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **dostupnost 5 až 9** – Azure Cosmos DB nabízí 99,999 % čtení a zápis dostupnosti zaručené [finančně zajištěné smlouvy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **Řešení konfliktů flexibilní** – pro zákazníky multimasterovou funkcí služby Azure Cosmos DB poskytuje tří režimů pro zpracování konfliktů, aby se globální data integritu a konzistenci.

* **Konzistence s možností vyladění** – Azure Cosmos DB podporuje 5 různých [úrovně konzistence](consistency-levels.md) s globální distribucí v paměti s podporou pro všechny kromě silnou konzistenci pro účty ovládnutí více funkce.

* **Implicitní odolnost proti chybám** -data replikovat napříč několika oblastmi, můžete aplikace využijte vysokou odolnost proti místní výpadky.

## <a name="use-cases"></a>Případy použití

Tady je jenom malé ukázkové scénáře, které můžete využít globální distribuce možnosti služby Azure Cosmos DB.

* **Aplikace sociálních médií** – aplikace sociálních sítí vyžadují nízkou latenci, vysokou dostupnost a škálovatelnost, které poskytují prostředí pro uživatele rozmístění po celém světě.

* **IoT** -edge geograficky distribuované nasazení často potřebují ke sledování dat časových řad z více míst. Každé zařízení můžete adresami k jeho nejbližší oblasti. Když se zařízení se přesunou do různých umístění, tato zařízení můžou přesunuty k zápisu do nejbližší dostupné oblasti.

* **E-Commerce** -E-Commerce vyžaduje velmi nízkou latencí stejně jako vysokou dostupnost. Geografická distribuce dat pomocí operací čtení a zápisů uložíte data co nejblíž koncovým uživatelům, zvýšit rychlost odezvy aplikací. Dostupnost pro čtení a zápis v několika oblastech a zajišťuje vyšší dostupnost.

* **Zjišťování možných podvodů a anomálií** -často aplikací, které monitoruje aktivity uživatele nebo aktivity účtu jsou globálně distribuované a musí udržovat přehled o několik událostí současně k aktualizaci skóre zachovat vložené metriky rizika.

* **Monitorování míry využívání** – počítání a řízení využití (jako je například volání rozhraní API používá minut transakcí za sekundu) je možné implementovat globálně pomocí jednoduchosti použití více hlavních databází Azure Cosmos DB. Řešení konfliktů integrované zaručuje obou nemusí být počty a nařízení v reálném čase.

## <a name="next-steps"></a>Další postup  

V tomto článku jste se dozvěděli o klíčových výhodách a případy použití pro funkce distribuce globálních dat služby Azure Cosmos DB. Podívejte se v dalším kroku v následujících článcích:

* [Jak služby Azure Cosmos DB umožňuje globální distribuce na klíč](distribute-data-globally-turnkey.md)

* [Jak nakonfigurovat službu Azure Cosmos DB globální replikace databáze](tutorial-global-distribution-sql-api.md)

* [Povolení více hlavních databází pro účty služby Azure Cosmos DB](enable-multi-master.md)

* [Jak funguje automatické a ruční převzetí služeb při selhání ve službě Azure Cosmos DB](regional-failover.md)

* [Principy řešení konfliktů ve službě Azure Cosmos DB](multi-master-conflict-resolution.md)

* [Použití více hlavních databází s databázemi NoSQL otevřít zdroj](multi-master-oss-nosql.md)
