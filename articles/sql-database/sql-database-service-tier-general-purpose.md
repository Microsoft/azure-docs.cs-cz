---
title: Úroveň služby pro obecné účely – Azure SQL Database | Microsoft Docs
description: Další informace o Azure SQL Database úrovni pro obecné účely
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
ms.openlocfilehash: 2573adcb199ab32467493729842b6c47e3add64b
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515294"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Úroveň služby pro obecné účely – Azure SQL Database

> [!NOTE]
> Úroveň služby pro obecné účely v rámci nákupního modelu založeného na vCore se označuje jako standardní úroveň služby v rámci nákupního modelu založeného na DTU. Porovnání nákupního modelu založeného na DTU v vCore s nákupním modelem založeným na DTU najdete v tématu [Azure SQL Database nákupu modelů a prostředků](sql-database-purchase-models.md).

Azure SQL Database vychází z architektury SQL Server databázového stroje přizpůsobeného pro cloudové prostředí, aby se zajistila 99,99% dostupnost i v případě selhání infrastruktury. V Azure SQL Database se používají tři úrovně služeb, z nichž každá má různé modely architektury. Tyto úrovně služeb jsou:

- Pro obecné účely
- Pro důležité obchodní informace
- Hyperškálování

Model architektury pro obecné účely úrovně služby je založený na oddělení výpočetních prostředků a úložiště. Tento model architektury spoléhá na vysokou dostupnost a spolehlivost úložiště objektů BLOB v Azure, které transparentně replikuje soubory databáze a nezaručuje žádnou ztrátu dat, pokud dojde k selhání základní infrastruktury.

Následující obrázek znázorňuje čtyři uzly v modelu standardního architektury s oddělenými výpočetními a úložnými vrstvami.

![Oddělení výpočtů a úložiště](media/sql-database-managed-instance/general-purpose-service-tier.png)

V modelu architektury pro vrstvu služby pro obecné účely jsou k dispozici dvě vrstvy:

- Bezstavová výpočetní vrstva, která spouští `sqlserver.exe` proces a obsahuje pouze přechodná data a data uložená v mezipaměti (například mezipaměť plánu, fond vyrovnávacích pamětí, fond úložiště sloupců). Tento bezstavový SQL Server uzel provozuje služba Azure Service Fabric, která inicializuje proces, řídí stav uzlu a v případě potřeby provádí převzetí služeb při selhání na jiné místo.
- Stavová Datová vrstva s databázovými soubory (. mdf/. ldf), které jsou uložené v úložišti objektů BLOB v Azure. Služba Azure Blob Storage zaručuje, že nedojde ke ztrátě dat žádného záznamu, který je umístěný v žádném databázovém souboru. Azure Storage má vestavěnou dostupnost a redundanci dat, která zajišťuje, že všechny záznamy v souboru protokolu nebo na stránce v datovém souboru budou zachovány i v případě selhání procesu SQL Server.

Pokaždé, když je databázový stroj nebo operační systém upgradovaný, některá část základní infrastruktury selže nebo pokud se v SQL Server procesu zjistí nějaký kritický problém, Azure Service Fabric přesune bezstavový SQL Server proces do jiného bezstavového výpočetního uzlu. Existuje sada náhradních uzlů, které čekají na spuštění nové výpočetní služby, pokud dojde k převzetí služeb při selhání primárního uzlu za účelem minimalizace doby převzetí služeb při selhání. Data ve vrstvě úložiště Azure nejsou ovlivněná a soubory dat a protokolů jsou připojené k nově inicializovanému procesu SQL Server. Tento proces garantuje 99,99% dostupnost, ale může mít dopad na výkon náročné na náročné úlohy, která běží kvůli době přechodu a faktu, že nový uzel SQL Server začíná s studenou mezipamětí.

## <a name="when-to-choose-this-service-tier"></a>Kdy zvolit tuto úroveň služby

Úroveň služby Pro obecné účely je výchozí úroveň služby v Azure SQL Database, která je určená pro většinu obecných úloh. Pokud potřebujete plně spravovaný databázový stroj s 99,99% smlouvou SLA s latencí úložiště mezi 5 a 10 MS, který odpovídá Azure SQL IaaS ve většině případů, je pro vás možnost Pro obecné účely úroveň.

## <a name="next-steps"></a>Další kroky

- Vyhledá charakteristiky prostředků (počet jader, vstupně-výstupních operací, paměti) Pro obecné účely/standardní úrovně ve [spravované instanci](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), izolovaném databázi v [modelu Vcore](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-for-provisioned-compute) nebo [modelu DTU](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)nebo elastickém fondu v modelu [Vcore](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes) a modelu [DTU](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Přečtěte si o [pro důležité obchodní informace](sql-database-service-tier-business-critical.md) a úrovních [škálování](sql-database-service-tier-hyperscale.md) .
- Přečtěte si o [Service Fabric](../service-fabric/service-fabric-overview.md).
- Další možnosti pro vysokou dostupnost a zotavení po havárii najdete v tématu [Kontinuita podnikových aplikací](sql-database-business-continuity.md).
