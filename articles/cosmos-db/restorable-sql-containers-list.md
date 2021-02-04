---
title: Vypsat kontejnery rozhraní API SQL obnovitelné v Azure Cosmos DB pomocí REST API
description: Zobrazit informační kanál událostí všech mutací provedených na všech Azure Cosmos DB kontejnerech SQL v konkrétní databázi. To pomáhá v situaci, kdy byl kontejner omylem odstraněn.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: bba00b10d1a24dc29c1e986de1f0144c00350f5d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537420"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>Vypsat kontejnery rozhraní API SQL obnovitelné v Azure Cosmos DB pomocí REST API

> [!IMPORTANT]
> Funkce obnovení bodu v čase (režim průběžného zálohování) pro Azure Cosmos DB je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zobrazit informační kanál událostí všech mutací provedených na všech Azure Cosmos DB kontejnerech SQL v konkrétní databázi. To pomáhá v situaci, kdy byl kontejner omylem odstraněn. Toto rozhraní API vyžaduje `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` oprávnění.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

S nepovinnými parametry:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>Parametry identifikátoru URI

| Name | V | Požaduje se | Typ | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | program | True |řetězec| Identifikátor instanceId GUID účtu databáze obnovitelné |
| **oblasti** | program | True | řetězec| Oblast Azure Cosmos DB, s mezerami mezi slovy a každým slovem velkými písmeny. |
| **subscriptionId** | program | True | řetězec| ID cílového předplatného |
| **verze API-Version** | query | True | řetězec | Verze rozhraní API, která se má použít pro tuto operaci. |
| **restorableSqlDatabaseRid** | query | |řetězec| ID prostředku databáze SQL. |

## <a name="responses"></a>Odpovědi

| Název | Typ | Description |
| --- | --- | --- |
| 200 OK | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| Operace se úspěšně dokončila. |
| Další stavové kódy | [DefaultErrorResponse](#defaulterrorresponse)| Chybová odezva popisující, proč se operace nezdařila |

## <a name="examples"></a>Příklady

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**Ukázková žádost**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**Ukázková odpověď**

Stavový kód: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definice

| Definice | Popis | | --- || --- | | [Kontejner](#container) | Azure Cosmos DB objekt prostředku kontejneru SQL | | [DefaultErrorResponse](#defaulterrorresponse) | Odpověď na chybu od služby. | | [ErrorResponse](#errorresponse) | Chybná odpověď. | | [Typem operace OperationType](#operationtype) | Výčet, který označuje typ operace události. | | [Prostředek](#resource) | Prostředek Azure Cosmos DB události kontejneru SQL | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | Událost Azure Cosmos DBho kontejneru SQL | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | Vlastnosti Azure Cosmos DB události kontejneru SQL | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | Odpověď na operaci seznamu, která obsahuje události kontejneru SQL a jejich vlastnosti. |

### <a name="container"></a><a id="container"></a>Kontejner

Azure Cosmos DB objekt prostředku kontejneru SQL

| **Název**  |  **Typ**  |  **Popis** | | --- || --- | ---| | _etag | řetězec | Systémem generovaná vlastnost představující značku ETag prostředku požadovaná pro kontrolu optimistického řízení souběžnosti. | | _rid | řetězec | Systémová vlastnost generovaná systémem. Jedinečný identifikátor. | | _self | řetězec | Systémem generovaná vlastnost, která určuje adresovatelnou cestu prostředku kontejneru. | | _ts | | Systémem generovaná vlastnost, která označuje poslední aktualizované časové razítko prostředku. | | ID | řetězec | Název Azure Cosmos DB kontejneru SQL |

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
| Vytvořit |řetězec|událost vytvoření kontejneru|
| Odstranit |řetězec|událost odstranění kontejneru|
| Nahrazení |řetězec|událost úpravy kontejneru|
| SystemOperation |řetězec|událost změny kontejneru aktivované systémem Uživatel neinicioval tuto událost.|

### <a name="resource"></a><a id="resource"></a>Prostředek

Prostředek události Azure Cosmos DB kontejnerů SQL

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| _rid |řetězec| Systémová vlastnost generovaná systémem. Jedinečný identifikátor. |
| kontejner |[Kontejner](#container)| Azure Cosmos DB objekt prostředku kontejneru SQL |
| eventTimestamp |řetězec| Čas, kdy došlo k této události kontejneru. |
| operationType |[Typ operace](#operationtype)| Typ operace této události kontejneru. |
| ownerId |řetězec| Název kontejneru SQL. |
| ownerResourceId |řetězec| ID prostředku kontejneru SQL. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Událost Azure Cosmos DBho kontejneru SQL

| **Název** | **Typ** | **Popis** |
| --- | --- | ---
| ID |řetězec| Jedinečný identifikátor prostředku Azure Resource Manager prostředku |
| name |řetězec| Název prostředku Azure Resource Manager. |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| Vlastnosti události kontejneru SQL |
| typ |řetězec| Typ prostředku Azure. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

Vlastnosti Azure Cosmos DB události kontejneru SQL

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| prostředek |[Prostředek](#resource)| Prostředek události Azure Cosmos DB kontejnerů SQL |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

Odpověď na operaci seznamu, která obsahuje události kontejneru SQL a jejich vlastnosti.

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| hodnota |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| Seznam událostí kontejnerů SQL a jejich vlastností |

## <a name="next-steps"></a>Další kroky

* [Vypíše databáze obnovitelné](restorable-mongodb-databases-list.md)  v rozhraní Azure Cosmos DB API pro MongoDB pomocí REST API.
* [Model prostředků](continuous-backup-restore-resource-model.md) režimu průběžné zálohování.