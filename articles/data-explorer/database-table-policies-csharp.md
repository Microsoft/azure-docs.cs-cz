---
title: Vytváření zásad pomocí sady Azure Data Explorer C# SDK
description: V tomto článku se dozvíte, jak vytvořit zásady pomocí jazyka C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667294"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Vytvoření zásad databáze a tabulek pro Azure Data Explorer pomocí C #

> [!div class="op_single_selector"]
> * [C #](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. V tomto článku vytvoříte zásady databáze a tabulky pro Azure Data Explorer pomocí C#.

## <a name="prerequisites"></a>Požadavky

* Visual Studio 2019. Pokud visual studio 2019 nemáte, můžete si stáhnout a použít *bezplatnou* [komunitu Visual Studio 2019](https://www.visualstudio.com/downloads/). Nezapomeňte vybrat **vývoj Azure** během nastavení Visual Studia.
* Předplatné Azure. Pokud potřebujete, můžete si vytvořit [bezplatný účet Azure,](https://azure.microsoft.com/free/) než začnete.
* [Testovací cluster a databáze](create-cluster-database-csharp.md).
* [Testovací tabulka](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>Instalace jazyka C# NuGet

* Nainstalujte [balíček NuGet aplikace Azure Data Explorer (Kusto).](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Nainstalujte [balíček Microsoft.Azure.Kusto.Data.NETStandard NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Volitelné, pro zásady přebalovací tabulky.)
* Nainstalujte [balíček Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)pro ověření.

## <a name="authentication"></a>Ověřování
Chcete-li spustit příklady v tomto článku, budete potřebovat azure active directory (Azure AD) aplikace a instanční objekt, který může přistupovat k prostředkům. Můžete použít stejnou aplikaci Azure AD pro ověřování z [testovacího clusteru a databáze](create-cluster-database-csharp.md#authentication). Pokud chcete použít jinou aplikaci Azure AD, najdete [v tématu vytvoření aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) k vytvoření bezplatné aplikace Azure AD a přidání přiřazení role v oboru předplatného. Tento článek také `Directory (tenant) ID`ukazuje, `Application ID`jak `Client secret`získat , a . Možná budete muset přidat novou aplikaci Azure AD jako hlavní v databázi. Další informace najdete [v tématu Správa oprávnění databáze Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions).

## <a name="alter-database-retention-policy"></a>Změnit zásady uchovávání informací databáze
Nastaví zásady uchovávání informací s 10denním obdobím obnovitelného odstranění.
    
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

## <a name="alter-database-cache-policy"></a>Změnit zásadu mezipaměti databáze
Nastaví zásadu mezipaměti pro databázi. Předchozích pět dní dat bude na ssd disk clusteru.

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

## <a name="alter-table-cache-policy"></a>Změnit zásadu mezipaměti tabulky
Nastaví zásadu mezipaměti pro tabulku. Předchozích pět dní dat bude na ssd disk clusteru.

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
Přidá novou aplikaci Azure AD jako objekt zabezpečení správce pro databázi.

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
