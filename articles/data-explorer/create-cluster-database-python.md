---
title: Vytvoření clusteru Průzkumníka dat Azure & DB pomocí Pythonu
description: Zjistěte, jak vytvořit cluster a databázi Průzkumníka dat Azure pomocí Pythonu.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 8425058c9f6ac5b90c37a99f749a810672b406fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560503"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Vytvoření clusteru a databáze Průzkumníka dat Azure pomocí Pythonu

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Arm šablona](create-cluster-database-resource-manager.md)

V tomto článku vytvoříte cluster azure průzkumníkdat a databáze pomocí Pythonu. Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Data Explorer, nejdřív vytvořte cluster a vytvořte jednu nebo více databází v tomto clusteru. Potom ingestovat nebo načíst data do databáze, takže můžete spustit dotazy proti němu.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Azure AD aplikace a instanční objekt, který může přistupovat k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Získat hodnoty `Directory (tenant) ID` `Application ID`pro `Client Secret`, a .

## <a name="install-python-package"></a>Instalace balíčku Pythonu

Chcete-li nainstalovat balíček Pythonu pro Průzkumníka dat Azure (Kusto), otevřete příkazový řádek, který má Python v cestě. Spusťte tento příkaz:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Ověřování
Pro spuštění příklady v tomto článku potřebujeme azure ad aplikace a instanční objekt, který může získat přístup k prostředkům. Zaškrtněte [vytvořit aplikaci Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) k vytvoření bezplatné aplikace Azure AD a přidejte přiřazení role v oboru předplatného. To také `Directory (tenant) ID`ukazuje, jak `Application ID`získat `Client Secret`, a .

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Průzkumníka dat Azure

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

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Požadovaný název clusteru.|
   | sku_name | *Standard_D13_v2* | Skladová položka, která bude použita pro váš cluster. |
   | tier | *Standard* | Úroveň Skladové položky. |
   | capacity | *Číslo* | Počet instancí clusteru. |
   | resource_group_name | *testrg* | Název skupiny prostředků, kde bude vytvořen cluster. |

    > [!NOTE]
    > **Vytvoření clusteru** je dlouho běžící operace. Metoda **create_or_update** vrátí instanci LROPoller, naleznete v tématu [LROPoller třídy](/python/api/msrest/msrest.polling.lropoller?view=azure-python) získat další informace.

1. Spuštěním následujícího příkazu zkontrolujte, zda byl cluster úspěšně vytvořen:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Pokud výsledek `provisioningState` obsahuje `Succeeded` s hodnotou, byl cluster úspěšně vytvořen.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v clusteru Průzkumníka dat Azure

1. Vytvořte databázi pomocí následujícího příkazu:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = ReadWriteDatabase(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

        [!NOTE]
        If you are using Python version 0.4.0 or below, use Database instead of ReadWriteDatabase.

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Název clusteru, kde bude databáze vytvořena.|
   | Název_databáze | *mykustodatabáze* | Název databáze.|
   | resource_group_name | *testrg* | Název skupiny prostředků, kde bude vytvořen cluster. |
   | soft_delete_period | *3650 dní, 0:00:00* | Doba, po kterou budou data k dispozici pro dotaz. |
   | hot_cache_period | *3650 dní, 0:00:00* | Doba, po kterou budou data uložena v mezipaměti. |

1. Chcete-li zobrazit databázi, kterou jste vytvořili, spusťte následující příkaz:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Nyní máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud máte v plánu sledovat naše další články, uchovávejte zdroje, které jste vytvořili.
* Chcete-li vyčistit prostředky, odstraňte cluster. Když odstraníte cluster, odstraní také všechny databáze v něm. K odstranění clusteru použijte následující příkaz:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Další kroky

* [Ingestování dat pomocí knihovny Pythonu Průzkumníka dat Azure](python-ingest-data.md)
