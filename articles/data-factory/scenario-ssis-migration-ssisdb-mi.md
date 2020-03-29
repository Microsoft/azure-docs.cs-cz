---
title: Migrace SSIS se spravovanou instancí Azure SQL Database jako cílem úlohy databáze
description: Migrace SSIS se spravovanou instancí Azure SQL Database jako cílem úlohy databáze.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 38010e3aaa2d0544dfbfe19135d25250d2b021a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929783"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Migrace SSIS se spravovanou instancí Azure SQL Database jako cílem úlohy databáze

Při migraci databázových úloh z SQL Serveru v místním prostředí do spravované instance Azure SQL Database byste měli znát [službu Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) a síťové [topologie pro migrace spravovaných instancí Azure SQL Database pomocí DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Tento článek se zaměřuje na migraci balíčků služby SQL Server Integration Service (SSIS) uložených v katalogu SSIS (SSISDB) a úlohách agenta serveru SQL Server, které plánují spuštění balíčků SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrace katalogu SSIS (SSISDB)

Migraci SSISDB lze provést pomocí Služby DMS, jak je popsáno v článku: [Migrace balíčků SSIS do spravované instance Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Úlohy SSIS pro agenta spravované instance Azure SQL Database

Spravovaná instance Azure SQL Database má nativní, prvotřídní plánovač stejně jako SQL Server Agent v místním.  Vzhledem k tomu, že nástroj pro migraci úloh SSIS ještě není k dispozici, je nutné je migrovat z agenta SQL Server agenta místně do agenta spravované instance Azure SQL Database prostřednictvím skriptů/ruční kopírování.

## <a name="additional-resources"></a>Další zdroje

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Prostředí Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Síťové topologie pro migrace spravovaných instancí Azure SQL Database pomocí DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrace balíčků SSIS do spravované instance Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Další kroky

- [Připojení k SSISDB v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Spouštět balíčky SSIS nasazené v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
