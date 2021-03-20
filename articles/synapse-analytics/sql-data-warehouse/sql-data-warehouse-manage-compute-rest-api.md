---
title: Pozastavení, obnovení a škálování pomocí rozhraní REST API pro vyhrazený fond SQL (dřív SQL DW)
description: Spravujte výpočetní výkon pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics prostřednictvím rozhraní REST API.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c04f61aaef5f5072ce0fb39ff111ba07ee151700
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375897"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Rozhraní REST API pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics

Rozhraní REST API pro správu výpočetních prostředků pro vyhrazený fond SQL (dříve SQL DW) ve službě Azure synapse Analytics.

## <a name="scale-compute"></a>Škálování výpočetního výkonu

Chcete-li změnit jednotky datového skladu, použijte REST API [vytvořit nebo aktualizovat databázi](/rest/api/sql/databases/createorupdate) . Následující příklad nastaví jednotky datového skladu tak, aby DW1000 pro databázi MySQLDW, která je hostována na serveru MyServer. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    location: "West Central US",
    "sku": {
    "name": "DW200c"
    }
}
```

## <a name="pause-compute"></a>Pozastavit výpočetní prostředky

Chcete-li pozastavit databázi, použijte REST API [Pozastavení databáze](/rest/api/sql/databases/pause) . V následujícím příkladu je pozastavena databáze s názvem Database02, která je hostována na serveru s názvem server01. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Pokračovat v COMPUTE

Chcete-li spustit databázi, použijte REST API [obnovit databázi](/rest/api/sql/databases/resume) . V následujícím příkladu se spustí databáze s názvem Database02 hostovaná na serveru s názvem server01. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Kontrolovat stav databáze

> [!NOTE]
> Stav databáze se v současné době kontroluje ONLINE, zatímco databáze dokončuje online pracovní postup, což vede k chybám připojení. Pokud toto volání rozhraní API používáte k aktivaci pokusů o připojení, může být nutné přidat zpoždění 2 až 3 minuty do kódu aplikace.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}?api-version=2020-08-01-preview
```

## <a name="get-maintenance-schedule"></a>Získat plán údržby

Ověřte plán údržby, který byl nastaven pro vyhrazený fond SQL (dříve SQL DW).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Nastavit plán údržby

Nastavení a aktualizace plánu údržby stávajícího vyhrazeného fondu SQL (dříve SQL DW).

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": "Saturday",
                                "startTime": "00:00",
                                "duration": "08:00",
                },
                {
                                "dayOfWeek": "Wednesday",
                                "startTime": "00:00",
                                "duration": "08:00",
                }
                ]
    }
}

```

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Správa výpočtů](sql-data-warehouse-manage-compute-overview.md).
