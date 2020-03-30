---
title: Vytvoření datového připojení služby IoT Hub pro Azure Data Explorer pomocí Pythonu
description: V tomto článku se dozvíte, jak vytvořit datové připojení služby IoT Hub pro Azure Data Explorer pomocí Pythonu.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 76c8ca24882f465bf2a973dc59736745178fc61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669518"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Vytvoření datového připojení služby IoT Hub pro Azure Data Explorer pomocí Pythonu (Preview)

> [!div class="op_single_selector"]
> * [Portál](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Šablona Azure Resource Manageru](data-connection-iot-hub-resource-manager.md)

V tomto článku vytvoříte datové připojení služby IoT Hub pro Azure Data Explorer pomocí Pythonu. Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer nabízí ingestování nebo načítání dat z centra událostí, ioT hubů a objektů blob zapsaná do kontejnerů objektů blob.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Cluster a databáze](create-cluster-database-python.md).

* [Mapování tabulek a sloupců](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Zásady databáze a tabulky](database-table-policies-python.md) (volitelné).

* [Centrum IoT Hub s nakonfigurovanou zásadou sdíleného přístupu](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Přidání datového připojení centra IoT Hub 

Následující příklad ukazuje, jak přidat datové připojení centra IoT programově. Na [jdete připojit tabulku Průzkumníka dat Azure do Služby IoT Hub,](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) kde najdete přidání datového připojení služby Iot Hub pomocí portálu Azure.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
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
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Vaše tenantské ID. Označuje se také jako ID adresáře.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID předplatného, které používáte pro vytvoření prostředků.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| client_secret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, která může přistupovat k prostředkům ve vašem tenantovi. |
| resource_group_name | *testrg* | Název skupiny prostředků obsahující váš cluster.|
| cluster_name | *mykustocluster* | Název clusteru.|
| Název_databáze | *mykustodatabáze* | Název cílové databáze v clusteru.|
| data_connection_name | *myeventhubconnect* | Požadovaný název datového připojení.|
| Table_name | *Bouřkové akce* | Název cílové tabulky v cílové databázi.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Název mapování sloupců související s cílovou tabulkou.|
| data_format | *Csv* | Formát dat zprávy.|
| iot_hub_resource_id | *ID prostředku* | ID prostředků vašeho centra IoT hub, který obsahuje data pro ingestování.|
| shared_access_policy_name | *iothubforread* | Název zásady sdíleného přístupu, který definuje oprávnění pro zařízení a služby pro připojení k služby IoT Hub. |
| consumer_group | *$Default* | Skupina spotřebitelů centra událostí.|
| location | *USA – střed* | Umístění prostředku datového připojení.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]