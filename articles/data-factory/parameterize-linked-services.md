---
title: Parametrizovat propojené služby v Azure Data Factory
description: Naučte se, jak parametrizovat propojené služby v Azure Data Factory a v době běhu předávat dynamické hodnoty.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2021
author: dcstwh
ms.author: weetok
manager: anandsub
ms.openlocfilehash: e463328df195b5a91db8ce272d138f980d000e79
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232059"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizovat propojené služby v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nyní můžete parametrizovat propojenou službu a v době běhu předat dynamické hodnoty. Pokud se například chcete připojit k různým databázím na stejném logickém serveru SQL, můžete v definici propojené služby nyní parametrizovat název databáze. Tím zabráníte tomu, abyste vytvořili propojenou službu pro každou databázi na logickém SQL serveru. V definici propojené služby můžete také parametrizovat jiné vlastnosti, například *uživatelské jméno.*

K parametrizovat propojených služeb můžete použít uživatelské rozhraní Data Factory v Azure Portal nebo programovacím rozhraní.

> [!TIP]
> Doporučujeme, abyste neparametrizovati hesla ani tajné klíče. Místo toho uložte všechny připojovací řetězce v Azure Key Vault a parametrizovat *název tajného klíče*.

Pokud chcete tuto funkci seznámit a předvedení této funkce, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Podporované typy propojených služeb

Můžete parametrizovat libovolný typ propojené služby.
Při vytváření propojené služby na uživatelském rozhraní poskytuje Data Factory integrované prostředí parametrizace pro následující typy propojených služeb. V okně pro vytvoření nebo úpravu propojené služby můžete najít možnosti pro nové parametry a přidat dynamický obsah.

- Amazon Redshift
- Amazon S3
- Azure Cosmos DB (SQL API)
- Azure Database for MySQL
- Azure Databricks
- Azure Key Vault
- Azure SQL Database
- Azure SQL Managed Instance
- Azure Synapse Analytics 
- MySQL
- Oracle
- SQL Server
- Obecná rozhraní HTTP
- Obecný standard REST

U ostatních typů propojených služeb, které nejsou uvedené v seznamu výše, můžete propojenou službu parametrizovat úpravou formátu JSON v uživatelském rozhraní:

- V okně pro vytvoření nebo úpravu propojené služby – > rozbalte políčko Upřesnit v dolní části > zaškrtněte políčko zadat dynamický obsah ve formátu JSON – > zadejte datovou část JSON propojené služby. 
- Nebo po vytvoření propojené služby bez Parametrizace se v [centru pro správu](author-visually.md#management-hub) – > propojených službách – > najít konkrétní propojenou službu – > kliknutím na kód (tlačítko {} ) upravte JSON. 

Chcete-li [](#json) přidat ` parameters` oddíl s definicemi parametrů a odkazovat na parametr pomocí, přečtěte si ukázku JSON ` @{linkedService().paraName} ` .

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
