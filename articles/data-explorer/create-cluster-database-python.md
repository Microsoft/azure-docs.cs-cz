---
title: Vytvoření clusteru a databáze Azure Průzkumník dat pomocí Pythonu
description: Naučte se vytvářet cluster a databázi Azure Průzkumník dat pomocí Pythonu.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: b3329ccb3edb3077a45e3bbf9ba7b48d7e3a93a2
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996229"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Vytvoření clusteru a databáze Azure Průzkumník dat pomocí Pythonu

> [!div class="op_single_selector"]
> * [Bran](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [Prostředí](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Šablona ARM](create-cluster-database-resource-manager.md)

Azure Průzkumník dat je rychlá a plně spravovaná služba analýzy dat pro analýzu velkých objemů datových proudů z aplikací, webů, zařízení IoT a dalších prostředků v reálném čase. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do databáze, abyste na ni mohli spouštět dotazy. V tomto článku vytvoříte cluster a databázi pomocí Pythonu.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="install-python-package"></a>Nainstalovat balíček Pythonu

Pro instalaci balíčku Pythonu pro Azure Průzkumník dat (Kusto) otevřete příkazový řádek, který má v cestě Python. Spusťte tento příkaz:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Ověřování
Pro spuštění příkladů v tomto článku potřebujeme aplikaci služby Azure AD a instanční objekt, který má přístup k prostředkům. Pokud chcete vytvořit bezplatnou aplikaci Azure AD a přidat přiřazení role v oboru předplatného, podívejte se na [vytvořit aplikaci Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) . Také ukazuje, jak získat `Directory (tenant) ID`, `Application ID` a `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Azure Průzkumník dat

1. Vytvořte cluster pomocí následujícího příkazu:

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
    from azure.common.credentials import ServicePrincipalCredentials

    #Directory (tenant) ID
    tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    #Application ID
    client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    #Client Secret
    client_secret = "xxxxxxxxxxxxxx"
    subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Nastavením** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Požadovaný název clusteru.|
   | sku_name | *Standard_D13_v2* | SKU, které bude použito pro váš cluster. |
   | vrstva | *Standardní* | Úroveň SKU. |
   | klíčivost | *Automatické* | Počet instancí clusteru |
   | resource_group_name | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |

    > [!NOTE]
    > **Vytvoření clusteru** je dlouhodobá operace. Metoda **create_or_update** vrací instanci LROPoller, viz [Třída LROPoller](/python/api/msrest/msrest.polling.lropoller?view=azure-python) pro získání dalších informací.

1. Spusťte následující příkaz a ověřte, zda byl cluster úspěšně vytvořen:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Pokud výsledek obsahuje `provisioningState` s hodnotou `Succeeded`, cluster byl úspěšně vytvořen.

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
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Nastavením** | **Navrhovaná hodnota** | **Popis pole**|
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

## <a name="next-steps"></a>Další kroky

* [Ingestování dat pomocí knihovny Pythonu v Azure Průzkumník dat](python-ingest-data.md)
