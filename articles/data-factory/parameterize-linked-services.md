---
title: Parametrizovat propojené služby v Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak parametrizovat propojené služby v Azure Data Factory a předat dynamické hodnoty v době běhu.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: douglasl
ms.openlocfilehash: 7bc603ceee1f85e6f00cdac9a6ac098abb39e217
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48251001"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizovat propojené služby v Azure Data Factory

Teď můžete parametrizovat propojené služby a předat dynamické hodnoty v době běhu. Například pokud se chcete připojit do různých databází na stejném serveru Azure SQL Database, můžete nyní parametrizovat název databáze v definici propojené služby. Předchází se tak nutnosti vytvářet propojené služby pro každou databázi na serveru Azure SQL database. Můžete například parametrizovat další vlastnosti v definici propojené služby - *uživatelské jméno.*

Uživatelské rozhraní Data Factory na webu Azure Portal nebo programovací rozhraní můžete parametrizovat propojené služby.

> [!TIP]
> Doporučujeme, abyste se parametrizovat hesla nebo tajné kódy. Místo toho Store všechny připojovací řetězce ve službě Azure Key Vault a parametrizovat *název tajného kódu*.

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
