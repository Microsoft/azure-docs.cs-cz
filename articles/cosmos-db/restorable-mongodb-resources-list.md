---
title: Seznam prostředků obnovitelné v rozhraní Azure Cosmos DB API pro MongoDB pomocí REST API
description: Vrátí seznam se seznamem databází a kolekcí, který na daném účtu existuje v daném časovém razítku a umístění. To pomáhá ve scénářích ověřit, jaké prostředky existují v daném časovém razítku a umístění.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: b5800c2e9c1f3b36a3ac9afe6cd68f706505fbe0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537437"
---
# <a name="list-restorable-resources-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Seznam prostředků obnovitelné v rozhraní Azure Cosmos DB API pro MongoDB pomocí REST API

> [!IMPORTANT]
> Funkce obnovení bodu v čase (režim průběžného zálohování) pro Azure Cosmos DB je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vrátí seznam se seznamem databází a kolekcí, který na daném účtu existuje v daném časovém razítku a umístění. To pomáhá ve scénářích ověřit, jaké prostředky existují v daném časovém razítku a umístění. Toto rozhraní API vyžaduje `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` oprávnění.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview
```

S nepovinnými parametry:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
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
| 200 OK | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult)| Operace se úspěšně dokončila. |
| Další stavové kódy | [DefaultErrorResponse](#defaulterrorresponse)| Chybová odezva popisující, proč se operace nezdařila |


## <a name="examples"></a>Příklady

### <a name="cosmosdbrestorablemongodbresourcelist"></a>CosmosDBRestorableMongodbResourceList

**Ukázková žádost**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Ukázková odpověď**

Stavový kód: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Collection1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Collection1",
        "Collection2"
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

| Definice | Popis | | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Konkrétní databáze, které mají být obnoveny. | | [DefaultErrorResponse](#defaulterrorresponse) | Odpověď na chybu od služby. | | [ErrorResponse](#errorresponse) | Chybná odpověď. | | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult) | Odpověď na operaci seznamu, která obsahuje prostředky SQL obnovitelné. |

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

### <a name="restorablemongodbresourceslistresult"></a><a id="restorablemongodbresourceslistresult"></a>RestorableMongodbResourcesListResult

Odpověď na operaci seznamu, která obsahuje rozhraní obnovitelné Azure Cosmos DB API pro prostředky MongoDB.

| **Název** | **Typ** | **Popis** |
| --- | --- | --- |
| hodnota |[DatabaseRestoreResource](#databaserestoreresource)[]| Seznam obnovitelné Azure Cosmos DB API pro prostředky MongoDB, včetně názvů databází a kolekcí. |

## <a name="next-steps"></a>Další kroky

* [Vypíše databáze obnovitelné](restorable-mongodb-databases-list.md)  v rozhraní Azure Cosmos DB API pro MongoDB pomocí REST API.
* [Model prostředků](continuous-backup-restore-resource-model.md) režimu průběžné zálohování.