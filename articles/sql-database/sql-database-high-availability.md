---
title: Vysoká dostupnost – Azure SQL Database služba | Microsoft Docs
description: Seznamte se s funkcemi a funkcemi pro vysokou dostupnost služby Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 06/10/2019
ms.openlocfilehash: 54994dd626df23694ea372d4a662d2b4fb051fc8
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285757"
---
# <a name="high-availability-and-azure-sql-database"></a>Vysoká dostupnost a Azure SQL Database

Cílem architektury vysoké dostupnosti v Azure SQL Database je zaručit, že je vaše databáze v provozu a bude běžet 99,99% času, aniž byste se museli starat o dopad na operace údržby a výpadky. Azure automaticky zpracovává důležité úlohy údržby, jako jsou třeba opravy, zálohování, upgrady Windows a SQL a také neplánované události, jako je například základní hardware, software nebo chyba sítě.  Pokud je podkladová instance SQL opravena nebo převezme služby při selhání, nemůžete si všimnout, pokud ve své aplikaci použijete [logiku opakování](sql-database-develop-overview.md#resiliency) . Azure SQL Database můžete rychle obnovit i v nejdůležitějších případech, které zajistí, že vaše data jsou vždycky k dispozici.

Řešení vysoké dostupnosti je navrženo tak, aby se zajistilo, že potvrzená data nejsou nikdy ztracena z důvodu selhání, že operace údržby neovlivní vaše zatížení a že databáze nebude v rámci softwarové architektury jediným bodem selhání. Neexistují žádná časová období údržby ani výpadky, které by měly vyžadovat, abyste při upgradu nebo údržbě databáze zastavili úlohy. 

K dispozici jsou dva modely architektury s vysokou dostupností, které se používají v Azure SQL Database:

- Standardní model dostupnosti založený na oddělení výpočetních prostředků a úložiště.  Spoléhá se na vysokou dostupnost a spolehlivost vzdálené vrstvy úložiště. Tato architektura cílí na rozpočtově orientované podnikové aplikace, které mohou tolerovat některé snížení výkonu během údržby.
- Model dostupnosti Premium, který je založen na clusteru procesů databázového stroje. Spoléhá na to, že je vždy kvorum dostupných uzlů databázového stroje. Tato architektura cílí na kritické aplikace s vysokými nároky na vstupně-výstupní operace, vysokou přenosovou rychlostí a zaručuje minimální dopad na výkon na vaše zatížení během aktivit údržby.

Azure SQL Database běží na nejnovější stabilní verzi SQL Server databázový stroj a operační systém Windows a většina uživatelů si nevšimnete, že se upgradují průběžně.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Dostupnost úrovně služeb Basic, Standard a Pro obecné účely

Tyto úrovně služeb využívají standardní architekturu dostupnosti. Následující obrázek znázorňuje čtyři různé uzly s oddělenými výpočetními a úložnými vrstvami.

![Oddělení výpočtů a úložiště](media/sql-database-high-availability/general-purpose-service-tier.png)

Standardní model dostupnosti obsahuje dvě vrstvy:

- Bezstavová výpočetní vrstva, která spouští proces @no__t 0 a obsahuje pouze přechodná a uložená data v mezipaměti připojené jednotky SSD, jako je například TempDB, modelová databáze, mezipaměť plánu, fond vyrovnávacích pamětí a fond úložiště sloupců. Tento bezstavový uzel provozuje služba Azure Service Fabric, která inicializuje `sqlserver.exe`, řídí stav uzlu a v případě potřeby provádí převzetí služeb při selhání jiným uzlem.
- Stavová Datová vrstva s databázovými soubory (. mdf/. ldf), které jsou uložené v úložišti objektů BLOB v Azure. Služba Azure Blob Storage má vestavěnou funkci dostupnosti dat a redundance. Zaručuje, že každý záznam v souboru protokolu nebo na stránce v datovém souboru se zachová i v případě selhání procesu SQL Server.

Pokaždé, když je databázový stroj nebo operační systém upgradovaný, nebo dojde k selhání, Azure Service Fabric přesune bezstavový proces SQL Server na jiný bezstavový výpočetní uzel s dostatečnou volnou kapacitou. Data v úložišti objektů BLOB v Azure nejsou ovlivněná přesunem a soubory dat a protokolů jsou připojené k nově inicializovanému procesu SQL Server. Tento proces garantuje 99,99% dostupnost, ale během přechodu může dojít k výraznému snížení výkonu, protože nová instance SQL Server začíná studenou mezipamětí.

## <a name="premium-and-business-critical-service-tier-availability"></a>Dostupnost úrovně služeb Premium a Pro důležité obchodní informace

Úrovně služeb Premium a Pro důležité obchodní informace využívají model dostupnosti Premium, který integruje výpočetní prostředky (SQL Server proces databázového stroje) a úložiště (místně připojená jednotka SSD) na jednom uzlu. Vysoká dostupnost se dosahuje replikací výpočetních prostředků i úložiště do dalších uzlů vytvářejících cluster se čtyřmi uzly. 

![Cluster uzlů databázového stroje](media/sql-database-high-availability/business-critical-service-tier.png)

Podkladové soubory databáze (. mdf/. ldf) jsou umístěné v připojeném úložišti SSD, aby bylo možné zajistit velmi nízkou latenci v/v pro vaše zatížení. Vysoká dostupnost se implementuje pomocí technologie podobné SQL Server [skupinám dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cluster zahrnuje jednu primární repliku (SQL Server proces), která je přístupná pro úlohy zákazníka pro čtení i zápis, a až tři sekundární repliky (výpočetní výkon a úložiště) obsahující kopie dat. Primární uzel průběžně přenáší změny do sekundárních uzlů v pořadí a před potvrzením každé transakce zajišťuje, aby byla data synchronizována do alespoň jedné sekundární repliky. Tento postup zaručuje, že pokud dojde k selhání primárního uzlu z jakéhokoli důvodu, je vždy plně synchronizovaný uzel, který převezme služby při selhání. Převzetí služeb při selhání iniciuje Service Fabric Azure. Jakmile se sekundární replika pokusí o nový primární uzel, vytvoří se další sekundární replika, která zajistí, že cluster má dostatek uzlů (sada kvora). Po dokončení převzetí služeb při selhání se připojení SQL automaticky přesměrují do nového primárního uzlu.

Model dostupnosti Premium navíc nabízí možnost přesměrovat připojení SQL jen pro čtení na jednu ze sekundárních replik. Tato funkce se nazývá horizontální navýšení [kapacity čtení](sql-database-read-scale-out.md). Poskytuje 100% dodatečnou výpočetní kapacitu bez dalších poplatků za vypínání operací jen pro čtení, jako jsou analytické úlohy, z primární repliky.

### <a name="zone-redundant-configuration"></a>Redundantní konfigurace zóny

Ve výchozím nastavení se cluster uzlů pro model dostupnosti Premium vytvoří ve stejném datovém centru. Při zavedení [zóny dostupnosti Azure](../availability-zones/az-overview.md)můžou SQL Database umístit různé repliky databáze pro důležité obchodní informace do různých zón dostupnosti ve stejné oblasti. Chcete-li eliminovat jediný bod selhání, je řídicí kanál také duplikován v několika zónách jako tři prstence brány (GS). Směrování na konkrétního okruhu brány se řídí službou [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Vzhledem k tomu, že zóna redundantní konfigurace v úrovních služby Premium nebo Pro důležité obchodní informace nevytváří další redundanci databáze, můžete ji povolit bez dalších poplatků. Výběrem redundantní konfigurace zóny můžete zajistit, aby vaše databáze Premium nebo Pro důležité obchodní informace odolné vůči mnohem většímu počtu selhání, včetně závažných výpadků Datacenter, a to bez jakýchkoli změn v aplikační logice. Můžete také převést všechny existující databáze nebo fondy Premium nebo Pro důležité obchodní informace na zónu redundantní konfigurace.

Vzhledem k tomu, že redundantní databáze zóny mají v různých datových centrech repliky s určitou vzdáleností, zvýšení latence sítě může zvýšit dobu potvrzení a tím ovlivnit výkon některých OLTP úloh. Do konfigurace s jednou zónou se můžete kdykoli vrátit tím, že zakážete nastavení redundance zóny. Tento proces je online operace podobná standardnímu upgradu vrstvy služeb. Na konci procesu je databáze nebo fond migrován z zóny redundantního vyzvánění do jediného okruhu zóny nebo naopak.

> [!IMPORTANT]
> Redundantní databáze zóny a elastické fondy se momentálně podporují jenom v úrovních služby Premium a Pro důležité obchodní informace ve vybraných oblastech. Při použití Pro důležité obchodní informace úrovně je redundantní konfigurace zóny dostupná jenom v případě, že je vybraný Gen5 výpočetní hardware. Aktuální informace o oblastech, které podporují redundantní databáze zóny, najdete v tématu [Podpora služeb v jednotlivých oblastech](../availability-zones/az-overview.md#services-support-by-region).  
> Tato funkce není k dispozici ve spravované instanci.

Redundantní verze architektury s vysokou dostupností v zóně je znázorněna v následujícím diagramu:

![redundantní zóna architektury vysoké dostupnosti](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Urychlené obnovení databáze (ADR)

[Accelerated Database Recovery (ADR)](sql-database-accelerated-database-recovery.md) je nová funkce stroje SQL Database, která významně vylepšuje dostupnost databáze, zejména v případě dlouhotrvajících transakcí. Pro jednotlivé databáze, elastické fondy a Azure SQL Data Warehouse jsou aktuálně k dispozici pravidla automatického nasazení.

## <a name="conclusion"></a>Závěr

Azure SQL Database nabízí integrované řešení s vysokou dostupností, které je hluboko integrované s platformou Azure. Je závislý na Service Fabric pro detekci selhání a obnovení, v úložišti objektů BLOB v Azure pro ochranu dat a na Zóny dostupnosti pro zajištění vyšší odolnosti proti chybám. Kromě toho využívá Azure SQL Database technologii skupiny dostupnosti Always On z SQL Server pro replikaci a převzetí služeb při selhání. Kombinace těchto technologií umožňuje aplikacím plně realizovat výhody modelu kombinovaného úložiště a podporuje nejnáročnější SLA.

## <a name="next-steps"></a>Další kroky

- Informace o [zóny dostupnosti Azure](../availability-zones/az-overview.md)
- Informace o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Přečtěte si o [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- Další možnosti pro vysokou dostupnost a zotavení po havárii najdete v tématu [Kontinuita podnikových aplikací](sql-database-business-continuity.md) .
