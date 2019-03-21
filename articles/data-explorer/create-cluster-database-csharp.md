---
title: 'Rychlý start: Vytvoření clusteru Průzkumník dat Azure a databáze s použitímC#'
description: Zjistěte, jak vytvořit cluster Průzkumník dat Azure a databáze služby pomocíC#
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: d861eba6ce905ccaf0d08a08cdd9998a199889da
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287504"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Vytvoření clusteru Průzkumník dat Azure a databáze s použitímC#

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Tento rychlý start popisuje způsob tvorby clusteru Průzkumník dat Azure a databáze služby s použitím C#.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

- K dokončení tohoto rychlého startu potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="install-c-nuget"></a>Nainstalujte C# nuget

- Budete potřebovat balíček nuget pro Průzkumník dat Azure (Kusto), můžete najít zde Nuget: https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/
- Budete také potřebovat nuget Microsoft.IdentityModel.Clients.ActiveDirectory pro ověřování https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/


## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Průzkumník dat Azure

1. Vytvoření clusteru pomocí následujícího kódu:

    ```C#-interactive
    string resourceGroupName = "testrg";    
    string clusterName = "mykustocluster";
    string location = "Central US";
    AzureSku sku = new AzureSku("D13_v2", 5);
    Cluster cluster = new Cluster(location, sku);
    
    ar authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential).Result;
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
     
    KustoManagementClient KustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    KustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | clusterName | *mykustocluster* | Požadovaný název vašeho clusteru.|
   | SKU | *D13_v2* | Skladová položka, která se použije pro váš cluster. |
   | resourceGroupName | *testrg* | Název skupiny prostředků, ve kterém se cluster vytvoří. |

    Existují další volitelné parametry, které můžete použít, jako je například kapacita clusteru.
    
    Nastavení "pověření" vašich přihlašovacích údajů (Další informace najdete v části https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet )

2. Spusťte následující příkaz a zkontrolujte, zda byl úspěšně vytvořen cluster:

    ```C#-interactive
    KustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Pokud výsledek obsahuje `ProvisioningState` s `Succeeded` hodnotu, pak clusteru byl úspěšně vytvořen.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v Průzkumníku dat Azure clusteru

1. Vytvoření databáze pomocí následujícího kódu:

    ```c#-interactive
    TimeSpan hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    TimeSpan softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    string databaseName = "mykustodatabase";
    Database database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);
    
    KustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | clusterName | *mykustocluster* | Název clusteru s novou databází.|
   | databaseName | *mykustodatabase* | Název databáze.|
   | resourceGroupName | *testrg* | Název skupiny prostředků, ve kterém se cluster vytvoří. |
   | softDeletePeriod | *3650:00:00:00* | Množství času, které data zůstanou k dispozici pro dotazy. |
   | hotCachePeriod | *3650:00:00:00* | Množství času, které data zůstanou v mezipaměti. |

2. Spuštěním následujícího příkazu zobrazte databázi, kterou jste vytvořili:

    ```c#-interactive
    KustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Teď máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud plánujete postupovat podle našich dalších rychlých startů a kurzů, vytvořené prostředky zachovejte.
* Pokud chcete vyčistit prostředky, cluster odstraňte. Po odstranění clusteru se odstraní také všechny databáze v ní. Pomocí následujícího příkazu odstraňte cluster:

    ```C#-interactive
    KustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Příjem dat s využitím dat Explorer .NET Standard SDK služby Azure (Preview)](net-standard-ingest-data.md)
