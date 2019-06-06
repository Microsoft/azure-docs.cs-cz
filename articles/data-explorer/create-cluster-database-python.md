---
title: Vytvoření clusteru Průzkumník dat Azure a databáze s použitím jazyka Python
description: Zjistěte, jak vytvořit cluster Průzkumník dat Azure a databáze služby s použitím jazyka Python.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: caac53aec08e234f320ee4278a5a58bbc62bff68
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494584"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Vytvoření clusteru Průzkumník dat Azure a databáze s použitím jazyka Python

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Použití Průzkumníku dat Azure, nejprve vytvoříte cluster a vytvořit jednu nebo více databází v tomto clusteru. Pak můžete ingestovat data (načíst) do databáze tak, aby u ní můžete spouštět dotazy. V tomto článku vytvořte cluster a databáze s použitím jazyka Python.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="install-python-package"></a>Instalovat balíček Pythonu

Chcete-li nainstalovat balíček Pythonu pro Azure Průzkumník dat (Kusto), otevřete příkazový řádek, který má Python v cestě. Spusťte tento příkaz:

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Průzkumník dat Azure

1. Vytvoření clusteru pomocí následujícího příkazu:

    ```Python
    from azure.mgmt.kusto.kusto_management_client import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku

    credentials = xxxxxxxxxxxxxxx
    
    subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx'
    location = 'Central US'
    sku = 'D13_v2'
    capacity = 5
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku, capacity=capacity))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | název_clusteru | *mykustocluster* | Požadovaný název vašeho clusteru.|
   | Skladová položka | *D13_v2* | Skladová položka, která se použije pro váš cluster. |
   | resource_group_name | *testrg* | Název skupiny prostředků, ve kterém se cluster vytvoří. |

    Existují další volitelné parametry, které můžete použít, jako je například kapacita clusteru.
    
1. Nastavte [ *svoje přihlašovací údaje*](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)

1. Spusťte následující příkaz a zkontrolujte, zda byl úspěšně vytvořen cluster:

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

1. Spuštěním následujícího příkazu zobrazte databázi, kterou jste vytvořili:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Teď máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud budete chtít postupujte podle našich článků, zachovat prostředky, které jste vytvořili.
* Pokud chcete vyčistit prostředky, cluster odstraňte. Po odstranění clusteru se odstraní také všechny databáze v ní. Pomocí následujícího příkazu odstraňte cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Další postup

* [Ingestování dat pomocí knihovny Python Průzkumník dat Azure](python-ingest-data.md)
