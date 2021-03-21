---
title: Model prostředků pro funkci obnovení Azure Cosmos DBho bodu v čase.
description: Tento článek vysvětluje model prostředků pro funkci obnovení k určitému bodu v čase služby Azure Cosmos DB. Obsahuje informace o parametrech, které podporují průběžné zálohování, a prostředcích, které je možné obnovit v účtech rozhraní API služby Azure Cosmos DB pro SQL a MongoDB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: edf60a5c454d34a2424ef7981b02952ffbfd3bde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097223"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Model prostředků pro funkci obnovení Azure Cosmos DBho bodu v čase (Preview)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkce obnovení bodu v čase (režim průběžného zálohování) pro Azure Cosmos DB je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek popisuje model prostředků pro funkci obnovení Azure Cosmos DBho bodu v čase (Preview). Obsahuje informace o parametrech, které podporují průběžné zálohování, a prostředcích, které je možné obnovit v účtech rozhraní API služby Azure Cosmos DB pro SQL a MongoDB.

## <a name="database-accounts-resource-model"></a>Model prostředků databázového účtu

Model prostředků databázového účtu se aktualizuje o několik dalších vlastností, které podporují nové scénáře obnovení. Tyto vlastnosti jsou **BackupPolicy, CreateMode a RestoreParameters.**

### <a name="backuppolicy"></a>BackupPolicy

Nová vlastnost na úrovni účtu zásady zálohování s názvem `Type` v části `backuppolicy` parametr umožňuje průběžné zálohování a funkce obnovení k určitému bodu v čase. Tento režim se nazývá **průběžné zálohování**. Ve verzi Public Preview můžete tento režim nastavit pouze při vytváření účtu. Po povolení budou mít všechny kontejnery a databáze vytvořené v rámci tohoto účtu průběžné zálohování a funkce obnovení k bodu v čase, které jsou ve výchozím nastavení povolené.

> [!NOTE]
> V současné době je funkce obnovení v okamžiku v čase ve verzi Public Preview a je dostupná pro Azure Cosmos DB API pro MongoDB a účty SQL. Po vytvoření účtu s nepřetržitým režimem ho nemůžete přepnout na pravidelný režim.

### <a name="createmode"></a>CreateMode

Tato vlastnost indikuje, jak byl účet vytvořen. Možné hodnoty jsou *Default* a *Restore*. Chcete-li provést obnovení, nastavte tuto hodnotu pro *obnovení* a poskytněte příslušné hodnoty `RestoreParameters` Vlastnosti.

### <a name="restoreparameters"></a>RestoreParameters

`RestoreParameters`Prostředek obsahuje podrobnosti o operaci obnovení, včetně ID účtu, času obnovení a prostředků, které je potřeba obnovit.

|Název vlastnosti |Description  |
|---------|---------|
|restoreMode  | Režim obnovení by měl být *PointInTime* |
|restoreSource   |  ID instance zdrojového účtu, ze kterého se má obnovení iniciovat       |
|restoreTimestampInUtc  | Bod v čase ve standardu UTC, do kterého má být účet obnoven. |
|databasesToRestore   | Seznam `DatabaseRestoreSource` objektů pro určení, které databáze a kontejnery by měly být obnoveny. Pokud je tato hodnota prázdná, obnoví se celý účet.   |

**DatabaseRestoreResource** – každý prostředek představuje jednu databázi a všechny kolekce v rámci této databáze.

|Název vlastnosti |Description  |
|---------|---------|
|Databáze | Název databáze |
| collectionNames| Seznam kontejnerů v této databázi |

### <a name="sample-resource"></a>Ukázkový prostředek

Následující JSON je ukázkový prostředek databázového účtu s povoleným průběžným zálohováním:

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    },
}
}
```

## <a name="restorable-resources"></a>Prostředky obnovitelné

K dispozici je sada nových prostředků a rozhraní API, které vám pomohou odhalit důležité informace o prostředcích, které je možné obnovit, umístění, z nichž lze obnovit, a časová razítka při provádění klíčových operací na těchto prostředcích.

> [!NOTE]
> Každé rozhraní API, které se používá k vytvoření výčtu těchto prostředků, vyžaduje následující oprávnění:
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>Účet databáze obnovitelné

Tento prostředek obsahuje instanci databázového účtu, kterou je možné obnovit. Databázový účet může být buď odstraněný, nebo živý účet. Obsahuje informace, které vám umožní najít zdrojový účet databáze, který chcete obnovit.

|Název vlastnosti |Popis  |
|---------|---------|
| ID | Jedinečný identifikátor prostředku |
| accountName | Název globálního databázového účtu. |
| creationTime | Čas ve standardu UTC, kdy byl účet vytvořen.  |
| deletionTime | Čas ve standardu UTC, kdy se účet odstranil  Tato hodnota je prázdná, pokud je účet živý. |
| apiType | Typ rozhraní API účtu Azure Cosmos DB. |
| restorableLocations | Seznam umístění, kde existoval účet. |
| restorableLocations: Location | Název oblasti regionálního účtu. |
| restorableLocations: regionalDatabaseAccountInstanceI | Identifikátor GUID regionálního účtu. |
| restorableLocations: creationTime | Čas ve standardu UTC, kdy se vytvořil regionální účet.|
| restorableLocations: deletionTime | Čas ve standardu UTC, kdy se odstranil regionální účet. Tato hodnota je prázdná, pokud je místní účet živý.|

Pokud chcete získat seznam všech účtů obnovitelné, přečtěte si článek [účty obnovitelné Database – list](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorabledatabaseaccounts/list) nebo [obnovitelné Database Accounts-list podle umístění](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorabledatabaseaccounts/listbylocation) .

### <a name="restorable-sql-database"></a>Obnovitelné SQL Database

Každý prostředek obsahuje informace o události mutace, jako je vytváření a odstraňování, k nimž došlo na SQL Database. Tyto informace můžou pomáhat ve scénářích, kdy se databáze nedopatřením odstranila, a pokud potřebujete zjistit, kdy k této události došlo.

|Název vlastnosti |Description  |
|---------|---------|
| eventTimestamp | Čas ve standardu UTC, kdy se databáze vytvoří nebo odstraní. |
| ownerId | Název databáze SQL. |
| ownerResourceId | ID prostředku databáze SQL|
| operationType | Typ operace této události databáze. Tady jsou možné hodnoty:<br/><ul><li>Vytvořit: událost vytvoření databáze</li><li>Odstranit: událost odstranění databáze</li><li>Nahradit: událost změny databáze</li><li>SystemOperation: událost změny databáze aktivovaná systémem. Uživatel neinicioval tuto událost.</li></ul> |
| database |Vlastnosti databáze SQL v době události|

Seznam všech mutací databáze najdete v článku [databáze SQL obnovitelné – seznam](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablesqldatabases/list) článků.

### <a name="restorable-sql-container"></a>Obnovitelné kontejner SQL

Každý prostředek obsahuje informace o události mutace, jako je vytváření a odstraňování, k nimž došlo na kontejneru SQL. Tyto informace můžou pomáhat ve scénářích, kdy se kontejner změnil nebo odstranil, a pokud potřebujete zjistit, kdy k této události došlo.

|Název vlastnosti |Description  |
|---------|---------|
| eventTimestamp    | Čas ve standardu UTC, kdy došlo k této události kontejneru|
| ownerId| Název kontejneru SQL.|
| ownerResourceId   | ID prostředku kontejneru SQL.|
| operationType | Typ operace této události kontejneru. Tady jsou možné hodnoty: <br/><ul><li>Vytvořit: událost vytvoření kontejneru</li><li>Odstranit: událost odstranění kontejneru</li><li>Replace: událost úprav kontejneru</li><li>SystemOperation: událost úpravy kontejneru spuštěná systémem. Uživatel neinicioval tuto událost.</li></ul> |
| kontejner | Vlastnosti kontejneru SQL v době události.|

Pokud chcete získat seznam všech mutací kontejnerů ve stejné databázi, přečtěte si článek [obnovitelné kontejnerů SQL – seznam](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablesqlcontainers/list) .

### <a name="restorable-sql-resources"></a>Obnovitelné prostředků SQL

Každý prostředek představuje jednu databázi a všechny kontejnery v této databázi.

|Název vlastnosti |Description  |
|---------|---------|
| Databáze  | Název databáze SQL.
| collectionNames   | Seznam kontejnerů SQL v této databázi.|

Seznam SQL Database a kontejneru, který v daném časovém razítku a umístění existuje, získáte v článku [obnovitelné SQL Resources-list](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablesqlresources/list) .

### <a name="restorable-mongodb-database"></a>Databáze obnovitelné MongoDB

Každý prostředek obsahuje informace o události mutace, jako je vytváření a odstraňování, k nimž došlo v databázi MongoDB. Tyto informace můžou pomáhat v situaci, kdy se databáze nedopatřením odstranila, a uživatel musí zjistit, kdy k této události došlo.

|Název vlastnosti |Description  |
|---------|---------|
|eventTimestamp| Čas ve standardu UTC, kdy se tato databázová událost stala.|
| ownerId| Název databáze MongoDB. |
| ownerResourceId   | ID prostředku databáze MongoDB |
| operationType |   Typ operace této události databáze. Tady jsou možné hodnoty:<br/><ul><li> Vytvořit: událost vytvoření databáze</li><li> Odstranit: událost odstranění databáze</li><li> Nahradit: událost změny databáze</li><li> SystemOperation: událost změny databáze aktivovaná systémem. Uživatel neinicioval tuto událost. </li></ul> |

Seznam všech mutací databáze najdete v článku [databáze obnovitelné MongoDB – seznam](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablemongodbdatabases/list) článků.

### <a name="restorable-mongodb-collection"></a>Kolekce MongoDB obnovitelné

Každý prostředek obsahuje informace o události mutace, jako je vytváření a odstraňování, ke kterým došlo v kolekci MongoDB. Tyto informace můžou pomáhat ve scénářích, kdy se kolekce změnila nebo odstranila, a uživatel musí zjistit, kdy k této události došlo.

|Název vlastnosti |Description  |
|---------|---------|
| eventTimestamp |Čas ve standardu UTC, kdy došlo k této události kolekce. |
| ownerId| Název kolekce MongoDB |
| ownerResourceId   | ID prostředku kolekce MongoDB |
| operationType |Typ operace této události kolekce. Tady jsou možné hodnoty:<br/><ul><li>Vytvořit: událost vytvoření kolekce</li><li>Odstranit: událost odstranění kolekce</li><li>Nahradit: událost změny kolekce</li><li>SystemOperation: událost změny kolekce aktivovaná systémem. Uživatel neinicioval tuto událost.</li></ul> |

Pokud chcete získat seznam všech mutací kontejnerů ve stejné databázi, přečtěte si článek [obnovitelné MongoDB Collections-list](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablemongodbcollections/list) .

### <a name="restorable-mongodb-resources"></a>Prostředky obnovitelné MongoDB

Každý prostředek představuje jednu databázi a všechny kolekce v rámci této databáze.

|Název vlastnosti |Description  |
|---------|---------|
| Databáze  |Název databáze MongoDB. |
| collectionNames | Seznam kolekcí MongoDB v rámci této databáze. |

Seznam všech kombinací databáze MongoDB a kolekcí, které existují na účtu v daném časovém razítku a umístění, najdete v článku [obnovitelné MongoDB Resources-list](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablemongodbresources/list) .

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte a spravujte průběžné zálohování pomocí [Azure Portal](continuous-backup-restore-portal.md), [PowerShellu](continuous-backup-restore-powershell.md), rozhraní příkazového [řádku](continuous-backup-restore-command-line.md)nebo [Azure Resource Manager](continuous-backup-restore-template.md).
* [Správa oprávnění](continuous-backup-restore-permissions.md) potřebných pro obnovení dat pomocí režimu průběžné zálohování.
