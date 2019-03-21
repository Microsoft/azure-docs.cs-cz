---
title: 'Rychlý start: Vytvoření clusteru Průzkumník dat Azure a databáze s použitím jazyka Python'
description: Zjistěte, jak vytvořit cluster Průzkumník dat Azure a databáze služby pomocí Pythonu
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 4f87c5996ea323c26c32c1680ba6f627bf8f95c2
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287507"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Vytvoření clusteru Průzkumník dat Azure a databáze s použitím jazyka Python

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Tento rychlý start popisuje způsob vytvoření clusteru Průzkumník dat Azure a databáze s použitím jazyka Python.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="install-python-package"></a>Instalovat balíček Pythonu

Chcete-li nainstalovat balíček Pythonu pro Azure Průzkumník dat (Kusto), otevřete příkazový řádek, který má Python v cestě a spusťte tento příkaz:

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Průzkumník dat Azure

1. Vytvoření clusteru pomocí následujícího příkazu:

    

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | název_clusteru | *mykustocluster* | Požadovaný název vašeho clusteru.|
   | SKU | *D13_v2* | Skladová položka, která se použije pro váš cluster. |
   | resource_group_name | *testrg* | Název skupiny prostředků, ve kterém se cluster vytvoří. |

    Existují další volitelné parametry, které můžete použít, jako je například kapacita clusteru.
    
    Nastavení "pověření" vašich přihlašovacích údajů (Další informace najdete v části https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python )

2. Spusťte následující příkaz a zkontrolujte, zda byl úspěšně vytvořen cluster:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Pokud výsledek obsahuje `provisioningState` s `Succeeded` hodnotu, pak clusteru byl úspěšně vytvořen.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v Průzkumníku dat Azure clusteru

1. Vytvoření databáze pomocí následujícího příkazu:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | název_clusteru | *mykustocluster* | Název clusteru s novou databází.|
   | Název databáze database_name | *mykustodatabase* | Název databáze.|
   | resource_group_name | *testrg* | Název skupiny prostředků, ve kterém se cluster vytvoří. |
   | soft_delete_period | *3650 dnů, 0:00:00* | Množství času, které data zůstanou k dispozici pro dotazy. |
   | hot_cache_period | *3650 dnů, 0:00:00* | Množství času, které data zůstanou v mezipaměti. |

2. Spuštěním následujícího příkazu zobrazte databázi, kterou jste vytvořili:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Teď máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud plánujete postupovat podle našich dalších rychlých startů a kurzů, vytvořené prostředky zachovejte.
* Pokud chcete vyčistit prostředky, cluster odstraňte. Po odstranění clusteru se odstraní také všechny databáze v ní. Pomocí následujícího příkazu odstraňte cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Ingestování dat pomocí knihovny Python Průzkumník dat Azure](python-ingest-data.md)
