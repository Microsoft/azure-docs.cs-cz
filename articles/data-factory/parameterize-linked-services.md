---
title: Parametrizovat propojené služby v Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak parametrizovat propojené služby v Azure Data Factory a předat dynamické hodnoty v době běhu.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/18/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 0239c53f98fba201b6d70e1e2212eea36134e30d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635524"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizovat propojené služby v Azure Data Factory

Teď můžete parametrizovat propojené služby a předat dynamické hodnoty v době běhu. Například pokud se chcete připojit do různých databází na stejném serveru Azure SQL Database, můžete nyní parametrizovat název databáze v definici propojené služby. Předchází se tak nutnosti vytvářet propojené služby pro každou databázi na serveru Azure SQL database. Můžete například parametrizovat další vlastnosti v definici propojené služby - *uživatelské jméno.*

Uživatelské rozhraní Data Factory na webu Azure Portal nebo programovací rozhraní můžete parametrizovat propojené služby.

> [!TIP]
> Doporučujeme, abyste se parametrizovat hesla nebo tajné kódy. Místo toho Store všechny připojovací řetězce ve službě Azure Key Vault a parametrizovat *název tajného kódu*.

Pro zavedení sedm po minutách a ukázku této funkce z následujícího videa:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Úložiště dat podporovaných

V tuto chvíli Parametrizace propojené služby je podporována v Uživatelském rozhraní Data Factory na webu Azure Portal pro následující typy úložišť dat. Pro všechny ostatní úložišť dat, můžete parametrizovat propojenou službu tak, že vyberete **kód** na ikonu **připojení** kartu a pomocí editoru JSON.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

![Přidat dynamický obsah k definici propojené služby](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Vytvoření nového parametru](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
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
