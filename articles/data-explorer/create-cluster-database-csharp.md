---
title: Vytvoření clusteru a databáze Azure Průzkumník dat pomocíC#
description: Naučte se vytvořit cluster a databázi Azure Průzkumník dat pomocíC#
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 2d800dc401b0d85b26a71817a1a70d66539203ae
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902123"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Vytvoření clusteru a databáze Azure Průzkumník dat pomocíC#

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Šablona Azure Resource Manageru](create-cluster-database-resource-manager.md)

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do databáze, abyste na ni mohli spouštět dotazy. V tomto článku vytvoříte cluster a databázi pomocí nástroje C#.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="install-c-nuget"></a>Nainstalovat C# NuGet

* Nainstalujte [balíček NuGet pro Azure Průzkumník dat (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Nainstalujte [balíček NuGet Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pro ověřování.

## <a name="authentication"></a>Ověření
Pro spuštění příkladů v tomto článku potřebujeme aplikaci služby Azure AD a instanční objekt, který má přístup k prostředkům. Pokud chcete vytvořit bezplatnou aplikaci Azure AD a přidat přiřazení role v oboru předplatného, podívejte se na [vytvořit aplikaci Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) . Také ukazuje, jak získat `Directory (tenant) ID`, `Application ID`a `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Azure Průzkumník dat

1. Vytvořte cluster pomocí následujícího kódu:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
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
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | clusterName | *mykustocluster* | Požadovaný název clusteru.|
   | skuName | *Standard_D13_v2* | SKU, které bude použito pro váš cluster. |
   | Basic | *Standard* | Úroveň SKU. |
   | capacity | *Automatické* | Počet instancí clusteru |
   | resourceGroupName | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |

    > [!NOTE]
    > **Vytvoření clusteru** je dlouhodobá operace, takže se doporučujeme místo CreateOrUpdate použít CreateOrUpdateAsync. 

1. Spusťte následující příkaz a ověřte, zda byl cluster úspěšně vytvořen:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Pokud výsledek obsahuje `ProvisioningState` s hodnotou `Succeeded`, cluster se úspěšně vytvořil.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v clusteru Azure Průzkumník dat

1. Vytvořte databázi pomocí následujícího kódu:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

        [!NOTE]
        If you are using C# version 2.0.0 or below, use Database instead of ReadWriteDatabase.

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | clusterName | *mykustocluster* | Název clusteru, ve kterém se databáze vytvoří.|
   | databaseName | *mykustodatabase* | Název vaší databáze.|
   | resourceGroupName | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |
   | softDeletePeriod | *3650:00:00:00* | Doba, po kterou budou data uchována k dispozici pro dotaz. |
   | hotCachePeriod | *3650:00:00:00* | Doba, po kterou budou data uchována v mezipaměti. |

2. Spuštěním následujícího příkazu zobrazíte databázi, kterou jste vytvořili:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

Teď máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud plánujete postupovat podle našich dalších článků, ponechejte prostředky, které jste vytvořili.
* Pokud chcete vyčistit prostředky, odstraňte cluster. Když odstraníte cluster, odstraní se i všechny jeho databáze. Pomocí následujícího příkazu odstraňte cluster:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Další kroky

* [Ingestování dat pomocí sady Azure Průzkumník dat .NET Standard SDK (Preview)](net-standard-ingest-data.md)
