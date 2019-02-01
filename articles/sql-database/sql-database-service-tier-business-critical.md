---
title: Obchodní vrstva kritické – služba Azure SQL Database | Dokumentace Microsoftu
description: Další informace o Azure SQL Database pro důležité obchodní informace vrstvy
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: ca907e145067043b41dd6000248d44ecc1e3434f
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510438"
---
# <a name="business-critical-tier---azure-sql-database"></a>Obchodní vrstva kritické – Azure SQL Database

> [!NOTE]
> Úroveň pro důležité obchodní je volána v model nákupu DTU úrovně Premium. Porovnání nákupní model s založený na DTU nákupní model založený na virtuálních jádrech najdete v tématu [nákupu modely a prostředků Azure SQL Database](sql-database-service-tiers.md).

Azure SQL Database je založené na architektuře databázový stroj SQL serveru, která je upravená pro cloudové prostředí, aby bylo možné zajistit 99,99 % dostupnost i v případě selhání infrastruktury. Existují tři modely architektury, které se používají ve službě Azure SQL Database:
- Obecné účely nebo Standard 
- Kritické obchodní nebo Premium
- Hyperškálování

Model vrstvy úrovně Premium/důležitých služby je založen na cluster procesy modulu databáze. Tento Architektonický model závisí na skutečnost, že existuje, bude vždy Kvorum uzlů k dispozici databáze modul a má minimální vliv zatížení vliv na vaši sadu funkcí i během údržby nevyžadovala.

Azure upgradů a oprav základního operačního systému, ovladačů a databázový stroj SQL serveru transparentně s minimálními výpadkům pro koncové uživatele. 

Premium dostupnosti je povolený v úrovních Premium a pro důležité obchodní informace služby Azure SQL Database a je určeno pro intenzivní úlohy, které nemůžou tolerovat ovlivnit výkon z důvodu operace průběžnou údržbu.

V modelu úrovně premium Azure SQL database integruje výpočetní výkon a úložiště na jeden uzel. Zajištění vysoké dostupnosti v této architektuře modelu se dosahuje prostřednictvím replikace výpočetního prostředí (SQL Server Database Engine proces) a úložiště (místně připojených jednotkách SSD) nasazené v clusteru se čtyřmi uzly pomocí technologie podobný SQL serveru [Always On Skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Databázové stroje uzly clusteru](media/sql-database-managed-instance/business-critical-service-tier.png)

Obě SQL database proces modulu a základní, které soubory mdf a ldf jsou umístěny na stejném uzlu s místně připojené úložiště SSD, které poskytuje nízkou latenci pro vaši úlohu. Vysoká dostupnost je implementována pomocí technologie podobný SQL serveru [skupin dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Každá databáze je cluster databázové uzly se jedna primární databáze, který je přístupný pro úlohu zákazníka a tři sekundární procesy kopie dat, který obsahuje. Primárního uzlu neustále nasdílí změny do sekundární uzly aby se zajistilo, že data jsou k dispozici na sekundárních replikách, pokud z nějakého důvodu dojde k chybě primárního uzlu. Převzetí služeb při selhání se zpracovává souborem databázový stroj SQL serveru – jedna sekundární replika se stane primárním uzlu a zajistit dostatek uzlů v clusteru se vytvoří sekundární repliku. Zatížení je automaticky přesměrován na nový primární uzel.

Kromě toho pro důležité obchodní informace clusteru má integrovanou [horizontální navýšení kapacity pro čtení](sql-database-read-scale-out.md) funkci, která poskytuje zdarma z účtovat předdefinovaný uzel jen pro čtení, který slouží ke spouštění jen pro čtení dotazů (například sestavy), které by neměla mít vliv na výkon primární úlohy.

## <a name="when-to-choose-this-service-tier"></a>Kdy zvolit tuto úroveň služeb?

Obchodní kritické služby vrstva je určená pro aplikace, které vyžadují nízkou latenci odpovědi od základního úložiště SSD (v průměru 1 až 2 ms), rychlé obnovení selže základní infrastruktury nebo nutnost vyvážila sestavy, analýzy a jen pro čtení dotazy k bezplatné poplatek čitelnou sekundární repliku primární databáze.

## <a name="next-steps"></a>Další postup

- Další informace o [Obecné](sql-database-service-tier-general-purpose.md) a [Hyperškálovatelného](sql-database-service-tier-hyperscale.md) úrovně.
- Další informace o [Service Fabric](../service-fabric/service-fabric-overview.md).
- Další možnosti pro vysokou dostupnost a zotavení po havárii najdete v tématu [kontinuita podnikových procesů](sql-database-business-continuity.md).
