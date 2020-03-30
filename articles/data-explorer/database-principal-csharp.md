---
title: 'Přidání databázových objektů pro Azure Data Explorer pomocí C #'
description: V tomto článku se dozvíte, jak přidat objekty zabezpečení databáze pro Azure Data Explorer pomocí C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965031"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Přidání databázových objektů pro Azure Data Explorer pomocí C #

> [!div class="op_single_selector"]
> * [C #](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Šablona Azure Resource Manageru](database-principal-resource-manager.md)

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. V tomto článku přidáte objekty zabezpečení databáze pro Azure Data Explorer pomocí C#.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovaný Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvořte cluster a databázi](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Instalace jazyka C# NuGet

* Nainstalujte [soubor Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* K ověřování nainstalujte [microsoft.rest.clientruntime.Azure.Authentication.](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Přidání objektu zabezpečení databáze

Následující příklad ukazuje, jak přidat objekt zabezpečení databáze programově.

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
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Vaše tenantské ID. Označuje se také jako ID adresáře.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID předplatného, které používáte pro vytvoření prostředků.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| clientSecret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, která může přistupovat k prostředkům ve vašem tenantovi. |
| resourceGroupName | *testrg* | Název skupiny prostředků obsahující váš cluster.|
| clusterName | *mykustocluster* | Název clusteru.|
| Databasename | *mykustodatabáze* | Název databáze.|
| název_příkazu principalAssignmentName | *databasePrincipalAssignment1* | Název hlavního prostředku databáze.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID objektu zabezpečení, kterým může být e-mail uživatele, ID aplikace nebo název skupiny zabezpečení.|
| role | *Správce* | Role objektu zabezpečení databáze, který může být 'Admin', 'Ingestor', 'Monitor', 'Uživatel', 'NeomezenýDiváci', 'Prohlížeč'.|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID klienta hlavního povinného.|
| principalType | *Aplikace* | Typ objektu zabezpečení, který může být "Uživatel", "App" nebo "Skupina"|

## <a name="next-steps"></a>Další kroky

* [Ingestování dat pomocí knihovny uzlů Průzkumníka dat Azure](node-ingest-data.md)
