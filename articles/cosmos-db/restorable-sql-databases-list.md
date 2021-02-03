---
title: Vypsat obnovitelné databáze rozhraní SQL API v Azure Cosmos DB pomocí REST API
description: Zobrazit informační kanál událostí všech mutací, které se provedly ve všech Azure Cosmos DB databázích SQL pod účtem obnovitelné To pomáhá ve scénáři, kdy byla databáze omylem odstraněna, aby získala čas odstranění.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: d3d72cff5fcfeed17d60e2f856be4adc1a983819
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527368"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>Vypsat obnovitelné databáze rozhraní SQL API v Azure Cosmos DB pomocí REST API

Zobrazit informační kanál událostí všech mutací, které se provedly ve všech Azure Cosmos DB databázích SQL pod účtem obnovitelné To pomáhá ve scénáři, kdy byla databáze omylem odstraněna, aby získala čas odstranění. Toto rozhraní API vyžaduje `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` oprávnění.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>Parametry identifikátoru URI

| Name | V | Požaduje se | Typ | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | program | True |řetězec| Identifikátor instanceId GUID účtu databáze obnovitelné |
| **oblasti** | program | True | řetězec| Oblast Azure Cosmos DB, s mezerami mezi slovy a každým slovem velkými písmeny. |
| **subscriptionId** | program | True | řetězec| ID cílového předplatného |
| **verze API-Version** | query | True | řetězec | Verze rozhraní API, která se má použít pro tuto operaci. |

## <a name="responses"></a>Odpovědi

| Název | Typ | Description |
| --- | --- | --- |
| 200 OK | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| Operace se úspěšně dokončila. |
| Další stavové kódy | [DefaultErrorResponse](#defaulterrorresponse)| Chybová odezva popisující, proč se operace nezdařila |

## <a name="examples"></a>Příklady

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**Ukázkový požadavek**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**Ukázková odpověď**

Stavový kód: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definice

| Definice | Popis | | --- || --- | | [Databáze](#database) | Azure Cosmos DB objekt zdroje databáze SQL | | [DefaultErrorResponse](#defaulterrorresponse) | Odpověď na chybu od služby. | | [ErrorResponse](#errorresponse) | Chybná odpověď. | | [Typem operace OperationType](#operationtype) | Výčet, který označuje typ operace události. | | [Prostředek](#resource) | Prostředek Azure Cosmos DB události služby SQL Database | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | Událost Azure Cosmos DB SQL Database | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | Vlastnosti Azure Cosmos DB události služby SQL Database | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | Odpověď na operaci seznamu, která obsahuje události SQL Database a jejich vlastnosti. |

### <a name="database"></a><a id="database"></a>Databáze

Azure Cosmos DB objekt prostředku databáze SQL

| **Název**  |  **Typ**  |  **Popis** | | --- || --- | ---| | _colls | řetězec | Systémová vlastnost, která určuje adresovatelnou cestu prostředku kolekce. | | _etag | řetězec | Systémem generovaná vlastnost představující značku ETag prostředku požadovaná pro kontrolu optimistického řízení souběžnosti. | | _rid | řetězec | Systémová vlastnost generovaná systémem. Jedinečný identifikátor. | | _self | řetězec | Systémem generovaná vlastnost, která určuje adresovatelnou cestu k databázovému prostředku. | | _ts | | Systémem generovaná vlastnost, která označuje poslední aktualizované časové razítko prostředku. | | _users | řetězec | Systémem generovaná vlastnost, která určuje adresovatelnou cestu prostředku uživatele. | | ID | řetězec | Název Azure Cosmos DB databáze SQL |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

Odpověď na chybu od služby.

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| error | [ErrorResponse](#errorresponse)| Chybná odpověď. |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

Chybná odpověď.

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| kód |řetězec| Kód chyby |
| zpráva |řetězec| Chybová zpráva indikující, proč se operace nezdařila. |

### <a name="operationtype"></a><a id="operationtype"></a>Typ operace

Výčet, který označuje typ operace události.

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| Vytvořit |řetězec|událost vytvoření databáze|
| Odstranit |řetězec|událost odstranění databáze|
| Nahrazení |řetězec|událost změny databáze|
| SystemOperation |řetězec|událost změny databáze spuštěná systémem Uživatel neinicioval tuto událost.|

### <a name="resource"></a><a id="resource"></a>Prostředek

Prostředek události Azure Cosmos DB SQL Database

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| _rid |řetězec| Systémová vlastnost generovaná systémem. Jedinečný identifikátor. |
| database |[Databáze](#database)| Azure Cosmos DB objekt prostředku databáze SQL |
| eventTimestamp |řetězec| Čas, kdy se tato databázová událost stala. |
| operationType |[Typ operace](#operationtype)| Typ operace této události databáze. |
| ownerId |řetězec| Název databáze SQL. |
| ownerResourceId |řetězec| ID prostředku databáze SQL. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Událost Azure Cosmos DB SQL Database

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| ID |řetězec| Jedinečný identifikátor prostředku Azure Resource Manager prostředku |
| name |řetězec| Název prostředku Azure Resource Manager. |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| Vlastnosti události databáze SQL |
| typ |řetězec| Typ prostředku Azure. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

Vlastnosti Azure Cosmos DB události služby SQL Database

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| prostředek |[Prostředek](#resource)| Prostředek události Azure Cosmos DB SQL Database |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

Odpověď na operaci seznamu, která obsahuje události SQL Database a jejich vlastnosti.

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| hodnota |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| Seznam událostí SQL Database a jejich vlastností |

## <a name="next-steps"></a>Další kroky

* [Vypíše kontejnery obnovitelné](restorable-sql-containers-list.md) v Azure Cosmos DB SQL API pomocí REST API.
* [Model prostředků](continuous-backup-restore-resource-model.md) režimu průběžné zálohování.