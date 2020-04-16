---
title: Parametrize propojených služeb v Azure Data Factory
description: Zjistěte, jak parametrizovat propojené služby v Azure Data Factory a předat dynamické hodnoty za běhu.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 8fa8603f2bee7e42db0f085d78117d61bd14ce5c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419387"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrize propojených služeb v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nyní můžete parametrizovat propojenou službu a předat dynamické hodnoty za běhu. Například pokud se chcete připojit k různým databázím na stejném serveru Azure SQL Database, můžete nyní parametrizovat název databáze v definici propojené služby. Tím zabráníte nutnosti vytvořit propojenou službu pro každou databázi na databázovém serveru Azure SQL. Můžete parametrizovat i další vlastnosti v definici propojené služby - například *Uživatelské jméno.*

Uživatelské rozhraní Datové továrny můžete použít na webu Azure Portal nebo programovací rozhraní k parametrizaci propojených služeb.

> [!TIP]
> Doporučujeme neparametrizovat hesla nebo tajné klíče. Místo toho uložte všechny připojovací řetězce do služby Azure Key Vault a parametrizujte *tajný název*.

Sedmiminutový úvod a ukázku této funkce najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Podporované zdroje dat

V tuto chvíli je parametrizace propojené služby podporována v uzdu datové továrny na webu Azure Portal pro následující úložiště dat. Pro všechna ostatní úložiště dat můžete parametrizovat propojenou službu výběrem ikony **Kód** na kartě **Připojení** a pomocí editoru JSON.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Databáze Cosmos
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

![Přidání dynamického obsahu do definice propojené služby](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Vytvoření nového parametru](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
