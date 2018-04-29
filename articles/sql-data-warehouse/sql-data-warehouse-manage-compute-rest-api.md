---
title: Pozastavit, obnovit a škálování se ULOŽENÁ v Azure SQL Data Warehouse | Microsoft Docs
description: Spravujte výpočetní výkon v SQL Data Warehouse pomocí rozhraní REST API.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 21423d69bf2cf06bcd208082ce492bf5dd038e29
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Rozhraní REST API pro Azure SQL Data Warehouse
Rozhraní REST API pro správu výpočtů v Azure SQL Data Warehouse.

## <a name="scale-compute"></a>Škálování výpočetního výkonu
Chcete-li změnit jednotky datového skladu, použijte [vytvoření nebo aktualizace databáze](/rest/api/sql/databases/createorupdate) REST API. Následující příklad nastaví DW1000 pro databázi MySQLDW, který je hostován na serveru MyServer jednotky datového skladu. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Pozastavit výpočetní

Chcete-li pozastavit databázi, použijte [pozastavení databáze](/rest/api/sql/databases/pause) REST API. Následující příklad pozastaví databáze s názvem Database02 hostovaná na serveru s názvem Server01. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Obnovit výpočetní

Chcete-li spustit databázi, použijte [obnovit databáze](/rest/api/sql/databases/resume) REST API. Následující příklad spustí databáze s názvem Database02 hostovaná na serveru s názvem Server01. Server je ve skupině prostředků Azure s názvem ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Zkontrolujte stav databáze

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```


## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [spravovat výpočetní](sql-data-warehouse-manage-compute-overview.md).

