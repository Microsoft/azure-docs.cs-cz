---
title: 'REST API: synchronizace mezi několika databázemi'
description: Použijte vzorový skript REST API k synchronizaci mezi několika databázemi.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: d3ff8114c11b224a0bdbb0bd2d0e5686a7e57b55
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565879"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>Synchronizace dat mezi několika databázemi pomocí REST API 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Tento REST API příklad konfiguruje Synchronizace dat SQL k synchronizaci dat mezi více databázemi.

Přehled Synchronizace dat SQL najdete v tématu [synchronizace dat napříč několika cloudy a místními databázemi pomocí synchronizace dat SQL v Azure](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> Synchronizace dat SQL v tuto chvíli nepodporuje spravovanou instanci SQL Azure.

## <a name="create-sync-group"></a>Vytvořit skupinu synchronizace

Pomocí šablony [vytvořit nebo aktualizovat](/rest/api/sql/syncgroups/createorupdate) vytvořte skupinu synchronizace.
 
Při vytváření skupiny synchronizace nemusíte předávat schéma synchronizace (table\column) a Nepředávat masterSyncMemberName, protože v této době skupina synchronizace ještě nemá informace table\column.

Ukázková žádost o vytvoření skupiny synchronizace: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Ukázková odpověď pro vytvoření skupiny synchronizace:

Stavový kód: 200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

Stavový kód: 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>Vytvořit člena synchronizace

K vytvoření člena synchronizace použijte šablonu [vytvořit nebo aktualizovat](/rest/api/sql/syncmembers/createorupdate) .

Ukázková žádost o vytvoření člena synchronizace:

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
Ukázková odpověď pro vytvoření člena synchronizace:

Stavový kód: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Stavový kód: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>Aktualizovat schéma

Po úspěšném vytvoření skupiny synchronizace aktualizujte schéma pomocí následujících šablon.

K aktualizaci schématu pro databázi centra použijte šablonu [schématu centra aktualizace](/rest/api/sql/syncgroups/refreshhubschema)  . 

Ukázková žádost o aktualizaci schématu databáze centra: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

Ukázková odpověď pro aktualizaci schématu databáze centra: 

Stavový kód: 200

Stavový kód: 202

K vypsání schématu databáze centra použijte šablonu [schémat centra seznamu](/rest/api/sql/syncgroups/listhubschemas) . 

K aktualizaci schématu členské databáze použijte šablonu [schématu členu aktualizace](/rest/api/sql/syncmembers/refreshmemberschema) . 

K vypsání schématu databáze člena použijte [seznam šablon schématu členů](/rest/api/sql/syncmembers/listmemberschemas) . 

Až se schéma úspěšně aktualizuje, přejděte jenom k dalšímu kroku. 

## <a name="update-sync-group"></a>Aktualizovat skupinu synchronizace 

K aktualizaci skupiny synchronizace použijte šablonu [vytvořit nebo aktualizovat](/rest/api/sql/syncgroups/createorupdate) .

Aktualizujte skupinu synchronizace zadáním schématu synchronizace. Zahrňte schéma a masterSyncMemberName, což je název, který obsahuje schéma, které chcete použít. 

Ukázková žádost o aktualizaci skupiny synchronizace: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Ukázková odpověď pro aktualizaci skupiny synchronizace: 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>Aktualizovat člena synchronizace

K aktualizaci člena synchronizace použijte šablonu [vytvořit nebo aktualizovat](/rest/api/sql/syncmembers/createorupdate) .

Ukázková žádost o aktualizaci člena synchronizace: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

Ukázková odpověď pro aktualizaci člena synchronizace: 

Stavový kód: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Stavový kód: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>Spustit synchronizaci

Pomocí šablony [synchronizace triggeru](/rest/api/sql/syncgroups/triggersync) spusťte operaci synchronizace.

Ukázková žádost o aktivaci operace synchronizace: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

Ukázková odpověď pro aktivaci operace synchronizace: 

Stavový kód: 200

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../powershell-script-content-guide.md).

Další informace o Synchronizace dat SQL najdete v tématech:

- Přehled – [synchronizace dat napříč několika cloudy a místními databázemi pomocí synchronizace dat SQL v Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Nastavení synchronizace dat
    - Použití Azure Portal- [kurzu: nastavení synchronizace dat SQL synchronizace dat mezi Azure SQL Database a SQL Server](../sql-data-sync-sql-server-configure.md)
    - Použití PowerShellu – [použití PowerShellu k synchronizaci dat mezi databází v Azure SQL Database a SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Agent synchronizace dat – [Agent synchronizace dat pro synchronizace dat SQL v Azure](../sql-data-sync-agent-overview.md)
- Osvědčené postupy – [osvědčené postupy pro synchronizace dat SQL v Azure](../sql-data-sync-best-practices.md)
- Monitorování – [monitorování synchronizace dat SQL pomocí protokolů Azure monitor](../monitor-tune-overview.md)
- Řešení potíží – [řešení potíží s synchronizace dat SQL v Azure](../sql-data-sync-troubleshoot.md)
- Aktualizace schématu synchronizace
    - Použití jazyka Transact-SQL – [Automatizace replikace změn schématu v synchronizace dat SQL v Azure](../sql-data-sync-update-sync-schema.md)
    - Použití PowerShellu – [k aktualizaci schématu synchronizace v existující skupině synchronizace použijte PowerShell](update-sync-schema-in-sync-group.md)

Další informace o SQL Database najdete v tématech:

- [Přehled SQL Database](../sql-database-paas-overview.md)
- [Správa životního cyklu databáze](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))