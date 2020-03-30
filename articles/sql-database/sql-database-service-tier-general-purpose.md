---
title: Úroveň služby pro obecné účely
description: Informace o obecné úrovni Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 7c57755ae63f8af5a2a4faa4764bc6a9597e8c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255883"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Úroveň služeb pro obecné účely – Azure SQL Database

> [!NOTE]
> Úroveň služeb pro obecné účely v nákupním modelu založeném na virtuálních jádrech se nazývá standardní úroveň služeb v nákupním modelu založeném na DTU. Porovnání nákupního modelu založeného na virtuálních jádrech s nákupním modelem založeným na DTU najdete v [tématu Nákupní modely a prostředky azure SQL Database](sql-database-purchase-models.md).

Azure SQL Database je založen na architektuře databázového stroje SQL Serveru přizpůsobené pro cloudové prostředí, aby byla zajištěna 99,99% dostupnost i v případě selhání infrastruktury. Existují tři úrovně služeb, které se používají v Azure SQL Database, každý s různými architektonickými modely. Tyto úrovně služeb jsou:

- Obecné účely
- Důležité obchodní
- Hyperškálování

Architektonický model pro úroveň služby pro obecné účely je založen na oddělení výpočetních prostředků a úložiště. Tento architektonický model závisí na vysoké dostupnosti a spolehlivosti úložiště objektů blob Azure, které transparentně replikuje databázové soubory a zaručuje žádnou ztrátu dat, pokud dojde k selhání základní infrastruktury.

Následující obrázek znázorňuje čtyři uzly ve standardním architektonickém modelu s oddělenými výpočetními a úložnými vrstvami.

![Oddělení výpočetních prostředků a úložiště](media/sql-database-managed-instance/general-purpose-service-tier.png)

V architektonickém modelu pro úroveň služby pro obecné účely existují dvě vrstvy:

- Bezstavová výpočetní vrstva, `sqlservr.exe` která je spuštěna proces a obsahuje pouze přechodná data a data uložená v mezipaměti (například – plánování mezipaměti, fond vyrovnávacích pamětí, fond úložiště sloupců). Tento bezstavový uzel SQL Server je provozován Azure Service Fabric, který inicializuje proces, řídí stav uzlu a v případě potřeby provede převzetí služeb při selhání na jiné místo.
- Stavová datová vrstva s databázovými soubory (.mdf/.ldf), které jsou uložené v úložišti objektů Blob Azure. Úložiště objektů blob Azure zaručuje, že nedojde ke ztrátě dat jakéhokoli záznamu, který je umístěn v libovolném databázovém souboru. Azure Storage má integrovanou dostupnost a redundanci dat, která zajišťuje, že každý záznam v souboru protokolu nebo na stránce v datovém souboru bude zachován, i když dojde k chybě procesu SQL Serveru.

Kdykoli je upgradován databázový stroj nebo operační systém, některá část základní infrastruktury selže nebo pokud je v procesu SQL Serveru zjištěn nějaký kritický problém, Azure Service Fabric přesune proces bezstavového serveru SQL Server do jiného bezstavového výpočetního uzlu. Existuje sada náhradních uzlů, která čeká na spuštění nové výpočetní služby, pokud dojde k převzetí služeb při selhání primárního uzlu, aby se minimalizovala doba převzetí služeb při selhání. Data ve vrstvě úložiště Azure nejsou ovlivněna a soubory dat/protokolu se připojují k nově inicializovanému procesu SERVERU SQL Server. Tento proces zaručuje 99,99 % dostupnost, ale může mít některé dopady na výkon na velké zatížení, které je spuštěno z důvodu doby přechodu a skutečnosti, že nový uzel SQL Server začíná studenou mezipamětí.

## <a name="when-to-choose-this-service-tier"></a>Kdy zvolit tuto úroveň služby

Úroveň služby pro obecné účely je výchozí úroveň služeb v Azure SQL Database, která je určena pro většinu obecných úloh. Pokud potřebujete plně spravovaný databázový stroj s 99,99% sla s latencí úložiště mezi 5 a 10 ms, které odpovídají Azure SQL IaaS ve většině případů, úroveň obecného použití je možnost pro vás.

## <a name="next-steps"></a>Další kroky

- Najděte charakteristiky prostředků (počet jader, vstupně-to, paměť) úrovně Obecné účely/Standardní ve [spravované instanci](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), Jedna databáze v [modelu virtuálních jader](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) nebo [Model DTU](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)nebo Elastický fond v [modelu virtuálních jader](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) a [Model DTU](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Přečtěte si o [úrovních Business Critical](sql-database-service-tier-business-critical.md) a [Hyperscale.](sql-database-service-tier-hyperscale.md)
- Další informace o [service fabric](../service-fabric/service-fabric-overview.md).
- Další možnosti pro vysokou dostupnost a zotavení po havárii naleznete [v tématu Kontinuita provozu](sql-database-business-continuity.md).
