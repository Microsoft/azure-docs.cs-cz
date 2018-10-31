---
title: Vysoká dostupnost ve službě Azure Cosmos DB
description: Tento článek popisuje, jak službu Azure Cosmos DB poskytuje vysokou dostupnost
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: fdf1bf227ed21111c1aa0754a8423c1ee6aa151b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244140"
---
# <a name="high-availability-in-azure-cosmos-db"></a>Vysoká dostupnost ve službě Azure Cosmos DB

Azure Cosmos DB transparentně replikuje data mezi oblastmi Azure, které jsou přidružené k vašemu účtu Cosmos.  Cosmos DB využívá několik vrstev redundance pro vaše data. Jak je znázorněno v následujícím obrázku:

- Data v rámci Cosmos kontejnerů se horizontálně dělené do oddílů.
- V rámci každé oblasti je každý oddíl chráněný sady replik se všechny operace zápisu replikovat a odolném potvrzení většinou replik. Repliky distribuují napříč doménami selhání až 10-20.
- Ve všech oblastech se replikují všechny oddíly. Každé oblasti obsahuje všechny datové oddíly kontejneru Cosmos a můžou akceptovat zápisy a čtení obsluhovat.  

![Vytváření oddílů prostředků](./media/high-availability/figure1.png) 

Pokud je váš účet služby Cosmos distribuovaná napříč oblastmi N Azure, bude existovat alespoň N x 4 kopie všechna vaše data. Kromě toho, že přístup s nízkou latencí k vašim datům a škálování zápisu a propustnost čtení napříč oblastmi spojenými s vaším účtem Cosmos, s více oblastmi (vyšší N) také zlepšuje dostupnost.  

## <a name="availability-slas"></a>Smlouvy SLA o dostupnosti  

Cosmos DB jakožto globálně distribuovaná databáze, poskytuje komplexní smlouvy SLA, včetně propustnosti, latenci na úrovni 99. percentilu, konzistence a vysokou dostupnost. V následující tabulce popisujeme záruky týkající se vysoké dostupnosti poskytuje jeden a více oblastí pro účty služby Cosmos DB. Kvůli nejvyšší dostupnosti důrazně doporučujeme konfigurovat vaše účty Cosmos mít více oblastí zápisu.

|Typ operace  | Jedna oblast |Ve více oblastech (zápisy do jedné oblasti)|Ve více oblastech (zápisy do více oblastí|
|---------|---------|---------|-------|
|Zápisy    | 99,99    |99,99   |99,999|
|Čtení     | 99,99    |99,999  |99,999|

> [!NOTE]
> V praxi dostupnost skutečné zápisu omezená neaktuálnost, relace, modelů konzistence konzistentní Předpona a konečný výsledek je výrazně vyšší než publikované smlouvy o úrovni služeb. A v praxi je výrazně vyšší než publikované smlouvy o úrovni služeb skutečnou dostupnost čtení pro všechny úrovně konzistence.

## <a name="regional-outages"></a>Regionální výpadky

Nejsou místních výpadků a Azure Cosmos DB zajišťuje, že vaše databáze budou vždy k dispozici. Následující zachycuje Cosmos DB chování během výpadku, v závislosti na konfiguraci vašeho účtu Cosmos: 

- Účty ve více oblastech nakonfigurovanou více oblasti zůstane s vysokou dostupností pro zápisu i čtení během oblastního výpadku. Regionální převzetí služeb při selhání se okamžité a nevyžadují žádné změny z aplikace.

- Účty ve více oblastech s oblastí zápisu jednou během výpadku oblasti zápisu zůstane s vysokou dostupností pro čtení. Však pro zápis musí "Povolit automatické převzetí služeb při selhání" na vašem účtu Cosmos pro službu Cosmos DB k převzetí služeb při selhání ovlivněné oblasti do jiné oblasti přidružené k účtu Cosmos. Převzetí služeb při selhání dojde v pořadí podle priority oblasti, kterou jste zadali. Nakonec pokud ovlivněné oblasti je zpátky do online režimu, nereplikované data k dispozici v oblasti ovlivněné zápisu během výpadku je k dispozici prostřednictvím konflikty informačního kanálu. Aplikace může číst konflikty informačního kanálu, vyřešte konflikty, na základě specifické pro aplikaci logiky a zapsat aktualizovaná data zpět do kontejneru Cosmos podle potřeby. Po obnoví dříve ovlivněné zápisu oblasti stane automaticky k dispozici jako oblasti čtení. Můžete vyvolat ruční převzetí služeb při selhání a vrácení ovlivněné oblasti jako oblast pro zápis. Ruční převzetí služeb při selhání můžete provést pomocí webu Azure portal nebo rozhraní příkazového řádku Azure.  

- Účty ve více oblastech s oblastí zápisu jednou během výpadku oblasti čtení zůstane s vysokou dostupností pro čtení a zápis. Ovlivněné oblasti se automaticky odpojí z oblasti pro zápis a budou označeny v režimu offline. Cosmos DB SDK přesměruje volání další dostupné oblasti v seznamu preferované oblasti čtení. Pokud žádná z oblasti v seznamu preferované oblasti není k dispozici, volání automaticky vrátit do aktuální oblasti pro zápis. Nevyžaduje žádné změny v kódu aplikace zpracovávat výpadku oblasti čtení. Nakonec při ovlivněné oblasti je zpátky do online režimu, budou automaticky synchronizovat s aktuální oblasti pro zápis dříve ovlivněné oblasti čtení a bude možné znovu pro poskytování čtení. Další čtení se přesměrují na obnoveném oblasti nevyžaduje žádné změny kódu aplikace. Během převzetí služeb při selhání i některých dříve nezdařené oblasti záruky konzistence čtení dál respektovat Cosmos DB.

- Účty v jedné oblasti může dojít ke ztrátě dostupnosti v případě regionálního výpadku. Důrazně doporučujeme nastavit alespoň dvou oblastech (pokud možno měly aspoň dva oblasti) pomocí svého účtu Cosmos k zajištění vysoké dostupnosti po celou dobu.

## <a name="building-highly-available-applications"></a>Vytváření aplikací s vysokou dostupností

- K zajištění vysoké zápisu a dostupnost pro čtení, nastavte v účtu Cosmos rozložit alespoň dvě oblasti s několika oblasti. Tato konfigurace zajistí nejlepší dostupnost, nejnižší latenci a škálovatelnost pro čtení a zápisy, podložená smlouvami SLA. V tématu Jak [nakonfigurujte svůj účet Cosmos s využitím více oblastí zápisu](tutorial-global-distribution-sql-api.md).

- Pro Cosmos účty ve více oblastech, které jsou nakonfigurovány s oblastí zápisu jedním povolte pomocí webu Azure portal nebo rozhraní příkazového řádku Azure "automatické – převzetí služeb při selhání".  Když povolíte automatické převzetí služeb při selhání, vždycky, když je regionálního, Cosmos DB bude automaticky převzetí služeb při selhání vašeho účtu.  

- I v případě, že je váš účet Cosmos s vysokou dostupností, aplikace nemusí správně navržená k dispozici. Chcete-li zajistit vysokou dostupnost začátku do konce pro vaši aplikaci, pravidelně vyvolat "ruční převzetí služeb při selhání" pomocí webu Azure portal nebo rozhraní příkazového řádku Azure jako součást vašeho testování aplikace nebo zotavení po havárii (DR) operací. Další informace najdete v tématu změnu regionální priorit pro váš účet Cosmos).  

## <a name="next-steps"></a>Další postup

Další informace o škálování propustnosti v následujícím článku:

* [Škálování propustnosti](scaling-throughput.md)

* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)

* [Globální škálování propustnosti](scaling-throughput.md)

* [Globální distribuce - pod pokličkou](global-dist-under-the-hood.md)

* [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md)


