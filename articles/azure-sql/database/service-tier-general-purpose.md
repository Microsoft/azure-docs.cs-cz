---
title: Úroveň služby pro obecné účely
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Přečtěte si o úrovni služby pro obecné účely Azure SQL Database a službě Azure SQL Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 7fd446e6894c3c88701527bb2d3668a570888a06
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84049908"
---
# <a name="general-purpose-service-tier---azure-sql-database--sql-managed-instance"></a>Úroveň služby pro obecné účely – Azure SQL Database & spravované instance SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Úroveň služby pro obecné účely v rámci nákupního modelu založeného na vCore se označuje jako standardní úroveň služby v rámci nákupního modelu založeného na DTU. Porovnání nákupního modelu založeného na DTU vCore s modelem nákupu založeném na DTU najdete v tématu [nakupování modelů a prostředků](purchasing-models.md).

Azure SQL Database a Azure SQL Managed instance jsou založené na architektuře SQL Server databázového stroje přizpůsobené pro cloudové prostředí, aby se zajistila 99,99% dostupnost i v případě selhání infrastruktury. 

Azure SQL Database a spravované instance SQL používají dvě úrovně služeb: 

- Obecné účely
- Důležité pro podnikání

Azure SQL Database má také třetí úroveň služby, která není aktuálně k dispozici pro spravovanou instanci Azure SQL: 

- Hyperškálování

Model architektury pro obecné účely úrovně služby je založený na oddělení výpočetních prostředků a úložiště. Tento model architektury spoléhá na vysokou dostupnost a spolehlivost úložiště objektů BLOB v Azure, které transparentně replikuje soubory databáze a nezaručuje žádnou ztrátu dat, pokud dojde k selhání základní infrastruktury.

Následující obrázek znázorňuje čtyři uzly v modelu standardního architektury s oddělenými výpočetními a úložnými vrstvami.

![Oddělení výpočtů a úložiště](./media/service-tier-general-purpose/general-purpose-service-tier.png)

V modelu architektury pro vrstvu služby pro obecné účely jsou k dispozici dvě vrstvy:

- Bezstavová výpočetní vrstva, která spouští `sqlservr.exe` proces a obsahuje pouze přechodná data a data uložená v mezipaměti (například mezipaměť plánu, fond vyrovnávacích pamětí, fond úložiště sloupců). Tento bezstavový uzel provozuje služba Azure Service Fabric tím, že inicializuje proces, řídí stav uzlu a v případě potřeby provádí převzetí služeb při selhání na jiné místo.
- Stavová Datová vrstva s databázovými soubory (. mdf/. ldf), které jsou uložené v úložišti objektů BLOB v Azure. Služba Azure Blob Storage zaručuje, že nedojde ke ztrátě dat žádného záznamu, který je umístěný v žádném databázovém souboru. Azure Storage má vestavěnou dostupnost a redundanci dat, která zajišťuje, že se všechny záznamy v souboru protokolu nebo stránce v datovém souboru zachovají i v případě, že dojde k chybě procesu.

Pokaždé, když je databázový stroj nebo operační systém upgradován, některá část základní infrastruktury selže nebo pokud se v procesu zjistí nějaký kritický problém `sqlservr.exe` , Azure Service Fabric přesune bezstavový proces do jiného bezstavového výpočetního uzlu. Existuje sada náhradních uzlů, které čekají na spuštění nové výpočetní služby, pokud dojde k převzetí služeb při selhání primárního uzlu za účelem minimalizace doby převzetí služeb při selhání. Data ve vrstvě úložiště Azure nejsou ovlivněná a soubory dat a protokolů jsou připojené k nově inicializovanému procesu. Tento proces garantuje 99,99% dostupnost, ale může mít dopad na výkon u těžkých úloh, které běží kvůli době přechodu a faktu, že nový uzel začíná studenou mezipamětí.

## <a name="when-to-choose-this-service-tier"></a>Kdy zvolit tuto úroveň služby

Úroveň služby Pro obecné účely je výchozí úroveň služby v Azure SQL Database a Azure SQL Managed instance, která je určená pro většinu obecných úloh. Pokud potřebujete plně spravovaný databázový stroj s 99,99% smlouvou SLA s latencí úložiště mezi 5 a 10 MS, který odpovídá SQL Server na virtuálním počítači Azure ve většině případů, Pro obecné účely úroveň je pro vás možnost.

## <a name="next-steps"></a>Další kroky

- Vyhledá charakteristiky prostředků (počet jader, vstupně-výstupních operací, paměti) Pro obecné účely/standardní úrovně v [SQL Managed instance](../managed-instance/resource-limits.md#service-tier-characteristics), izolovaná databáze v [modelu Vcore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) nebo [modelu DTU](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)nebo elastický fond v modelu [Vcore](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4) a [modelu DTU](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits).
- Přečtěte si o [pro důležité obchodní informace](service-tier-business-critical.md) a úrovních [škálování](service-tier-hyperscale.md) .
- Přečtěte si o [Service Fabric](../../service-fabric/service-fabric-overview.md).
- Další možnosti pro vysokou dostupnost a zotavení po havárii najdete v tématu [Kontinuita podnikových aplikací](business-continuity-high-availability-disaster-recover-hadr-overview.md).
