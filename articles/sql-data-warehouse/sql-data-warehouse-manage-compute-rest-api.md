---
title: Pozastavit, obnovit, vertikálně s využitím REST ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Spravujte výpočetní výkon v SQL Data Warehouse pomocí rozhraní REST API.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 658b35163e20d024118bc7a3142c86614540f00c
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804696"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Rozhraní REST API pro Azure SQL Data Warehouse
Rozhraní REST API pro správu výpočetních prostředků ve službě Azure SQL Data Warehouse.

## <a name="scale-compute"></a>Škálování výpočetního výkonu
Chcete-li změnit jednotky datového skladu, použijte [vytvořit nebo aktualizovat databázi](/rest/api/sql/databases/createorupdate) rozhraní REST API. Následující příklad nastaví pro databázi MySQLDW, který je hostitelem serveru MyServer jednotky datového skladu na úroveň DW1000 a. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Pozastavit výpočetní prostředky

Chcete-li pozastavit databázi, použijte [pozastavení databáze](/rest/api/sql/databases/pause) rozhraní REST API. Následující příklad pozastaví databázi s názvem Database02 hostovaný na serveru s názvem Server01. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Obnovit výpočetní prostředky

Pokud chcete spustit databázi, použijte [obnovuje se chod databáze](/rest/api/sql/databases/resume) rozhraní REST API. Následující příklad spustí databázi s názvem Database02 hostovaný na serveru s názvem Server01. Server je ve skupině prostředků Azure s názvem ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Zkontrolujte stav databáze

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Získat plán údržby
Zkontrolujte plán údržby, která byla nastavena pro datový sklad. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Nastaveného plánu údržby
K nastavení a aktualizaci plánu maintnenance na existující datový sklad.

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


## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [Správa výpočetních služeb](sql-data-warehouse-manage-compute-overview.md).

