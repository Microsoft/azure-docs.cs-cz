---
title: Parametrizovat propojené služby v Azure Data Factory
description: Naučte se, jak parametrizovat propojené služby v Azure Data Factory a v době běhu předávat dynamické hodnoty.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: acc7284eb607d20ca1d62b478d802be56048bc6c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440095"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizovat propojené služby v Azure Data Factory

Nyní můžete parametrizovat propojenou službu a v době běhu předat dynamické hodnoty. Pokud se například chcete připojit k různým databázím na stejném serveru Azure SQL Database, můžete v definici propojené služby teď parametrizovat název databáze. Tím zabráníte tomu, abyste vytvořili propojenou službu pro každou databázi na serveru Azure SQL Database. V definici propojené služby můžete také parametrizovat jiné vlastnosti, například *uživatelské jméno.*

K parametrizovat propojených služeb můžete použít uživatelské rozhraní Data Factory v Azure Portal nebo programovacím rozhraní.

> [!TIP]
> Doporučujeme, abyste neparametrizovati hesla ani tajné klíče. Místo toho uložte všechny připojovací řetězce v Azure Key Vault a parametrizovat *název tajného klíče*.

Pokud chcete tuto funkci seznámit a předvedení této funkce, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Podporované zdroje dat

V tuto chvíli je propojená služba Parametrizace podporovaná v uživatelském rozhraní Data Factory v Azure Portal pro následující úložiště dat. U všech ostatních úložišť dat můžete propojenou službu parametrizovat tak, že na kartě **připojení** vyberete ikonu **kódu** a použijete Editor JSON.
- Databáze SQL Azure
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

![Přidat dynamický obsah do definice propojené služby](media/parameterize-linked-services/parameterize-linked-services-image1.png)

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
