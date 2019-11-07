---
title: Pozastavení, obnovení, škálování pomocí rozhraní REST API
description: Spravujte výpočetní výkon v Azure SQL Data Warehouse prostřednictvím rozhraní REST API.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f72b3fd1024a68a6f48d2e9e676fc7ca23bf2a4f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686055"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Rozhraní REST API pro Azure SQL Data Warehouse
Rozhraní REST API pro správu výpočetních prostředků v Azure SQL Data Warehouse.

## <a name="scale-compute"></a>Škálování výpočetního výkonu
Chcete-li změnit jednotky datového skladu, použijte REST API [vytvořit nebo aktualizovat databázi](/rest/api/sql/databases/createorupdate) . Následující příklad nastaví jednotky datového skladu tak, aby DW1000 pro databázi MySQLDW, která je hostována na serveru MyServer. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

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

Chcete-li pozastavit databázi, použijte REST API [Pozastavení databáze](/rest/api/sql/databases/pause) . V následujícím příkladu je pozastavena databáze s názvem Database02, která je hostována na serveru s názvem server01. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Pokračovat v COMPUTE

Chcete-li spustit databázi, použijte REST API [obnovit databázi](/rest/api/sql/databases/resume) . V následujícím příkladu se spustí databáze s názvem Database02 hostovaná na serveru s názvem server01. Server je ve skupině prostředků Azure s názvem ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Kontrolovat stav databáze

> [!NOTE]
> Stav databáze se v současné době kontroluje ONLINE, zatímco databáze dokončuje online pracovní postup, což vede k chybám připojení. Pokud toto volání rozhraní API používáte k aktivaci pokusů o připojení, může být nutné přidat zpoždění 2 až 3 minuty do kódu aplikace.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Získat plán údržby
Ověřte plán údržby, který byl nastaven pro datový sklad. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Nastavit plán údržby
Nastavení a aktualizace plánu maintnenance pro existující datový sklad.

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
Další informace najdete v tématu [Správa výpočtů](sql-data-warehouse-manage-compute-overview.md).

