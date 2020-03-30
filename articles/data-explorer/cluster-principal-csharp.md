---
title: 'Přidání objektů zabezpečení clusteru pro Azure Data Explorer pomocí C #'
description: V tomto článku se dozvíte, jak přidat objekty zabezpečení clusteru pro Azure Data Explorer pomocí C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: e6c3970890dfe2c669dee1acf631e9dd45ab1085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965057"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-c"></a>Přidání objektů zabezpečení clusteru pro Azure Data Explorer pomocí C #

> [!div class="op_single_selector"]
> * [C #](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Šablona Azure Resource Manageru](cluster-principal-resource-manager.md)

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. V tomto článku přidáte objekty zabezpečení clusteru pro Azure Data Explorer pomocí C#.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovaný Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvořte cluster](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Instalace jazyka C# NuGet

* Nainstalujte [soubor Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* K ověřování nainstalujte [microsoft.rest.clientruntime.Azure.Authentication.](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Přidání objektu zabezpečení clusteru

Následující příklad ukazuje, jak přidat hlavní objekt clusteru programově.

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
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Vaše tenantské ID. Označuje se také jako ID adresáře.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID předplatného, které používáte pro vytvoření prostředků.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| clientSecret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, která může přistupovat k prostředkům ve vašem tenantovi. |
| resourceGroupName | *testrg* | Název skupiny prostředků obsahující váš cluster.|
| clusterName | *mykustocluster* | Název clusteru.|
| název_příkazu principalAssignmentName | *clusterPrincipalAssignment1* | Název hlavního prostředku clusteru.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID objektu zabezpečení, kterým může být e-mail uživatele, ID aplikace nebo název skupiny zabezpečení.|
| role | *AllDatabasesAdmin* | Role objektu zabezpečení clusteru, který může být "AllDatabasesAdmin" nebo AllDatabasesViewer.|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID klienta hlavního povinného.|
| principalType | *Aplikace* | Typ objektu zabezpečení, který může být "Uživatel", "App" nebo "Skupina"|

## <a name="next-steps"></a>Další kroky

* [Přidání objektů zabezpečení databáze](database-principal-csharp.md)
