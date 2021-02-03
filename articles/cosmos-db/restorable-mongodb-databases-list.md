---
title: Výpis databází obnovitelné v rozhraní Azure Cosmos DB API pro MongoDB pomocí REST API
description: Zobrazit informační kanál událostí všech mutací dokončených ve všech databázích Azure Cosmos DB MongoDB pod účtem obnovitelné To pomáhá ve scénáři, kdy byla databáze omylem odstraněna, aby získala čas odstranění.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 64288f67728e59c32c662a6640d60daf52c53fe1
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527454"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Výpis databází obnovitelné v rozhraní Azure Cosmos DB API pro MongoDB pomocí REST API

Zobrazit informační kanál událostí všech mutací dokončených ve všech databázích Azure Cosmos DB MongoDB pod účtem obnovitelné To pomáhá ve scénáři, kdy byla databáze omylem odstraněna, aby získala čas odstranění. Toto rozhraní API vyžaduje `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` oprávnění.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
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
| 200 OK | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| Operace se úspěšně dokončila. |
| Další stavové kódy | [DefaultErrorResponse](#defaulterrorresponse)| Chybová odezva popisující, proč se operace nezdařila|

## <a name="examples"></a>Příklady

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**Ukázková žádost**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**Ukázková odpověď**

Stavový kód: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definice

| Definice | Popis | | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | Odpověď na chybu od služby. | | [ErrorResponse](#errorresponse) | Chybná odpověď. | | [Typem operace OperationType](#operationtype) | Výčet, který označuje typ operace události. | | [Prostředek](#resource) | Prostředek Azure Cosmos DB API pro událost databáze MongoDB | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | Rozhraní Azure Cosmos DB API pro událost databáze MongoDB | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | Vlastnosti Azure Cosmos DB rozhraní API pro událost databáze MongoDB | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | Odpověď na operaci seznamu, která obsahuje rozhraní Azure Cosmos DB API pro události databáze MongoDB a jejich vlastnosti. |

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

### <a name="resource"></a><a id="resource"></a>Prostředek

Prostředek Azure Cosmos DB API pro událost databáze MongoDB

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| _rid |řetězec| Systémová vlastnost generovaná systémem. Jedinečný identifikátor. |
| eventTimestamp |řetězec| Čas, kdy se tato databázová událost stala. |
| operationType |[Typ operace](#operationtype)| Typ operace této události databáze.  |
| ownerId |řetězec| Název Azure Cosmos DB rozhraní API pro databázi MongoDB.|
| ownerResourceId |řetězec| ID prostředku Azure Cosmos DB rozhraní API pro databázi MongoDB. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

Azure Cosmos DB rozhraní API pro událost databáze MongoDB

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| ID |řetězec| Jedinečný identifikátor prostředku Azure Resource Manager prostředku |
| name |řetězec| Název prostředku Správce prostředků. |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| Vlastnosti Azure Cosmos DB rozhraní API pro událost databáze MongoDB |
| typ |řetězec| Typ prostředku Azure. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

Vlastnosti Azure Cosmos DB rozhraní API pro událost databáze MongoDB

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| prostředek |[Prostředek](#resource)| Prostředek Azure Cosmos DB API pro událost databáze MongoDB |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

Odpověď na operaci seznamu, která obsahuje události databáze a jejich vlastnosti.

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| hodnota |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| Seznam událostí databáze a jejich vlastností. |

## <a name="next-steps"></a>Další kroky

* [Vypíše prostředky obnovitelné](restorable-mongodb-resources-list.md)  v rozhraní Azure Cosmos DB API pro MongoDB pomocí REST API.
* [Vypíše kolekce obnovitelné](restorable-mongodb-collections-list.md)  v rozhraní Azure Cosmos DB API pro MongoDB pomocí REST API.
* [Model prostředků](continuous-backup-restore-resource-model.md) režimu průběžné zálohování.