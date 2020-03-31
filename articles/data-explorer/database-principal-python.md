---
title: Přidání databázových objektů pro Azure Data Explorer pomocí Pythonu
description: V tomto článku se dozvíte, jak přidat objekty zabezpečení databáze pro Azure Data Explorer pomocí Pythonu.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8b9c4f4d5427b326c273558db0bff808068b192a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965005"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Přidání databázových objektů pro Azure Data Explorer pomocí Pythonu

> [!div class="op_single_selector"]
> * [C #](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Šablona Azure Resource Manageru](database-principal-resource-manager.md)

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. V tomto článku přidáte objekty databáze pro Azure Data Explorer pomocí Pythonu.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvoření clusteru a databáze](create-cluster-database-python.md)

## <a name="install-python-package"></a>Instalace balíčku Pythonu

Chcete-li nainstalovat balíček Pythonu pro Průzkumníka dat Azure (Kusto), otevřete příkazový řádek, který má Python v cestě. Spusťte tento příkaz:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Přidání objektu zabezpečení databáze

Následující příklad ukazuje, jak přidat objekt zabezpečení databáze programově.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
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
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Vaše tenantské ID. Označuje se také jako ID adresáře.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID předplatného, které používáte pro vytvoření prostředků.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| client_secret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, která může přistupovat k prostředkům ve vašem tenantovi. |
| resource_group_name | *testrg* | Název skupiny prostředků obsahující váš cluster.|
| cluster_name | *mykustocluster* | Název clusteru.|
| Název_databáze | *mykustodatabáze* | Název databáze.|
| principal_assignment_name | *databasePrincipalAssignment1* | Název hlavního prostředku databáze.|
| Principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID objektu zabezpečení, kterým může být e-mail uživatele, ID aplikace nebo název skupiny zabezpečení.|
| role | *Správce* | Role objektu zabezpečení databáze, který může být 'Admin', 'Ingestor', 'Monitor', 'Uživatel', 'NeomezenýDiváci', 'Prohlížeč'.|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID klienta hlavního povinného.|
| principal_type | *Aplikace* | Typ objektu zabezpečení, který může být "Uživatel", "App" nebo "Skupina"|

## <a name="next-steps"></a>Další kroky

* [Ingestování dat pomocí knihovny Pythonu Průzkumníka dat Azure](python-ingest-data.md)
