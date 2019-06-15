---
title: Vysoká dostupnost – služba Azure SQL Database | Dokumentace Microsoftu
description: Další informace o možnosti vysoké dostupnosti služby Azure SQL Database a funkcí
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 06/10/2019
ms.openlocfilehash: a88842802759a5c3ae7af7334bbe125344c978ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066918"
---
# <a name="high-availability-and-azure-sql-database"></a>Vysoká dostupnost a Azure SQL Database

Cílem architektura pro vysokou dostupnost ve službě Azure SQL Database je zaručeno, že je vaše databáze a spuštěné 99,99 % času, ale nemusíme se starat o dopad operace údržby a výpadků. Důležité úlohy údržby, jako použití dílčích oprav, zálohování, upgrady Windows a SQL, nebo neplánované události, například základní selhání hardwaru, softwaru nebo sítě se automaticky postará Azure.  Pokud základní instance SQL je opravit nebo převezme služby při selhání, výpadek není patrné Pokud jste [použít logiku opakování](sql-database-develop-overview.md#resiliency) ve vaší aplikaci. Azure SQL Database můžete rychle obnovit i v těch nejdůležitějších okolností zajistit, že vaše data budou vždy k dispozici.

Řešení vysoké dostupnosti je navržený tak, aby potvrzených dat se nikdy ztraceno v důsledku chyby, že operace údržby nemají vliv na vaše úlohy a, že databáze nebude jediný bod selhání v softwarové architektuře. Neexistují žádné časová období údržby nebo prostoje, které by měl vyžadují, abyste k zastavení úlohy a databáze je upgradovat nebo udržovat. 

Existují dva modely architektury vysoké dostupnosti, které se používají ve službě Azure SQL Database:

- Standardní dostupnosti modelu, který je založen na oddělení výpočetního výkonu a úložiště.  Spoléhá na vysokou dostupnost a spolehlivost na úrovni vzdálené úložiště. Tato architektura se zaměřuje levným provozem obchodních aplikací, které může tolerovat možnost, některé snížení výkonu během údržby nevyžadovala.
- Premium dostupnost model, který je založen na cluster procesy modulu databáze. Spoléhá na skutečnost, že je vždycky Kvorum uzlů pro modul databáze k dispozici. Tato architektura se zaměřuje nejdůležitější aplikace s vysokou četností transakcí vysoký výkon vstupně-výstupní operace a záruky dopad na vaše úlohy během údržby nevyžadovala minimální výkon.

Azure SQL Database běží na nejnovější stabilní verze databázového stroje SQL Server a operační systém Windows a většina uživatelů by Všimněte si, že průběžně probíhají upgrady.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Úroveň Basic, Standard a obecné úroveň dostupnosti služeb

Tyto úrovně služby využívají architekturu standardní dostupnosti. Následující obrázek znázorňuje čtyři různé uzly s oddělenými výpočetní a úložnou vrstvu.

![Oddělení výpočetního výkonu a úložiště](media/sql-database-high-availability/general-purpose-service-tier.png)

Tento model standardní dostupnosti zahrnuje dvě vrstvy:

- Bezstavové výpočetní vrstvě, na kterém běží `sqlserver.exe` zpracování a obsahuje pouze přechodné a uložená v mezipaměti dat na připojené SSD, jako např. databázi TempDB, model databáze, mezipaměti plánu, fond vyrovnávacích pamětí a sloupce úložiště fondu. Tento uzel bezstavové je provozována společností Azure Service Fabric, která inicializuje `sqlserver.exe`řídí stav uzlu a provede převzetí služeb při selhání do jiného uzlu v případě potřeby.
- Stavová data vrstvy se soubory databáze (.mdf/.ldf), které jsou uloženy v úložišti objektů Blob v Azure. Úložiště objektů blob v Azure má integrovaný dostupnost a redundance funkce. Zaručuje, že každý záznam v souboru protokolu nebo stránku v datovém souboru budou zachovány i v případě, že dojde k chybě procesu serveru SQL Server.

Pokaždé, když se upgraduje databázový stroj nebo operačního systému nebo selhání detekuje, přesuňte Azure Service Fabric bezstavové procesu serveru SQL Server na jiný uzel bezstavové výpočetní s dostatečnou kapacitu zdarma. Data v úložišti objektů Blob v Azure nemá vliv přesunutí a data/protokolové soubory jsou připojeny k nově inicializované procesu serveru SQL Server. Tento postup zaručuje 99,99 % dostupnost, ale případě velkého zatížení může docházet nějaké snížení výkonu během přechodu od spuštění nové instance SQL serveru s studenou mezipaměti.

## <a name="premium-and-business-critical-service-tier-availability"></a>Dostupnost úroveň služeb Premium a pro důležité obchodní informace

Premium a pro důležité obchodní informace služby využívají úrovně dostupnosti modelu Premium, která integruje výpočetní prostředky (databázový stroj SQL serveru proces) a úložiště (místně připojených jednotkách SSD) v jednom uzlu. Vysoké dostupnosti se dosahuje pomocí replikace výpočetní výkon a úložiště do dalších uzlů, vytvořte cluster tři až čtyři - uzlu. 

![Databázové stroje uzly clusteru](media/sql-database-high-availability/business-critical-service-tier.png)

Základní soubory databáze (.mdf/.ldf) jsou umístěny na připojené úložiště SSD poskytují velmi nízkou latenci vstupně-výstupních operací vaší úloze. Vysoká dostupnost je implementována pomocí technologie, podobně jako SQL Server [skupin dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cluster obsahuje jen jednu primární repliku (procesu serveru SQL Server), který je přístupný pro úlohy čtení a zápis zákazníků a až tři sekundární repliky (výpočetní výkon a úložiště) obsahující kopie dat. Primární uzel neustále nasdílí změny do sekundárních uzlů v pořadí a zajistí, že se data synchronizují na nejméně jedna sekundární replika před každou transakci. Tento postup zaručuje, že pokud z nějakého důvodu dojde k chybě a primární uzel, je vždy plně synchronizovaná uzel k převzetí služeb. Inicializuje převzetí služeb při selhání v Azure Service Fabric. Když sekundární replika stane novou primární uzel, jiné sekundární repliky se vytvoří Ujistěte se, že cluster má dostatek uzlů (sadu kvorum). Po dokončení převzetí služeb při selhání připojení SQL se automaticky přesměrují na nový primární uzel.

Další výhodou je model dostupnost premium zahrnuje možnost přesměrovat jen pro čtení připojení SQL jednou ze sekundárních replik. Tato funkce se nazývá [horizontální navýšení kapacity pro čtení](sql-database-read-scale-out.md). Nabízí 100 % další výpočetní kapacitu bez dalších poplatků k vyvážila jen pro čtení operací, jako je například analytické úlohy z primární repliky.

## <a name="zone-redundant-configuration"></a>Konfigurace redundantního zóny

Ve výchozím nastavení vytvoření clusteru uzlů pro model dostupnost premium ve stejném datacentru. Se zavedením [zóny dostupnosti Azure](../availability-zones/az-overview.md), SQL Database můžete umístit jiné repliky v clusteru do různých zón dostupnosti ve stejné oblasti. Chcete-li odstranit jediný bod selhání, je aktualizační kanál, který ovládací prvek duplicitní napříč několika zónami jako tři prstence brány (gs) také. Směrování do aktualizačního kanálu konkrétní gateway řídí [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Protože redundantní konfigurace zóny v úrovních služeb úrovně Premium nebo pro důležité obchodní informace neslouží k vytvoření redundance další databáze, můžete ho povolit bez dalších poplatků. Výběrem redundantní konfigurace zóny můžete provádět databází Premium nebo pro důležité obchodní informace odolné mnohem většímu množství chyb, včetně výpadků katastrofickými datacenter, bez nutnosti jakkoli měnit aplikace logiky. Můžete také převést libovolný existující Premium nebo pro důležité obchodní informace databáze nebo fondy redundantní konfigurace zóny.

Vzhledem k tomu zónu databáze redundantní repliky v různých datových centrech některé vzdálenost mezi nimi, latence sítě může prodloužit dobu potvrzení a tedy mít vliv na výkon některé úlohy OLTP. Můžete se kdykoli vrátit v konfiguraci s jednou zónou zakázáním nastavení redundance zóny. Tento proces je podobný upgrade úroveň regulární služby online operace. Na konci procesu databáze nebo fondu migrován z kanál redundantní zóny v jedné zóně kanál nebo naopak.

> [!IMPORTANT]
> Zóna redundantní databáze a elastické fondy jsou aktuálně podporuje jenom v úrovních služby úrovně Premium a pro důležité obchodní informace ve vybraných oblastech. Pokud používáte úroveň pro důležité obchodní informace, redundantní konfigurace je k dispozici pouze pokud je vybrána hardwaru compute Gen5. Aktuální informace o oblastech, které podporují zónu databáze redundantní najdete v tématu [služeb podpory podle oblasti](../availability-zones/az-overview.md#services-support-by-region).  

Zóna redundantní verzi architektury vysoké dostupnosti je znázorněn v následujícím diagramu:

![Vysoká dostupnost architektura zónově redundantní](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Obnovení databáze akcelerace (ADR)

[Obnovení databáze (ADR) Accelerated](sql-database-accelerated-database-recovery.md) běží novou funkci modul databáze SQL, která výrazně zlepšuje dostupnost databáze, zejména za přítomnosti dlouho transakce. Pravidla automatického nasazení je aktuálně dostupné pro izolované databáze, elastické fondy a Azure SQL Data Warehouse.

## <a name="conclusion"></a>Závěr

Azure SQL Database nabízí integrovanou vysokou dostupnost řešení, která je úzce integrovaná s platformou Azure. Je závislý na platformě Service Fabric pro zjištění selhání a obnovení, úložiště objektů Blob v Azure pro ochranu dat a zóny dostupnosti pro vyšší odolnost proti chybám. Kromě toho Azure SQL database využívá technologii skupiny dostupnosti AlwaysOn v systému SQL Server pro replikaci a převzetí služeb při selhání. Kombinací těchto technologií umožňuje aplikacím plně využít výhody hybridní úložiště modelu a podporují nejnáročnější smlouvy o úrovni služeb.

## <a name="next-steps"></a>Další postup

- Další informace o [zóny dostupnosti Azure](../availability-zones/az-overview.md)
- Další informace o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Další informace o [Azure Traffic Manageru](../traffic-manager/traffic-manager-overview.md)
- Další možnosti pro vysokou dostupnost a zotavení po havárii najdete v tématu [kontinuita podnikových procesů](sql-database-business-continuity.md)
