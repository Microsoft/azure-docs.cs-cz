---
title: Vytvoření clusteru a databáze Azure Průzkumník dat pomocí Pythonu
description: Naučte se vytvářet cluster a databázi Azure Průzkumník dat pomocí Pythonu.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e3f58e596db26c04a8f3be4f87eb129fadf5e328
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141738"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Vytvoření clusteru a databáze Azure Průzkumník dat pomocí Pythonu

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do databáze, abyste na ni mohli spouštět dotazy. V tomto článku vytvoříte cluster a databázi pomocí Pythonu.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="install-python-package"></a>Instalovat balíček Pythonu

Pro instalaci balíčku Pythonu pro Azure Průzkumník dat (Kusto) otevřete příkazový řádek, který má v cestě Python. Spusťte tento příkaz:

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Azure Průzkumník dat

1. Vytvořte cluster pomocí následujícího příkazu:

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
   | cluster_name | *mykustocluster* | Požadovaný název clusteru.|
   | SKU | *D13_v2* | SKU, které bude použito pro váš cluster. |
   | resource_group_name | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |

    Existují další nepovinné parametry, které můžete použít, například kapacitu clusteru.
    
1. Nastavení [ *přihlašovacích údajů*](/azure/python/python-sdk-azure-authenticate)

1. Spusťte následující příkaz a ověřte, zda byl cluster úspěšně vytvořen:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Pokud výsledek obsahuje `provisioningState` `Succeeded` hodnotu, cluster se úspěšně vytvořil.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v clusteru Azure Průzkumník dat

1. Vytvořte databázi pomocí následujícího příkazu:

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
   | cluster_name | *mykustocluster* | Název clusteru, ve kterém se databáze vytvoří.|
   | název_databáze | *mykustodatabase* | Název vaší databáze.|
   | resource_group_name | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |
   | soft_delete_period | *3650 dní, 0:00:00* | Doba, po kterou budou data uchována k dispozici pro dotaz. |
   | hot_cache_period | *3650 dní, 0:00:00* | Doba, po kterou budou data uchována v mezipaměti. |

1. Spuštěním následujícího příkazu zobrazíte databázi, kterou jste vytvořili:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Teď máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud plánujete postupovat podle našich dalších článků, ponechejte prostředky, které jste vytvořili.
* Pokud chcete vyčistit prostředky, odstraňte cluster. Když odstraníte cluster, odstraní se i všechny jeho databáze. Pomocí následujícího příkazu odstraňte cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Další postup

* [Ingestování dat pomocí knihovny Pythonu v Azure Průzkumník dat](python-ingest-data.md)
