---
title: Pozastavení, pokračování, škálování pomocí rest API
description: Spravujte výpočetní výkon v datovém skladu Azure Synapse Analytics prostřednictvím api REST.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 322f1dfcb709727ddd3a97ea22dbe8243aedca20
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350354"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>REST API pro Datový sklad Azure SQL
REST API pro správu výpočetních prostředků v datovém skladu Azure Synapse Analytics.

## <a name="scale-compute"></a>Škálování výpočetního výkonu
Chcete-li změnit jednotky datového skladu, použijte rozhraní API REST [vytvořit nebo aktualizovat databázi.](/rest/api/sql/databases/createorupdate) Následující příklad nastaví jednotky datového skladu na DW1000 pro databázi MySQLDW, která je hostována na serveru MyServer. Server se nachází ve skupině prostředků Azure s názvem ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Pozastavení výpočtu

Chcete-li databázi pozastavit, použijte rozhraní REST ROZHRANÍ [PAUSE Database.](/rest/api/sql/databases/pause) Následující příklad pozastaví databázi s názvem Database02 hostovoci na serveru s názvem Server01. Server se nachází ve skupině prostředků Azure s názvem ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Pokračovat v výpočtu

Chcete-li spustit databázi, použijte rozhraní REST ROZHRANÍ [RESUME Database.](/rest/api/sql/databases/resume) Následující příklad spustí databázi s názvem Database02 hostovanou na serveru s názvem Server01. Server se nachází ve skupině prostředků Azure s názvem ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Zkontrolovat stav databáze

> [!NOTE]
> V současné době Zkontrolujte stav databáze může vrátit ONLINE, zatímco databáze je dokončení pracovního postupu online, výsledkem jsou chyby připojení. Pokud používáte toto volání rozhraní API ke spuštění pokusů o připojení, bude pravděpodobně nutné přidat 2 až 3 minuty zpoždění v kódu aplikace.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Získejte plán údržby
Zkontrolujte plán údržby, který byl nastaven pro datový sklad. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Nastavení plánu údržby
Nastavení a aktualizace plánu údržby v existujícím datovém skladu.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```


## <a name="next-steps"></a>Další kroky
Další informace naleznete v [tématu Správa výpočetních prostředků](sql-data-warehouse-manage-compute-overview.md).

