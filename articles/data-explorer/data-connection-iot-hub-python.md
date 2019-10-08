---
title: Vytvoření datového připojení IoT Hub pro Azure Průzkumník dat pomocí Pythonu
description: V tomto článku se dozvíte, jak vytvořit datové připojení IoT Hub pro Azure Průzkumník dat pomocí Pythonu.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1e6cd94e18382b3e66fc6a9c284539bc178e199c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031640"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Vytvoření datového připojení IoT Hub pro Azure Průzkumník dat pomocí Pythonu (Preview)

> [!div class="op_single_selector"]
> * [Azure Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Průzkumník dat nabízí ingestování (načítání dat) z Event Hubs, rozbočovačů IoT a objektů BLOB zapsaných do kontejnerů objektů BLOB. V tomto článku vytvoříte datové připojení IoT Hub pro Azure Průzkumník dat pomocí Pythonu.

## <a name="prerequisites"></a>Předpoklady

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* Vytvoření [clusteru a databáze](create-cluster-database-csharp.md)

* Vytvořit [mapování tabulek a sloupců](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

* Nastavení [zásad databáze a tabulek](database-table-policies-csharp.md) (volitelné)

* Vytvoření služby [IoT Hub se nakonfigurovanými zásadami sdíleného přístupu](ingest-data-iot-hub.md#create-an-iot-hub)

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Přidání datového připojení IoT Hub 

Následující příklad ukazuje, jak přidat IoT Hub datové připojení prostřednictvím kódu programu. Pokud chcete přidat datové připojení ke službě IoT Hub pomocí Azure Portal, přečtěte si téma [připojení tabulky Azure Průzkumník dat k IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) .

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

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
data_connection_name = "myeventhubconnect";
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread";
consumer_group = "$Default";
location = "Central US";
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents";
mapping_rule_name = "StormEvents_CSV_Mapping";
data_format = "csv";

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
|---|---|---|
| tenant_id | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | Vaše ID tenanta. Označuje se také jako ID adresáře.|
| subscriptionId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID předplatného, které používáte pro vytváření prostředků.|
| client_id | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| client_secret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, který má přístup k prostředkům ve vašem tenantovi. |
| resource_group_name | *testrg* | Název skupiny prostředků, která obsahuje váš cluster.|
| cluster_name | *mykustocluster* | Název vašeho clusteru.|
| název_databáze | *mykustodatabase* | Název cílové databáze v clusteru.|
| data_connection_name | *myeventhubconnect* | Požadovaný název datového připojení.|
| TABLE_NAME | *StormEvents* | Název cílové tabulky v cílové databázi.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Název mapování sloupce souvisejícího s cílovou tabulkou.|
| data_format | *Formát* | Formát dat zprávy|
| iot_hub_resource_id | *ID prostředku* | ID prostředku vašeho centra IoT, které obsahuje data pro ingestování.|
| shared_access_policy_name | *iothubforread* | Název zásad sdíleného přístupu, který definuje oprávnění pro zařízení a služby pro připojení k IoT Hub. |
| consumer_group | *$Default* | Skupina uživatelů centra událostí.|
| location | *Střed USA* | Umístění prostředku datového připojení.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]