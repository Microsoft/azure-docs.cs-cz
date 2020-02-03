---
title: Přidání objektů zabezpečení databáze pro Azure Průzkumník dat pomocíC#
description: V tomto článku se dozvíte, jak přidat objekty zabezpečení databáze pro Azure Průzkumník dat pomocí C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965031"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Přidání objektů zabezpečení databáze pro Azure Průzkumník dat pomocíC#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Šablona Azure Resource Manageru](database-principal-resource-manager.md)

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. V tomto článku přidáte objekty zabezpečení databáze pro Azure Průzkumník dat pomocí C#.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvořte cluster a databázi](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Nainstalovat C# NuGet

* Nainstalujte [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Nainstalujte [Microsoft. REST. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) pro ověřování.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Přidat objekt zabezpečení databáze

Následující příklad ukazuje, jak programově přidat objekt zabezpečení databáze.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
|---|---|---|
| tenantId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | Vaše ID tenanta. Označuje se také jako ID adresáře.|
| subscriptionId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID předplatného, které používáte pro vytváření prostředků.|
| clientId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| clientSecret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, který má přístup k prostředkům ve vašem tenantovi. |
| resourceGroupName | *testrg* | Název skupiny prostředků, která obsahuje váš cluster.|
| clusterName | *mykustocluster* | Název vašeho clusteru.|
| databaseName | *mykustodatabase* | Název vaší databáze.|
| principalAssignmentName | *databasePrincipalAssignment1* | Název vašeho prostředku objektu zabezpečení databáze.|
| principalId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID objektu zabezpečení, které může být e-mailem uživatele, ID aplikace nebo názvem skupiny zabezpečení.|
| roles | *Správ* | Role objektu zabezpečení databáze, která může být "admin", "ingesta", "monitor", "User", "UnrestrictedViewers", "Viewer".|
| tenantIdForPrincipal | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID tenanta objektu zabezpečení|
| principalType | *Aplikace* | Typ objektu zabezpečení, který může být "User", "App" nebo "Group"|

## <a name="next-steps"></a>Další kroky

* [Ingestování dat pomocí knihovny uzlů Azure Průzkumník dat](node-ingest-data.md)
