---
title: Vytvoření datového připojení Grid událostí pro Azure Data Explorer pomocí Pythonu
description: V tomto článku se dozvíte, jak vytvořit datové připojení Grid událostí pro Azure Data Explorer pomocí Pythonu.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1439383598517f57bc77e718d4ded7f53941d3bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444192"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Vytvoření datového připojení Grid událostí pro Azure Data Explorer pomocí Pythonu

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Šablona Azure Resource Manageru](data-connection-event-grid-resource-manager.md)

V tomto článku vytvoříte datové připojení Event Grid pro Azure Data Explorer pomocí Pythonu. Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer nabízí ingestování nebo načítání dat z centra událostí, ioT hubů a objektů blob zapsaná do kontejnerů objektů blob.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Cluster a databáze](create-cluster-database-python.md).

* [Mapování tabulek a sloupců](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Zásady databáze a tabulky](database-table-policies-csharp.md) (volitelné).

* [Účet úložiště s předplatným Služby event grid](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Přidání datového připojení Mřížky událostí

Následující příklad ukazuje, jak přidat datové připojení Event Grid programově. V Průzkumníkovi dat Azure pro přidání datového připojení Event Grid pomocí portálu Azure najdete v tématu [vytvoření datového připojení Event Grid.](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
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
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Vaše tenantské ID. Označuje se také jako ID adresáře.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID předplatného, které používáte pro vytvoření prostředků.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| client_secret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, která může přistupovat k prostředkům ve vašem tenantovi. |
| resource_group_name | *testrg* | Název skupiny prostředků obsahující váš cluster.|
| cluster_name | *mykustocluster* | Název clusteru.|
| Název_databáze | *mykustodatabáze* | Název cílové databáze v clusteru.|
| data_connection_name | *myeventhubconnect* | Požadovaný název datového připojení.|
| Table_name | *Bouřkové akce* | Název cílové tabulky v cílové databázi.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Název mapování sloupců související s cílovou tabulkou.|
| data_format | *Csv* | Formát dat zprávy.|
| event_hub_resource_id | *ID prostředku* | ID prostředku centra událostí, kde je mřížka událostí nakonfigurována pro odesílání událostí. |
| storage_account_resource_id | *ID prostředku* | ID prostředku vašeho účtu úložiště, který uchovává data pro ingestování. |
| consumer_group | *$Default* | Skupina spotřebitelů centra událostí.|
| location | *USA – střed* | Umístění prostředku datového připojení.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]