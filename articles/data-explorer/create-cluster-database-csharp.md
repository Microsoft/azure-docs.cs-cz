---
title: Vytvoření clusteru a databáze Azure Průzkumník dat pomocíC#
description: Naučte se vytvořit cluster a databázi Azure Průzkumník dat pomocíC#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 35f11ee9bce4dc7c68e12749f69d2f2e4253d4bc
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996243"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Vytvoření clusteru a databáze Azure Průzkumník dat pomocíC#

> [!div class="op_single_selector"]
> * [Bran](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [Prostředí](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Šablona ARM](create-cluster-database-resource-manager.md)

Azure Průzkumník dat je rychlá a plně spravovaná služba analýzy dat pro analýzu velkých objemů datových proudů z aplikací, webů, zařízení IoT a dalších prostředků v reálném čase. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do databáze, abyste na ni mohli spouštět dotazy. V tomto článku vytvoříte cluster a databázi pomocí nástroje C#.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit **vývoj pro Azure** .

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="install-c-nuget"></a>Nainstalovat C# NuGet

1. Nainstalujte [balíček NuGet pro Azure Průzkumník dat (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

1. Nainstalujte [balíček NuGet Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pro ověřování.

## <a name="authentication"></a>Ověřování
Pro spuštění příkladů v tomto článku potřebujeme aplikaci služby Azure AD a instanční objekt, který má přístup k prostředkům. Pokud chcete vytvořit bezplatnou aplikaci Azure AD a přidat přiřazení role v oboru předplatného, podívejte se na [vytvořit aplikaci Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) . Také ukazuje, jak získat `Directory (tenant) ID`, `Application ID` a `Client Secret`.

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

   |**Nastavením** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | clusterName | *mykustocluster* | Požadovaný název clusteru.|
   | skuName | *Standard_D13_v2* | SKU, které bude použito pro váš cluster. |
   | vrstva | *Standardní* | Úroveň SKU. |
   | klíčivost | *Automatické* | Počet instancí clusteru |
   | resourceGroupName | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |

    > [!NOTE]
    > **Vytvoření clusteru** je dlouhodobá operace, takže se doporučujeme místo CreateOrUpdate použít CreateOrUpdateAsync. 

1. Spusťte následující příkaz a ověřte, zda byl cluster úspěšně vytvořen:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Pokud výsledek obsahuje `ProvisioningState` s hodnotou `Succeeded`, cluster byl úspěšně vytvořen.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v clusteru Azure Průzkumník dat

1. Vytvořte databázi pomocí následujícího kódu:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Nastavením** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | clusterName | *mykustocluster* | Název clusteru, ve kterém se databáze vytvoří.|
   | Databáze | *mykustodatabase* | Název vaší databáze.|
   | resourceGroupName | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |
   | softDeletePeriod | *3650:00:00:00* | Doba, po kterou budou data uchována k dispozici pro dotaz. |
   | hotCachePeriod | *3650:00:00:00* | Doba, po kterou budou data uchována v mezipaměti. |

2. Spuštěním následujícího příkazu zobrazíte databázi, kterou jste vytvořili:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
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
