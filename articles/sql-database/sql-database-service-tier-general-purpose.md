---
title: Úrovni obecné účely – služba Azure SQL Database | Dokumentace Microsoftu
description: Další informace o úrovni Azure SQL Database pro obecné účely
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 8b708818584be6cdb84530ce008295ccee080d94
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882467"
---
# <a name="general-purpose-tier---azure-sql-database"></a>Úrovni obecné účely – Azure SQL Database

> [!NOTE]
> Ve model nákupu DTU nazývá obecné účely úrovně Standard. Porovnání nákupní model s založený na DTU nákupní model založený na virtuálních jádrech najdete v tématu [nákupu modely a prostředků Azure SQL Database](sql-database-service-tiers.md).

Azure SQL Database je založené na architektuře databázový stroj SQL serveru upravené pro cloudovém prostředí, aby bylo možné zajistit 99,99 % dostupnost i v případě selhání infrastruktury. Existují tři modely architektury, které se používají ve službě Azure SQL Database:
- Obecné použití 
- Pro důležité obchodní informace
- Hyperškálování

Obecné účely model je založen na oddělení výpočetního výkonu a úložiště. Tento Architektonický model spoléhá na vysokou dostupnost a spolehlivost Azure Premium Storage, který transparentně replikuje soubory databáze a pokud základní infrastruktury selhání zaručuje ztrátě dojde.

Následující obrázek znázorňuje čtyři uzly ve standardní Architektonický model s oddělenými výpočetní a úložnou vrstvu.

![Oddělení výpočetního výkonu a úložiště](media/sql-database-managed-instance/general-purpose-service-tier.png)

V modelu obecné účely jsou dvě vrstvy:

- Bezstavové výpočetní vrstvě, na kterém běží `sqlserver.exe` zpracování a obsahuje pouze přechodné a uložená v mezipaměti dat (například – mezipaměti plánu, fondu vyrovnávací paměti, fondu úložiště sloupce). Azure Service Fabric, který inicializuje procesu, řídí stav uzlu a provede převzetí služeb při selhání na jiné místo v případě potřeby je provozována této bezstavové uzlu serveru SQL Server.
- Stavová data vrstvy se soubory databáze (.mdf/.ldf), které jsou uložené ve službě Azure Premium Storage. Azure Storage zaručuje, že bude bez ztráty dat u všech záznamů, který je umístěn v žádném souboru databáze. Azure Storage má předdefinované datové dostupnost a redundance, který zajistí, že každý záznam v souboru protokolu nebo stránky v datovém souboru budou zachovány, i když dojde k chybě procesu serveru SQL Server.

Pokaždé, když se upgraduje operační systém nebo databázový stroj, některá část základní infrastruktury selže nebo pokud se zjistí některé kritický problém v procesu serveru SQL Server, Azure Service Fabric se přesune bezstavové procesu serveru SQL Server na jiný bezstavové výpočetní uzel. Je sada uzlů za chodu, který čeká na spuštění nové výpočetní služby, pokud převzetí služeb při selhání z primárního uzlu se stane, aby se minimalizoval čas převzetí služeb při selhání. Data ve vrstvě služby Azure Storage to neovlivní a data/log soubory jsou připojeny k nově inicializované procesu serveru SQL Server. Tento postup zaručuje 99,99 % dostupnost, ale může mít některé vliv na funkčnost v případě velkého zatížení, které běží kvůli čas přechodu a fakt nový uzel SQL serveru začíná studenou mezipaměti.

## <a name="when-to-choose-this-service-tier"></a>Kdy zvolit tuto úroveň služeb?

Obecné účely úrovně služeb je výchozí úroveň služby ve službě Azure SQL Database, která je navržená pro většinu obecných úloh. Pokud potřebujete modul je plně spravovaná databáze s 99,99 % smlouva SLA s latencí úložiště mezi 5 až 10 ms, které odpovídá Azure SQL IaaS ve většině případů, úrovni General Purpose je možnost za vás.

## <a name="next-steps"></a>Další postup

- Další informace o [pro důležité obchodní informace](sql-database-service-tier-business-critical.md) a [Hyperškálovatelného](sql-database-service-tier-hyperscale.md) úrovně.
- Další informace o [Service Fabric](../service-fabric/service-fabric-overview.md).
- Další možnosti pro vysokou dostupnost a zotavení po havárii najdete v tématu [kontinuita podnikových procesů](sql-database-business-continuity.md).
