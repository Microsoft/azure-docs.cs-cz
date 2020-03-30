---
title: 'Vytvoření clusteru Průzkumníka dat Azure & DB pomocí C #'
description: 'Zjistěte, jak vytvořit cluster a databázi Průzkumníka dat Azure pomocí c #'
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 0c32d438ac8551f061343edb747e9fc035b498e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246406"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Vytvoření clusteru a databáze Průzkumníka dat Azure pomocí C #

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Šablona Azure Resource Manageru](create-cluster-database-resource-manager.md)

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Data Explorer, musíte nejdříve vytvořit cluster a v něm vytvořit jednu nebo více databází. Do databáze potom ingestujete (načtete) data, abyste se na ně mohli dotazovat spouštěním dotazů. V tomto článku vytvoříte cluster a databázi pomocí jazyka C#.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovaný Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

## <a name="authentication"></a>Ověřování
Pro spuštění příklady v tomto článku potřebujeme azure ad aplikace a instanční objekt, který může získat přístup k prostředkům. Zaškrtněte [vytvořit aplikaci Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) k vytvoření bezplatné aplikace Azure AD a přidejte přiřazení role v oboru předplatného. To také `Directory (tenant) ID`ukazuje, jak `Application ID`získat `Client Secret`, a .

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Průzkumníka dat Azure

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
   | skuName | *Standard_D13_v2* | Skladová položka, která bude použita pro váš cluster. |
   | tier | *Standard* | Úroveň Skladové položky. |
   | capacity | *Číslo* | Počet instancí clusteru. |
   | resourceGroupName | *testrg* | Název skupiny prostředků, kde bude vytvořen cluster. |

    > [!NOTE]
    > **Vytvoření clusteru** je dlouho běžící operace, takže je vysoce doporučeno použít CreateOrUpdateAsync, namísto CreateOrUpdate. 

1. Spuštěním následujícího příkazu zkontrolujte, zda byl cluster úspěšně vytvořen:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Pokud výsledek `ProvisioningState` obsahuje `Succeeded` s hodnotou, byl cluster úspěšně vytvořen.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v clusteru Průzkumníka dat Azure

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
   | clusterName | *mykustocluster* | Název clusteru, kde bude databáze vytvořena.|
   | Databasename | *mykustodatabáze* | Název databáze.|
   | resourceGroupName | *testrg* | Název skupiny prostředků, kde bude vytvořen cluster. |
   | softDeletePeriod | *3650:00:00:00* | Doba, po kterou budou data k dispozici pro dotaz. |
   | hotCachePeriod | *3650:00:00:00* | Doba, po kterou budou data uložena v mezipaměti. |

2. Chcete-li zobrazit databázi, kterou jste vytvořili, spusťte následující příkaz:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

Nyní máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud máte v plánu sledovat naše další články, uchovávejte zdroje, které jste vytvořili.
* Chcete-li vyčistit prostředky, odstraňte cluster. Když odstraníte cluster, odstraní také všechny databáze v něm. K odstranění clusteru použijte následující příkaz:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Další kroky

* [Ingestování dat pomocí azure data exploreru .NET Standard SDK (Preview)](net-standard-ingest-data.md)
