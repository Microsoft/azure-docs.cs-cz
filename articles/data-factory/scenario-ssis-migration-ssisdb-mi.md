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
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 6ea9134085812b99d59ad64aa2c9ec1b2ff827d9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684398"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>SSIS migrace pomocí Azure SQL Database spravované instance jako cíle úloh databáze

Při migraci databázových úloh z SQL Server v místním prostředí do Azure SQL Database spravované instance byste měli být obeznámeni se [službou Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) a [topologiemi sítě pro migrace Azure SQL Database spravované instance. pomocí DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Tento článek se zaměřuje na migraci balíčků služby SQL Server Integration Service (SSIS) uložených v katalogu SSIS (SSISDB) a úlohách SQL Server agenta, které naplánují spouštění balíčků SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrace katalogu SSIS (SSISDB)

Migraci SSISDB můžete provést pomocí DMS, jak je popsáno v článku: [migrace balíčků SSIS do spravované instance Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS úlohy pro Azure SQL Database agenta spravované instance

Azure SQL Database spravovaná instance má nativního plánovače první třídy stejně jako SQL Serverho agenta místně.  Vzhledem k tomu, že nástroj pro migraci pro úlohy SSIS ještě není k dispozici, musí být migrován z SQL Serverho agenta místně do Azure SQL Database spravované instance pomocí skriptů a ruční kopie.

## <a name="additional-resources"></a>Další zdroje

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Síťové topologie pro migrace spravované instance Azure SQL Database pomocí DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrace balíčků SSIS do spravované instance Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Další kroky

- [Připojení k SSISDB v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Spouštění balíčků SSIS nasazených v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
