---
title: SSIS migrace pomocí Azure SQL Database spravované instance jako cíle úloh databáze
description: SSIS migraci pomocí Azure SQL Database spravované instance jako cíle úloh databáze.
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929783"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>SSIS migrace pomocí Azure SQL Database spravované instance jako cíle úloh databáze

Když migrujete databázové úlohy z SQL Server místně do Azure SQL Database spravované instance, měli byste být obeznámeni se [službou Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) a [síťovými topologiemi pro migraci Azure SQL Database spravované instance pomocí DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Tento článek se zaměřuje na migraci balíčků služby SQL Server Integration Service (SSIS) uložených v katalogu SSIS (SSISDB) a úlohách SQL Server agenta, které naplánují spouštění balíčků SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrace katalogu SSIS (SSISDB)

Migraci SSISDB můžete provést pomocí DMS, jak je popsáno v článku: [migrace balíčků SSIS do spravované instance Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS úlohy pro Azure SQL Database agenta spravované instance

Azure SQL Database spravovaná instance má nativního plánovače první třídy stejně jako SQL Serverho agenta místně.  Vzhledem k tomu, že nástroj pro migraci pro úlohy SSIS ještě není k dispozici, musí být migrován z SQL Serverho agenta místně do Azure SQL Database spravované instance pomocí skriptů a ruční kopie.

## <a name="additional-resources"></a>Další zdroje informací:

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Síťové topologie pro migrace spravované instance Azure SQL Database pomocí DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrace balíčků SSIS do spravované instance Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Další kroky

- [Připojení k SSISDB v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Spouštění balíčků SSIS nasazených v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
