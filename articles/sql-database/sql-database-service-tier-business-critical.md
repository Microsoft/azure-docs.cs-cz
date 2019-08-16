---
title: Azure SQL Database služba Pro důležité obchodní informace úrovně | Microsoft Docs
description: Další informace o vrstvě Azure SQL Database Pro důležité obchodní informace
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 48cde2f96083779bdeb13ba5f39b68c18b395045
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515373"
---
# <a name="business-critical-tier---azure-sql-database"></a>Pro důležité obchodní informace úroveň – Azure SQL Database

> [!NOTE]
> Pro důležité obchodní informace úroveň se označuje jako Premium v modelu nákupu DTU. Porovnání nákupního modelu založeného na DTU v vCore s nákupním modelem založeným na DTU najdete v tématu [Azure SQL Database nákupu modelů a prostředků](sql-database-purchase-models.md).

Azure SQL Database vychází z architektury SQL Server databázového stroje, která je upravena pro cloudové prostředí s cílem zajistit 99,99% dostupnost i v případě selhání infrastruktury. Existují tři modely architektury, které se používají v Azure SQL Database:
- Pro obecné účely/Standard 
- Pro důležité obchodní informace/Premium
- Hyperškálování

Model úrovně služeb Premium/Pro důležité obchodní informace je založený na clusteru procesů databázového stroje. Tento model architektury spoléhá na skutečnost, že je vždy kvorum dostupných uzlů databázového stroje a má minimální dopad na výkon na vaše úlohy, a to i během aktivit údržby.

Upgrady a opravy Azure odchází z operačního systému, ovladačů a SQL Server databázového stroje s minimálním časovým intervalem pro koncové uživatele. 

Dostupnost Premium je povolená v úrovni Premium a Pro důležité obchodní informace úrovně služeb Azure SQL Database a je navržená pro náročné úlohy, které nemůžou tolerovat dopad na výkon kvůli probíhajícím operacím údržby.

V modelu Premium Azure SQL Database integruje výpočetní prostředky a úložiště na jednom uzlu. Vysoká dostupnost v tomto modelu architektury se dosahuje replikací výpočetních prostředků (SQL Server procesu databázového stroje) a úložiště (místně připojené jednotky SSD) nasazených ve čtyřech uzlech clusteru s použitím technologie podobně jako SQL Server [skupiny dostupnosti Always On. ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster uzlů databázového stroje](media/sql-database-managed-instance/business-critical-service-tier.png)

Proces SQL Database Engine i zdrojové soubory MDF a LDF jsou umístěné na stejném uzlu s místně připojeným úložištěm SSD, které poskytuje nízkou latenci pro vaše zatížení. Vysoká dostupnost se implementuje pomocí technologie podobné SQL Server [skupinám dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Každá databáze je cluster databázových uzlů s jednou primární databází, která je přístupná pro úlohy zákazníka, a třemi sekundárními procesy, které obsahují kopie dat. Primární uzel průběžně přenáší změny do sekundárních uzlů, aby bylo zajištěno, že data jsou k dispozici na sekundárních replikách, pokud dojde k selhání primárního uzlu z jakéhokoli důvodu. Převzetí služeb při selhání je zpracováváno databázovým strojem SQL Server – jedna sekundární replika se stal primárním uzlem a vytvoří se nová sekundární replika, která zajistí dostatek uzlů v clusteru. Zatížení se automaticky přesměruje na nový primární uzel.

Kromě toho Pro důležité obchodní informace cluster obsahuje integrovanou možnost [škálování pro čtení](sql-database-read-scale-out.md) , která poskytuje bezplatný integrovaný uzel jen pro čtení, který se dá použít ke spouštění dotazů jen pro čtení (například sestav), které by neměly mít vliv na výkon vaší primární služby. úlohy.

## <a name="when-to-choose-this-service-tier"></a>Kdy zvolit tuto úroveň služby?

Úroveň služby Pro důležité obchodní informace je navržená pro aplikace, které vyžadují odezvy s nízkou latencí z podkladového úložiště SSD (průměrně 1-2 MS), rychlé obnovení v případě, že se podkladová infrastruktura nezdařila nebo pokud je potřeba mimo jiné načítat sestavy, analýzy a jen pro čtení. dotazuje se na bezplatně čitelnou sekundární repliku primární databáze.

## <a name="next-steps"></a>Další kroky

- Vyhledá charakteristiky prostředků (počet jader, vstupně-výstupních operací, paměti) Pro důležité obchodní informace úrovně ve [spravované instanci](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), izolovanou databázi v [modelu Vcore](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute) nebo [modelu DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)nebo elastický fond v modelu [Vcore](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes) a modelu [DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Přečtěte si o [pro obecné účely](sql-database-service-tier-general-purpose.md) a úrovních [škálování](sql-database-service-tier-hyperscale.md) .
- Přečtěte si o [Service Fabric](../service-fabric/service-fabric-overview.md).
- Další možnosti pro vysokou dostupnost a zotavení po havárii najdete v tématu [Kontinuita podnikových aplikací](sql-database-business-continuity.md).
