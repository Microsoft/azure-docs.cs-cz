---
title: Glosář termínů
titleSuffix: Azure SQL
description: Glosář podmínek pro práci s Azure SQL Database, Azure SQL Managed instance a SQL na virtuálním počítači Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/09/2020
ms.openlocfilehash: 8bd1e312463cf89ae30d54bc4a32a497fc7a9a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98249648"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database Glosář pojmů
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Azure SQL Database

|Kontext|Označení|Další informace|
|:---|:---|:---|
|Služba Azure|Azure SQL Database nebo SQL Database|[Azure SQL Database](database/sql-database-paas-overview.md)|
|Nákupní model|Nákupní model založený na DTU|[Nákupní model založený na DTU](database/service-tiers-dtu.md)|
||Nákupní model založený na virtuálních jádrech|[Nákupní model založený na virtuálních jádrech](database/service-tiers-vcore.md)|
|Možnost nasazení |Izolovaná databáze|[Izolované databáze](database/single-database-overview.md)|
||Elastický fond|[Elastický fond](database/elastic-pool-overview.md)|
|Úroveň služby|Basic, Standard, Premium, Pro obecné účely, škálování Pro důležité obchodní informace|Pro úrovně služeb v modelu vCore si přečtěte téma [SQL Database úrovně služeb](database/service-tiers-vcore.md#service-tiers). Pro úrovně služeb v modelu DTU si přečtěte téma [model DTU](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers).|
|Úroveň výpočetních prostředků|Bezserverové výpočetní prostředí|[Bezserverové výpočetní prostředí](database/service-tiers-vcore.md#compute-tiers)
||Zřízené výpočetní prostředky|[Zřízené výpočetní prostředky](database/service-tiers-vcore.md#compute-tiers)
|Generování výpočtů|Gen5, řady M-Series, Fsv2-Series, DC-Series|[Generace hardwaru](database/service-tiers-vcore.md#hardware-generations)
|Entita serveru| Server |[Logické SQL servery](database/logical-servers.md)|
|Typ prostředku|Virtuální jádro|PROCESORové jádro poskytované výpočetním prostředkem pro izolovanou databázi a elastický fond. |
||Velikost a velikost úložiště pro výpočetní prostředky|Výpočetní velikost je maximální množství prostředků procesoru, paměti a dalších nesouvisejících prostředků, které jsou k dispozici pro jednu databázi nebo elastický fond.  Velikost úložiště je maximální velikost úložiště, které je dostupné pro jednu databázi nebo elastický fond. Možnosti změny velikosti v modelu vCore najdete v tématu [Vcore Single](database/resource-limits-vcore-single-databases.md)Database a [Vcore elastické fondy](database/resource-limits-vcore-elastic-pools.md).  (.. /managed-instance/resource-limits.md).  Možnosti změny velikosti v modelu DTU najdete v tématu [DTU](database/resource-limits-dtu-single-databases.md) – izolované databáze a [elastické fondy DTU](database/resource-limits-dtu-elastic-pools.md).

## <a name="azure-sql-managed-instance"></a>Spravovaná instance Azure SQL

|Kontext|Označení|Další informace|
|:---|:---|:---|
|Služba Azure|Spravovaná instance Azure SQL|[Spravovaná instance SQL](managed-instance/sql-managed-instance-paas-overview.md)|
|Nákupní model|Nákupní model založený na virtuálních jádrech|[Nákupní model založený na virtuálních jádrech](database/service-tiers-vcore.md)|
|Možnost nasazení |Jedna instance|[Jediná instance](managed-instance/sql-managed-instance-paas-overview.md)|
||Fond instancí (Preview)|[Fondy instancí](managed-instance/instance-pools-overview.md)|
|Úroveň služby|Pro obecné účely Pro důležité obchodní informace|[Úrovně služeb spravované instance SQL](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|Úroveň výpočetních prostředků|Zřízené výpočetní prostředky|[Zřízené výpočetní prostředky](database/service-tiers-vcore.md#compute-tiers)|
|Generování výpočtů|Gen5|[Generace hardwaru](database/service-tiers-vcore.md#hardware-generations)
|Entita serveru|Spravovaná instance nebo instance| Není k dispozici, protože spravovaná instance SQL je sama o sobě serverem. |
|Typ prostředku|Virtuální jádro|Jádro procesoru poskytované výpočetním prostředkem pro spravovanou instanci SQL|
||Velikost a velikost úložiště pro výpočetní prostředky|Výpočetní velikost je maximální množství prostředků procesoru, paměti a dalších prostředků, které nesouvisí s úložištěm pro spravovanou instanci SQL.  Velikost úložiště je maximální velikost úložiště, které je k dispozici pro spravovanou instanci SQL.  Pro možnosti změny velikosti, [spravované instance SQL](managed-instance/resource-limits.md). |
