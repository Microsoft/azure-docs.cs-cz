---
title: K připojení databází v Azure Průzkumník dat použít funkci následovat databáze.
description: Přečtěte si informace o tom, jak připojit databáze v Azure Průzkumník dat pomocí funkce databáze následného.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: dd2c29632d70da64251c5e1736a9cb7d82f5d0dc
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667348"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>K připojení databází v Azure Průzkumník dat použít databázi sledování

Funkce **Database** by vám umožní připojit k vašemu clusteru Azure Průzkumník dat databázi umístěnou v jiném clusteru. **Databáze následného zpracování** je připojena v režimu jen *pro čtení* , což umožňuje zobrazit data a spouštět dotazy na data, která byla ingestovaná do **databáze vedoucího**procesu. Databáze sledování synchronizuje změny v databázích vedoucích. Z důvodu synchronizace je k dispozici prodleva mezi několika sekundami v dostupnosti dat. Délka časového prodlevy závisí na celkové velikosti metadat vedoucí databáze. Databáze vedoucí a následný používá stejný účet úložiště k načtení dat. Úložiště vlastní vedoucí databáze. Databáze následného zobrazení dat, aniž by je musela ingestovat. Vzhledem k tomu, že připojená databáze je databáze jen pro čtení, není možné upravovat data, tabulky a zásady v databázi s výjimkou [zásad ukládání do mezipaměti](#configure-caching-policy), [objektů zabezpečení](#manage-principals)a [oprávnění](#manage-permissions). Připojené databáze nejde odstranit. Musí být odpojeni vedoucím nebo následným a pouze potom je možné je odstranit. 

Připojení databáze k jinému clusteru pomocí schopnosti sledování se používá jako infrastruktura pro sdílení dat mezi organizacemi a týmy. Tato funkce je užitečná pro oddělení výpočetních prostředků k ochraně produkčního prostředí před neprodukčními případy použití. K přidružení nákladů na cluster Azure Průzkumník dat ke straně, která spouští dotazy na data, se dá použít i následný.

## <a name="which-databases-are-followed"></a>Které databáze jsou následovány?

* Cluster může splňovat jednu databázi, několik databází nebo všechny databáze vedoucího clusteru. 
* Jeden cluster může následovat po databázích z více vedoucích clusterů. 
* Cluster může obsahovat i databáze a vedoucí databáze pro následnou instalaci.

## <a name="prerequisites"></a>Předpoklady

1. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
1. [Vytvořte cluster a databázi](/azure/data-explorer/create-cluster-database-portal) pro vedoucího a následného.
1. Ingestování [dat](/azure/data-explorer/ingest-sample-data) do vedoucí databáze pomocí jedné z různých metod popsaných v tématu [Přehled](/azure/data-explorer/ingest-data-overview)ingestování.

## <a name="attach-a-database"></a>Připojení databáze

Existují různé metody, které můžete použít k připojení databáze. V tomto článku se podíváme na připojení databáze pomocí C# šablony nebo Azure Resource Manager. Aby bylo možné připojit databázi, je nutné mít oprávnění k zadanému clusteru vedoucího a následnému clusteru. Další informace o oprávněních najdete v tématu [Správa oprávnění](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Připojení databáze pomocíC#

#### <a name="needed-nugets"></a>Potřebný balíčky NuGet

* Nainstalujte [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Nainstalujte [Microsoft. REST. ClientRuntime. Azure. Authentication pro ověřování](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="code-example"></a>Příklad kódu

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "adc";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>Připojení databáze pomocí Pythonu

#### <a name="needed-modules"></a>Potřebné moduly

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Příklad kódu

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "adc"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Připojení databáze pomocí šablony Azure Resource Manager

V této části se dozvíte, jak připojit databázi pomocí [Azure Resource Manager šablony](../azure-resource-manager/resource-group-overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the leader cluster to create."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Nasazení šablony 

Šablonu Azure Resource Manager můžete nasadit [pomocí Azure Portal](https://portal.azure.com) nebo pomocí PowerShellu.

   ![nasazení šablony](media/follower/template-deployment.png)


|**Nastavení**  |**Popis**  |
|---------|---------|
|Název clusteru sledování     |  Název clusteru následných       |
|Název připojených konfigurací databáze    |    Název objektu připojené konfigurace databáze. Název musí být na úrovni clusteru jedinečný.     |
|Název databáze     |      Název databáze, která se má dodržovat Pokud chcete sledovat všechny databáze vedoucího vedoucího, použijte znak *.   |
|ID prostředku clusteru vedoucího procesu    |   ID prostředku vedoucího clusteru      |
|Výchozí typ změny objektů zabezpečení    |   Výchozí typ změny objektu zabezpečení. Může být `Union`, `Replace` nebo `None`. Další informace o výchozím typu změny objektu zabezpečení naleznete v tématu [hlavní ovládací prvek typu změny](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Umístění   |   Umístění všech prostředků. Vedoucí a následný musí být ve stejném umístění.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Ověření úspěšného připojení databáze

Chcete-li ověřit, zda byla databáze úspěšně připojena, vyhledejte v [Azure Portal](https://portal.azure.com)připojené databáze. 

1. Přejděte do clusteru sledování a vyberte **databáze** .
1. V seznamu databází vyhledejte nové databáze jen pro čtení.

    ![Databáze následného sledování jen pro čtení](media/follower/read-only-follower-database.png)

Jinou

1. Přejděte do clusteru vedoucího a vyberte **databáze** .
2. Ověřte, že jsou příslušné databáze označené jako **sdílené s ostatními** > **Ano** .

    ![Čtení a zápis připojených databází](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Odpojení databáze následného použitíC# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Odpojení připojené databáze od následného nástroje od clusteru následných

Cluster následného řízení může odpojit jakoukoli připojenou databázi následujícím způsobem:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Odpojení připojené databáze následného procesu od vedoucího clusteru

Cluster vedoucího procesu může odpojit jakoukoli připojenou databázi následujícím způsobem:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Odpojení databáze od následného použití Pythonu

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Odpojení připojené databáze od následného nástroje od clusteru následných

Cluster následného řízení může odpojit jakoukoli připojenou databázi následujícím způsobem:

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "adc"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Odpojení připojené databáze následného procesu od vedoucího clusteru

Cluster vedoucího procesu může odpojit jakoukoli připojenou databázi následujícím způsobem:

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "adc"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Správa objektů zabezpečení, oprávnění a zásad ukládání do mezipaměti

### <a name="manage-principals"></a>Spravovat objekty zabezpečení

Při připojování databáze zadejte **"výchozí druh úprav objektů zabezpečení"** . Ve výchozím nastavení je zachovává kolekce databáze vedoucích [autorizovaných objektů zabezpečení](/azure/kusto/management/access-control/index#authorization) .

|**Plnění** |**Popis**  |
|---------|---------|
|**Sjednocovací**     |   Připojené objekty databáze budou vždycky zahrnovat původní objekty zabezpečení databáze a další nové objekty zabezpečení přidané do databáze následného objektu.      |
|**Náhrady**   |    Žádná dědičnost objektů zabezpečení z původní databáze. Pro připojenou databázi je nutné vytvořit nové objekty zabezpečení.     |
|**NTato**   |   Připojené objekty zabezpečení databáze obsahují pouze objekty zabezpečení původní databáze bez dalších objektů zabezpečení.      |

Další informace o použití příkazů pro řízení ke konfiguraci autorizovaných objektů zabezpečení najdete v tématu [Řídicí příkazy pro správu clusteru následného](/azure/kusto/management/cluster-follower)řízení.

### <a name="manage-permissions"></a>Spravovat oprávnění

Správa oprávnění databáze jen pro čtení je stejná jako u všech typů databáze. Viz téma [Správa oprávnění v Azure Portal](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Konfigurace zásad ukládání do mezipaměti

Správce databáze následného řízení může upravit [zásady ukládání do mezipaměti](/azure/kusto/management/cache-policy) připojené databáze nebo jakékoli její tabulky v hostitelském clusteru. Ve výchozím nastavení je zachovává kolekce databází vedoucí databáze a zásad ukládání do mezipaměti na úrovni tabulky. Můžete mít například 30denní zásadu pro ukládání do mezipaměti v databázi vedoucího pro spouštění měsíčních sestav a tři dny pro ukládání do mezipaměti v databázi sledování a dotazovat se pouze na poslední data pro řešení potíží. Další informace o použití příkazů pro řízení pro konfiguraci zásad ukládání do mezipaměti v databázi a tabulce následného řízení najdete v tématu [Řídicí příkazy pro správu clusteru následných](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Omezení

* Následný a vedoucí cluster musí být ve stejné oblasti.
* Ingestování [streamování](/azure/data-explorer/ingest-data-streaming) se nedá použít na databázi, která se právě sleduje.
* Než odpojíte databázi, která je připojená k jinému clusteru, nemůžete ji odstranit.
* Nemůžete odstranit cluster, který má databázi připojenou k jinému clusteru, než ho odpojíte.
* Nelze zastavit cluster, který má připojené následné nebo vedoucí databáze. 

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci následného clusteru najdete v tématu [Řídicí příkazy pro správu clusteru následného](/azure/kusto/management/cluster-follower).