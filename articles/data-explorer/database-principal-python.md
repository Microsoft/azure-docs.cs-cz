---
title: Přidání objektů zabezpečení databáze pro Azure Průzkumník dat pomocí Pythonu
description: V tomto článku se dozvíte, jak přidat objekty zabezpečení databáze pro Azure Průzkumník dat pomocí Pythonu.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8b9c4f4d5427b326c273558db0bff808068b192a
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965005"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Přidání objektů zabezpečení databáze pro Azure Průzkumník dat pomocí Pythonu

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Šablona Azure Resource Manageru](database-principal-resource-manager.md)

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. V tomto článku přidáte objekty zabezpečení databáze pro Azure Průzkumník dat pomocí Pythonu.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvoření clusteru a databáze](create-cluster-database-python.md)

## <a name="install-python-package"></a>Instalovat balíček Pythonu

Pro instalaci balíčku Pythonu pro Azure Průzkumník dat (Kusto) otevřete příkazový řádek, který má v cestě Python. Spusťte tento příkaz:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Přidat objekt zabezpečení databáze

Následující příklad ukazuje, jak programově přidat objekt zabezpečení databáze.

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
| tenant_id | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | Vaše ID tenanta. Označuje se také jako ID adresáře.|
| subscription_id | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID předplatného, které používáte pro vytváření prostředků.|
| client_id | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| client_secret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, který má přístup k prostředkům ve vašem tenantovi. |
| resource_group_name | *testrg* | Název skupiny prostředků, která obsahuje váš cluster.|
| cluster_name | *mykustocluster* | Název vašeho clusteru.|
| database_name | *mykustodatabase* | Název vaší databáze.|
| principal_assignment_name | *databasePrincipalAssignment1* | Název vašeho prostředku objektu zabezpečení databáze.|
| principal_id | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID objektu zabezpečení, které může být e-mailem uživatele, ID aplikace nebo názvem skupiny zabezpečení.|
| roles | *Správ* | Role objektu zabezpečení databáze, která může být "admin", "ingesta", "monitor", "User", "UnrestrictedViewers", "Viewer".|
| tenant_id_for_principal | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID tenanta objektu zabezpečení|
| principal_type | *Aplikace* | Typ objektu zabezpečení, který může být "User", "App" nebo "Group"|

## <a name="next-steps"></a>Další kroky

* [Ingestování dat pomocí knihovny Pythonu v Azure Průzkumník dat](python-ingest-data.md)
