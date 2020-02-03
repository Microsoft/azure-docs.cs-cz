---
title: Přidání objektů zabezpečení clusteru pro Azure Průzkumník dat pomocíC#
description: V tomto článku se dozvíte, jak přidat objekty zabezpečení clusteru pro Azure Průzkumník dat pomocí C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: e6c3970890dfe2c669dee1acf631e9dd45ab1085
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965057"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-c"></a>Přidání objektů zabezpečení clusteru pro Azure Průzkumník dat pomocíC#

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Šablona Azure Resource Manageru](cluster-principal-resource-manager.md)

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. V tomto článku přidáte objekty zabezpečení clusteru pro Azure Průzkumník dat pomocí C#.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvořte cluster](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Nainstalovat C# NuGet

* Nainstalujte [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Nainstalujte [Microsoft. REST. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) pro ověřování.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Přidat objekt zabezpečení clusteru

Následující příklad ukazuje, jak programově přidat objekt clusteru.

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
//The cluster that is created as part of the Prerequisites
var clusterName = "mykustocluster";
string principalAssignmentName = "clusterPrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "AllDatabasesAdmin";//AllDatabasesAdmin or AllDatabasesViewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var clusterPrincipalAssignment = new ClusterPrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.ClusterPrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, principalAssignmentName, clusterPrincipalAssignment);
```

|**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
|---|---|---|
| tenantId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | Vaše ID tenanta. Označuje se také jako ID adresáře.|
| subscriptionId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID předplatného, které používáte pro vytváření prostředků.|
| clientId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| clientSecret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, který má přístup k prostředkům ve vašem tenantovi. |
| resourceGroupName | *testrg* | Název skupiny prostředků, která obsahuje váš cluster.|
| clusterName | *mykustocluster* | Název vašeho clusteru.|
| principalAssignmentName | *clusterPrincipalAssignment1* | Název prostředku objektu zabezpečení clusteru.|
| principalId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID objektu zabezpečení, které může být e-mailem uživatele, ID aplikace nebo názvem skupiny zabezpečení.|
| roles | *AllDatabasesAdmin* | Role objektu zabezpečení clusteru, který může být "AllDatabasesAdmin' nebo" AllDatabasesViewer ".|
| tenantIdForPrincipal | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID tenanta objektu zabezpečení|
| principalType | *Aplikace* | Typ objektu zabezpečení, který může být "User", "App" nebo "Group"|

## <a name="next-steps"></a>Další kroky

* [Přidat objekty zabezpečení databáze](database-principal-csharp.md)
