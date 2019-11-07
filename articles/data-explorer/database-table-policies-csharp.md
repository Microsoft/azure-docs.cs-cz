---
title: Vytvoření zásad pomocí sady Azure Průzkumník dat C# SDK
description: V tomto článku se naučíte, jak vytvářet zásady pomocí C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 8a5ea692bfdec7f676a80cc670f686af66152e6f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606607"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Vytvoření zásad databáze a tabulek pro Azure Průzkumník dat pomocíC#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. V tomto článku vytvoříte zásady databáze a tabulek pro Azure Průzkumník dat pomocí C#.

## <a name="prerequisites"></a>Požadavky

* Visual Studio 2019. Pokud nemáte Visual Studio 2019, můžete si stáhnout a použít *bezplatnou* [visual Studio Community 2019](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio vybrat možnost **vývoj pro Azure** .

* Předplatné Azure. Pokud potřebujete, můžete si před zahájením vytvořit [bezplatný účet Azure](https://azure.microsoft.com/free/) .

* [Testovací cluster a databáze](create-cluster-database-csharp.md).

* [Testovací tabulka](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

## <a name="install-c-nuget"></a>Nainstalovat C# NuGet

* Nainstalujte [balíček NuGet pro Azure Průzkumník dat (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Nainstalujte [balíček NuGet Microsoft. Azure. Kusto. data. NETStandard](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Volitelné, pro změnu zásad tabulky.)

* Pro ověřování nainstalujte [balíček NuGet Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

## <a name="authentication"></a>Ověřování
Chcete-li spustit příklady v tomto článku, potřebujete aplikaci Azure Active Directory (Azure AD) a instanční objekt, který má přístup k prostředkům. Můžete použít stejnou aplikaci Azure AD pro ověřování z [testovacího clusteru a databáze](create-cluster-database-csharp.md#authentication). Pokud chcete použít jinou aplikaci Azure AD, přečtěte si téma [Vytvoření aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) pro vytvoření bezplatné aplikace Azure AD a přidání přiřazení role v oboru předplatného. Tento článek také ukazuje, jak získat `Directory (tenant) ID`, `Application ID`a `Client secret`. Může být nutné přidat novou aplikaci Azure AD jako objekt zabezpečení v databázi. Další informace najdete v tématu [Správa oprávnění pro databázi Azure Průzkumník dat](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions).

## <a name="alter-database-retention-policy"></a>Změnit zásady uchovávání informací databáze
Nastaví zásady uchovávání informací s dobou tlumeného odstranění 10 dní.
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Změnit zásady mezipaměti databáze
Nastaví zásady mezipaměti pro databázi. Předchozí pět dní dat bude v clusteru SSD.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>Změnit zásady mezipaměti tabulek
Nastaví zásady mezipaměti pro tabulku. Předchozí pět dní dat bude v clusteru SSD.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>Přidání nového objektu zabezpečení pro databázi
Přidá novou aplikaci Azure AD jako hlavní správce databáze.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>Další kroky

* [Další informace o zásadách pro databáze a tabulky](https://docs.microsoft.com/azure/kusto/management/policies)
