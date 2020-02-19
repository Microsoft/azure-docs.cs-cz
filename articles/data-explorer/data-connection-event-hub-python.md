---
title: Vytvoření datového připojení centra událostí pro Azure Průzkumník dat pomocí Pythonu
description: V tomto článku se dozvíte, jak vytvořit datové připojení centra událostí pro Azure Průzkumník dat pomocí Pythonu.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: c08271286373ab8c3e621ee6fa59782ba2d16fb4
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444175"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Vytvoření datového připojení centra událostí pro Azure Průzkumník dat pomocí Pythonu

> [!div class="op_single_selector"]
> * [Azure Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Šablona Azure Resource Manageru](data-connection-event-hub-resource-manager.md)

V tomto článku vytvoříte připojení dat centra událostí pro Azure Průzkumník dat pomocí Pythonu. Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Průzkumník dat nabízí ingestování nebo načítání dat z Event Hubs, hub IoT a objektů BLOB zapsaných do kontejnerů objektů BLOB.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4 +](https://www.python.org/downloads/).

* [Cluster a databáze](create-cluster-database-python.md).

* [Mapování tabulek a sloupců](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Zásady databáze a tabulky](database-table-policies-python.md) (volitelné).

* [Centrum událostí s daty pro přijímání](ingest-data-event-hub.md#create-an-event-hub).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Přidání datového připojení centra událostí

Následující příklad ukazuje, jak přidat datové připojení centra událostí prostřednictvím kódu programu. Informace o přidání datového připojení centra událostí pomocí Azure Portal najdete v tématu [připojení k centru událostí](ingest-data-event-hub.md#connect-to-the-event-hub) .

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
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
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
|---|---|---|
| tenant_id | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | Vaše ID tenanta. Označuje se také jako ID adresáře.|
| subscriptionId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID předplatného, které používáte pro vytváření prostředků.|
| client_id | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| client_secret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, který má přístup k prostředkům ve vašem tenantovi. |
| resource_group_name | *testrg* | Název skupiny prostředků, která obsahuje váš cluster.|
| cluster_name | *mykustocluster* | Název vašeho clusteru.|
| database_name | *mykustodatabase* | Název cílové databáze v clusteru.|
| data_connection_name | *myeventhubconnect* | Požadovaný název datového připojení.|
| table_name | *StormEvents* | Název cílové tabulky v cílové databázi.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Název mapování sloupce souvisejícího s cílovou tabulkou.|
| data_format | *Formát* | Formát dat zprávy|
| event_hub_resource_id | *ID prostředku* | ID prostředku centra událostí, které obsahuje data pro ingestování. |
| consumer_group | *$Default* | Skupina uživatelů centra událostí.|
| location | *Střed USA* | Umístění prostředku datového připojení.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]