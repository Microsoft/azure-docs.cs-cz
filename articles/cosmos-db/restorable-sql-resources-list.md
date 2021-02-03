---
title: Vypsat prostředky rozhraní SQL API obnovitelné v Azure Cosmos DB pomocí REST API
description: Vrátí seznam databází a kontejnerů, které existují na účtu v daném časovém razítku a umístění. To pomáhá ve scénářích ověřit, jaké prostředky existují v daném časovém razítku a umístění.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: dbee87098dcc712669c332deac656cf5656ef4c4
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527363"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>Vypsat prostředky rozhraní SQL API obnovitelné v Azure Cosmos DB pomocí REST API

Vrátí seznam databází a kontejnerů, které existují na účtu v daném časovém razítku a umístění. To pomáhá ve scénářích ověřit, jaké prostředky existují v daném časovém razítku a umístění. Toto rozhraní API vyžaduje `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` oprávnění.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

S nepovinnými parametry:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>Parametry identifikátoru URI

| Name | V | Požaduje se | Typ | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | program | True |řetězec| Identifikátor instanceId GUID účtu databáze obnovitelné |
| **oblasti** | program | True | řetězec| Oblast Azure Cosmos DB, s mezerami mezi slovy a každým slovem velkými písmeny. |
| **subscriptionId** | program | True | řetězec| ID cílového předplatného |
| **verze API-Version** | query | True | řetězec | Verze rozhraní API, která se má použít pro tuto operaci. |
| **restoreLocation** | query | |řetězec| Umístění, kde jsou umístěny prostředky obnovitelné. |
| **restoreTimestampInUtc** | query | |řetězec| Časové razítko, kdy existovaly prostředky obnovitelné |

## <a name="responses"></a>Odpovědi

| Název | Typ | Description |
| --- | --- | --- |
| 200 OK | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult)| Operace se úspěšně dokončila. |
| Další stavové kódy | [DefaultErrorResponse](#defaulterrorresponse)| Chybová odezva popisující, proč se operace nezdařila |

## <a name="examples"></a>Příklady

### <a name="cosmosdbrestorablesqlresourcelist"></a>CosmosDBRestorableSqlResourceList

**Ukázková žádost**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Ukázková odpověď**

Stavový kód: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>Definice

| Definice | Popis | | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Konkrétní databáze, které mají být obnoveny. | | [DefaultErrorResponse](#defaulterrorresponse) | Odpověď na chybu od služby. | | [ErrorResponse](#errorresponse) | Chybná odpověď. | | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult) | Odpověď na operaci seznamu, která obsahuje prostředky SQL obnovitelné. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

Konkrétní databáze, které mají být obnoveny.

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| collectionNames |řetězec []| Názvy kolekcí, které jsou k dispozici pro obnovení. |
| Databáze |řetězec| Název databáze, která je k dispozici pro obnovení. |

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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>RestorableSqlResourcesListResult

Odpověď na operaci seznamu, která obsahuje prostředky SQL obnovitelné.

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| hodnota |[DatabaseRestoreResource](#databaserestoreresource)[]| Seznam prostředků obnovitelné SQL, včetně názvů databází a kolekcí |

## <a name="next-steps"></a>Další kroky

* [Vypíše databáze obnovitelné](restorable-sql-databases-list.md) v Azure Cosmos SQL API pomocí REST API.
* [Model prostředků](continuous-backup-restore-resource-model.md) režimu průběžné zálohování.