---
title: Připojení databází v Průzkumníku dat Azure pomocí funkce databáze sledujících
description: Přečtěte si, jak připojit databáze v Průzkumníku dat Azure pomocí funkce databáze následovníka.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: f6dbdb54c1c5a5d477c3ccb988963758faab83b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140010"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Připojení databází v Azure Data Exploreru pomocí databáze sledujících

Funkce **databáze následovníka** umožňuje připojit databázi umístěnou v jiném clusteru ke clusteru Azure Data Explorer. **Databáze sledujících** je připojena v režimu *jen pro čtení,* což umožňuje zobrazit data a spouštět dotazy na data, která byla ingestována do **databáze odkazové čáry**. Databáze sledujících synchronizuje změny v databázích odkazujících. Kvůli synchronizaci je zpoždění dat několik sekund na několik minut v dostupnosti dat. Délka časové prodlevy závisí na celkové velikosti metadat databáze odkazové čáry. Databáze vedoucích a sledujících používají stejný účet úložiště k načtení dat. Úložiště je vlastněno databází odkazové čáry. Databáze sledujících zobrazí data bez nutnosti jejich ingestování. Vzhledem k tomu, že připojená databáze je databáze jen pro čtení, data, tabulky a zásady v databázi nelze změnit s výjimkou [zásad ukládání do mezipaměti](#configure-caching-policy), [objekty zabezpečení](#manage-principals)a [oprávnění](#manage-permissions). Připojené databáze nelze odstranit. Musí být odděleny vůdcem nebo následovníkem a teprve poté mohou být odstraněny. 

Připojení databáze k jinému clusteru pomocí funkce následovníka se používá jako infrastruktura pro sdílení dat mezi organizacemi a týmy. Tato funkce je užitečná pro oddělení výpočetních prostředků k ochraně produkčního prostředí před případy použití mimo produkční prostředí. Follower lze také přidružit náklady clusteru Azure Data Explorer na stranu, která spouští dotazy na data.

## <a name="which-databases-are-followed"></a>Které databáze jsou sledovány?

* Cluster může sledovat jednu databázi, několik databází nebo všechny databáze předváděcího clusteru. 
* Jeden cluster může sledovat databáze z více clusterů odkazující. 
* Cluster může obsahovat databáze sledujících i databáze odkazových čárek.

## <a name="prerequisites"></a>Požadavky

1. Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
1. [Vytvořte cluster a DB](/azure/data-explorer/create-cluster-database-portal) pro vedoucího a následovníka.
1. [Ingestovat data](/azure/data-explorer/ingest-sample-data) do databáze odkazové čáry pomocí jedné z různých metod popisovaných v [přehledu ingestování](/azure/data-explorer/ingest-data-overview).

## <a name="attach-a-database"></a>Připojení databáze

Existují různé metody, které můžete použít k připojení databáze. V tomto článku se zabýváme připojením databáze pomocí jazyka C# nebo šablony Azure Resource Manager. Chcete-li připojit databázi, musíte mít oprávnění pro vedoucí cluster a cluster sledujíc. Další informace o oprávněních naleznete v tématu [Správa oprávnění](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Připojení databáze pomocí c #

#### <a name="needed-nugets"></a>Potřebné NuGets

* Nainstalujte [soubor Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* K ověřování nainstalujte [microsoft.rest.clientruntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

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
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
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
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Připojení databáze pomocí šablony Azure Resource Manageru

V této části se naučíte připojit databázi k existujícímu cluser pomocí [šablony Azure Resource Manager](../azure-resource-manager/management/overview.md). 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
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
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
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
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
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

Šablonu Azure Resource Manager umíte nasadit [pomocí portálu Azure nebo](https://portal.azure.com) powershellu.

   ![nasazení šablony](media/follower/template-deployment.png)


|**Nastavení**  |**Popis**  |
|---------|---------|
|Název clusteru sledujíce     |  Název clusteru sledujících; kde bude šablona nasazena.  |
|Název konfigurace připojené databáze    |    Název připojeného objektu konfigurace databáze. Název může být libovolný řetězec, který je jedinečný na úrovni clusteru.     |
|Název databáze     |      Název databáze, která má být následována. Pokud chcete sledovat všechny databáze vedoucího, použijte '*'.   |
|ID prostředku vedoucího clusteru    |   ID prostředku vedoucího clusteru.      |
|Typ změny výchozích objektů    |   Výchozí typ změny hlavní. Může `Union`být `Replace` `None`nebo . Další informace o výchozím druhu hlavní změny naleznete v [tématu příkaz hlavní změny druhu řízení](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Umístění   |   Umístění všech prostředků. Vedoucí a následovník musí být na stejném místě.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Ověřte, zda byla databáze úspěšně připojena.

Chcete-li ověřit, že databáze byla úspěšně připojena, vyhledejte připojené databáze na [webu Azure Portal](https://portal.azure.com). 

1. Přejděte do clusteru sledujících a vyberte **databáze.**
1. Vyhledejte nové databáze jen pro čtení v seznamu databáze.

    ![Databáze sledujícíjen pro čtení](media/follower/read-only-follower-database.png)

Máte k dispozici i další možnosti:

1. Přejděte do předváděcího clusteru a vyberte **databáze.**
2. Zkontrolujte, zda jsou příslušné databáze označeny jako **SDÍLENÉ S OSTATNÍMI** > **Ano.**

    ![Čtení a zápis připojených databází](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Odpojte databázi sledující pomocí C # 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Odpojte připojenou databázi sledující chod od clusteru sledující

Cluster sledujících může odpojit libovolnou připojenou databázi následujícím způsobem:

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
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Odpojte připojenou databázi sledujících od clusteru vedoucích.

Vedoucí clusteru můžete odpojit všechny připojené databáze takto:

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
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Odpojení databáze sledující pomocí Pythonu

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Odpojte připojenou databázi sledující chod od clusteru sledující

Cluster sledujících může odpojit libovolnou připojenou databázi následujícím způsobem:

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
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Odpojte připojenou databázi sledujících od clusteru vedoucích.

Vedoucí clusteru můžete odpojit všechny připojené databáze takto:

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
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Správa objektů zabezpečení, oprávnění a zásad ukládání do mezipaměti

### <a name="manage-principals"></a>Správa objektů zabezpečení

Při připojování databáze zadejte **"výchozí objekty změny druhu"**. Výchozí nastavení je udržování kolekce databáze odkazových čar [autorizovaných objektů.](/azure/kusto/management/access-control/index#authorization)

|**Druhu** |**Popis**  |
|---------|---------|
|**Sjednocení**     |   Připojené objekty databáze bude vždy obsahovat původní databázové objekty a další nové objekty přidané do databáze následovníka.      |
|**Nahradit**   |    Žádné dědičnosti objektů zabezpečení z původní databáze. Pro připojenou databázi musí být vytvořeny nové objekty zabezpečení.     |
|**Žádné**   |   Připojené objekty databáze zahrnují pouze objekty původní databáze bez dalších objektů.      |

Další informace o použití řídicích příkazů ke konfiguraci autorizovaných objektů zabezpečení naleznete [v tématu Control commands for managing a follower cluster](/azure/kusto/management/cluster-follower).

### <a name="manage-permissions"></a>Spravovat oprávnění

Správa oprávnění k databázi jen pro čtení je stejná jako u všech typů databází. Viz [správa oprávnění na webu Azure Portal](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Konfigurace zásad ukládání do mezipaměti

Správce databáze sledujících může upravit [zásady ukládání](/azure/kusto/management/cache-policy) do mezipaměti připojené databáze nebo libovolné tabulky v hostitelském clusteru. Výchozí nastavení je zachování kolekce databáze odkazové čáry databáze a zásady ukládání do mezipaměti na úrovni tabulky. Můžete mít například 30 denní zásady ukládání do mezipaměti v databázi vedoucích pro spouštění měsíčních sestav a třídenní zásady ukládání do mezipaměti v databázi sledujících, abyste se dotazovali pouze na nejnovější data pro řešení potíží. Další informace o použití řídicích příkazů ke konfiguraci zásad ukládání do mezipaměti v databázi nebo tabulce sledujících naleznete v [tématu Řídicí příkazy pro správu clusteru sledujících](/azure/kusto/management/cluster-follower).

## <a name="limitations"></a>Omezení

* Následovník a vedoucí clustery musí být ve stejné oblasti.
* [Streamování ingestuzi](/azure/data-explorer/ingest-data-streaming) nelze použít v databázi, která je sledována.
* Šifrování dat pomocí [klíčů spravovaných zákazníkem](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) není podporováno v clusterech vedoucích i sledujících. 
* Databázi, která je připojena k jinému clusteru, nelze odstranit před odpojením.
* Cluster, který má databázi připojenou k jinému clusteru, nelze odstranit, než ji odpojíte.
* Cluster, který má připojené následovníka nebo odkazující databáze, nelze zastavit. 

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci clusteru sledujících naleznete [v tématu Řízení příkazů pro správu clusteru sledujících](/azure/kusto/management/cluster-follower).
