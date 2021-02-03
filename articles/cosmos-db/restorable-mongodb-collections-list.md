---
title: Vypíše kolekce obnovitelné ve Azure Cosmos DB MongoDB API – REST API
description: Zobrazit informační kanál událostí všech mutací provedených ve všech Azure Cosmos DBch MongoDB kolekcích v konkrétní databázi. To pomáhá v situaci, kdy byl kontejner omylem odstraněn.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 83e1c7c27e8c5d179c4ec6aa4ba64b3367294a48
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527458"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Výpis kolekcí obnovitelné v rozhraní Azure Cosmos DB API pro MongoDB pomocí REST API

Zobrazit informační kanál událostí všech mutací provedených ve všech Azure Cosmos DB rozhraní API pro kolekce MongoDB v konkrétní databázi. To pomáhá v situaci, kdy byl kontejner omylem odstraněn. Toto rozhraní API vyžaduje `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` oprávnění.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

S nepovinnými parametry:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>Parametry identifikátoru URI

| Name | V | Požaduje se | Typ | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | program | True |řetězec| Identifikátor instanceId GUID účtu databáze obnovitelné |
| **oblasti** | program | True | řetězec| Oblast Azure Cosmos DB, s mezerami mezi slovy a každým slovem velkými písmeny. |
| **subscriptionId** | program | True | řetězec| ID cílového předplatného |
| **verze API-Version** | query | True | řetězec | Verze rozhraní API, která se má použít pro tuto operaci. |
| **restorableMongodbDatabaseRid** | query | |řetězec| ID prostředku rozhraní Azure Cosmos DB API pro databázi MongoDB |

## <a name="responses"></a>Odpovědi

| Název | Typ | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| Operace se úspěšně dokončila. |
| Další stavové kódy | [DefaultErrorResponse](#defaulterrorresponse)| Chybová odezva popisující, proč se operace nezdařila|

## <a name="examples"></a>Příklady

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**Ukázková žádost**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**Ukázková odpověď**

Stavový kód: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definice

|Definice  | Popis|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | Odpověď na chybu od služby. |
| [ErrorResponse](#errorresponse) | Chybná odpověď. |
| [Typ operace](#operationtype) | Výčet, který označuje typ operace události. |
| [Prostředek](#resource) | Prostředek Azure Cosmos DB API pro událost kolekce MongoDB |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | Azure Cosmos DB rozhraní API pro událost kolekce MongoDB |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | Vlastnosti Azure Cosmos DB rozhraní API pro událost kolekce MongoDB |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | Odpověď na operaci seznamu, která obsahuje události kolekce a jejich vlastnosti. |

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
| Vytvořit |řetězec|událost vytvoření kolekce|
| Odstranit |řetězec|událost odstranění kolekce|
| Nahrazení |řetězec|událost změny kolekce|

### <a name="resource"></a><a id="resource"></a>Prostředek

Prostředek události kolekce Azure Cosmos DB MongoDB

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| _rid |řetězec| Systémová vlastnost generovaná systémem. Jedinečný identifikátor. |
| eventTimestamp |řetězec| Čas, kdy došlo k této události kolekce. |
| operationType |[Typ operace](#operationtype)|  Typ operace této události kolekce. |
| ownerId |řetězec| Název kolekce MongoDB|
| ownerResourceId |řetězec| ID prostředku kolekce MongoDB |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Událost Azure Cosmos DB MongoDB Collection

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| ID |řetězec| Jedinečný identifikátor prostředku Azure Resource Manager prostředku |
| name |řetězec| Název prostředku Správce prostředků. |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| Vlastnosti události kolekce |
| typ |řetězec| Typ prostředku Azure. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

Vlastnosti události kolekce Azure Cosmos DB MongoDB

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| prostředek | [Prostředek](#resource)| Prostředek Azure Cosmos DB API pro událost kolekce MongoDB |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

Odpověď na operaci seznamu, která obsahuje události kolekce a jejich vlastnosti.

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| hodnota |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| Seznam Azure Cosmos DB rozhraní API pro události kolekce MongoDB a jejich vlastnosti. |

## <a name="next-steps"></a>Další kroky

* [Vypíše databáze obnovitelné](restorable-mongodb-databases-list.md)  v rozhraní Azure Cosmos DB API pro MongoDB pomocí REST API.
* [Model prostředků](continuous-backup-restore-resource-model.md) režimu průběžné zálohování.