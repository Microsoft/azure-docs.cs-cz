---
title: Výpis účtů databáze obnovitelné podle umístění pomocí Azure Cosmos DB REST API
description: Zobrazí seznam všech účtů databáze obnovitelné Azure Cosmos DB dostupných v rámci předplatného a v oblasti.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 2a3fbc1bb00c57c20436c19602c135f1917c6a60
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527442"
---
# <a name="list-restorable-database-accounts-by-location-using-azure-cosmos-db-rest-api"></a>Výpis účtů databáze obnovitelné podle umístění pomocí Azure Cosmos DB REST API

Zobrazí seznam všech účtů databáze obnovitelné Azure Cosmos DB dostupných v rámci předplatného a v oblasti. Toto volání vyžaduje `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` oprávnění.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts?api-version=2020-06-01-preview

```

## <a name="uri-parameters"></a>Parametry identifikátoru URI

| Name | V | Požaduje se | Typ | Description |
| --- | --- | --- | --- | --- |
| **oblasti** | program | True | řetězec| Oblast Azure Cosmos DB, s mezerami mezi slovy a každým slovem velkými písmeny. |
| **subscriptionId** | program | True | řetězec| ID cílového předplatného |
| **verze API-Version** | query | True | řetězec | Verze rozhraní API, která se má použít pro tuto operaci. |

## <a name="responses"></a>Odpovědi

| Název | Typ | Description |
| --- | --- | --- |
| 200 OK | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| Operace se úspěšně dokončila. |
| Další stavové kódy | [DefaultErrorResponse](#defaulterrorresponse)| Chybová odezva popisující, proč se operace nezdařila |

## <a name="examples"></a>Příklady

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**Ukázková žádost**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**Ukázková odpověď**

Stavový kód: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>Definice

|Definice  | Popis|
| --- | --- |
| [ApiType](#apitype) | Výčet označující typ rozhraní API pro účet databáze obnovitelné. |
| [DefaultErrorResponse](#defaulterrorresponse) | Odpověď na chybu od služby. |
| [ErrorResponse](#errorresponse) | Chybná odpověď. |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Účet databáze Azure Cosmos DB obnovitelné |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | Odpověď na operaci seznamu, která obsahuje účty databáze obnovitelné a jejich vlastnosti. |
| [RestorableLocationResource](#restorablelocationresource) | Vlastnosti místního účtu obnovitelné |

### <a name="apitype"></a><a id="apitype"></a>ApiType

Výčet označující typ rozhraní API pro účet databáze obnovitelné.

| **Název** | **Typ** |
| --- | --- |
| Cassandra |řetězec|
| Gremlin |řetězec|
| GremlinV2 |řetězec|
| MongoDB |řetězec|
| Sql |řetězec|
| Tabulka |řetězec|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Účet databáze Azure Cosmos DB obnovitelné

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| ID |řetězec| Jedinečný identifikátor prostředku Azure Resource Manager prostředku |
| location |řetězec| Umístění skupiny prostředků, do které prostředek patří. |
| name |řetězec| Název prostředku Správce prostředků. |
| vlastnosti. Account |řetězec| Název globálního databázového účtu. |
| Properties. apiType |[ApiType](#apitype)| Typ rozhraní API účtu databáze obnovitelné |
| Properties. creationTime |řetězec| Čas vytvoření účtu databáze obnovitelné (formát ISO-8601). |
| Properties. deletionTime |řetězec| Čas odstranění účtu databáze obnovitelné (formát ISO-8601). |
| Properties. restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| Seznam oblastí, ze kterých lze obnovit účet databáze. |
| typ |řetězec| Typ prostředku Azure. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

Odpověď na operaci seznamu, která obsahuje účty databáze obnovitelné a jejich vlastnosti.

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| hodnota |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| Seznam účtů databáze obnovitelné a jejich vlastností |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

Vlastnosti místního účtu obnovitelné

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| creationTime |řetězec| Čas vytvoření místního účtu databáze obnovitelné (formát ISO-8601). |
| deletionTime |řetězec| Čas, kdy byl odstraněn účet databáze místní obnovitelné (formát ISO-8601). |
| umístění |řetězec| Umístění místního účtu obnovitelné. |
| regionalDatabaseAccountInstanceId |řetězec| ID instance místního účtu obnovitelné. |

## <a name="next-steps"></a>Další kroky

* [Vypíše kolekce obnovitelné](restorable-database-accounts-list-by-location.md) v rozhraní Azure Cosmos DB API pro MongoDB pomocí REST API.
* [Model prostředků](continuous-backup-restore-resource-model.md) režimu průběžné zálohování.