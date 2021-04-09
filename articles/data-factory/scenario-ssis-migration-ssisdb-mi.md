---
title: SSIS migrace pomocí spravované instance Azure SQL jako cíle databázových úloh
description: SSIS migraci pomocí spravované instance Azure SQL jako cíle úloh databáze.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 3d2bc60f8ba7120f8d962500c06be50e905c11a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373585"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS migrace pomocí spravované instance Azure SQL jako cíle databázových úloh

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Při migraci databázových úloh z SQL Server instance do spravované instance Azure SQL byste měli být obeznámeni se [službou Azure Data Migration Service](../dms/dms-overview.md)(DMS) a [topologiemi sítě pro migrace spravované instance SQL pomocí DMS](../dms/resource-network-topologies.md).

Tento článek se zaměřuje na migraci balíčků služby SQL Server Integration Service (SSIS) uložených v katalogu SSIS (SSISDB) a úlohách SQL Server agenta, které naplánují spouštění balíčků SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrace katalogu SSIS (SSISDB)

Migraci SSISDB můžete provést pomocí DMS, jak je popsáno v článku: [migrace balíčků SSIS do spravované instance SQL](../dms/how-to-migrate-ssis-packages-managed-instance.md).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS úlohy do agenta spravované instance SQL

Spravovaná instance SQL má nativní, první Plánovač, který je první třídou, stejně jako SQL Server agenta místně.  Můžete [spouštět balíčky SSIS prostřednictvím agenta spravované instance Azure SQL](how-to-invoke-ssis-package-managed-instance-agent.md).

Vzhledem k tomu, že nástroj pro migraci pro úlohy SSIS ještě není dostupný, musí být migrován z SQL Server agenta místně do agenta spravované instance SQL prostřednictvím skriptů a ruční kopie.

## <a name="additional-resources"></a>Další zdroje informací

- [Azure Data Factory](./introduction.md)
- [Prostředí Azure-SSIS Integration Runtime](./create-azure-ssis-integration-runtime.md)
- [Azure Database Migration Service](../dms/dms-overview.md)
- [Síťové topologie pro migrace spravované instance SQL pomocí DMS](../dms/resource-network-topologies.md)
- [Migrace balíčků SSIS do spravované instance SQL](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Další kroky

- [Připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Spouštění balíčků SSIS nasazených v Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)