---
title: Vytvoření clusteru a databáze Azure Průzkumník dat pomocíC#
description: Naučte se vytvořit cluster a databázi Azure Průzkumník dat pomocíC#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 4a3f37c232fcd7a0fcbdac051ed36916ef5c2868
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326670"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Vytvoření clusteru a databáze Azure Průzkumník dat pomocíC#

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Šablona ARM](create-cluster-database-resource-manager.md)

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do databáze, abyste na ni mohli spouštět dotazy. V tomto článku vytvoříte cluster a databázi pomocí nástroje C#.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="install-c-nuget"></a>Nainstalovat C# NuGet

1. Nainstalujte [balíček NuGet pro Azure Průzkumník dat (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

1. Nainstalujte [balíček NuGet Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pro ověřování.

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Azure Průzkumník dat

1. Vytvořte cluster pomocí následujícího kódu:

    ```csharp
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var sku = new AzureSku("D13_v2", 5);
    var cluster = new Cluster(location, sku);

    var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    kustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | clusterName | *mykustocluster* | Požadovaný název clusteru.|
   | SKU | *D13_v2* | SKU, které bude použito pro váš cluster. |
   | resourceGroupName | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |

    Existují další nepovinné parametry, které můžete použít, například kapacitu clusteru.

1. Nastavení [přihlašovacích údajů](https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)

1. Spusťte následující příkaz a ověřte, zda byl cluster úspěšně vytvořen:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Pokud výsledek obsahuje `ProvisioningState` `Succeeded` hodnotu, cluster se úspěšně vytvořil.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v clusteru Azure Průzkumník dat

1. Vytvořte databázi pomocí následujícího kódu:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    kustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | clusterName | *mykustocluster* | Název clusteru, ve kterém se databáze vytvoří.|
   | databaseName | *mykustodatabase* | Název vaší databáze.|
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
