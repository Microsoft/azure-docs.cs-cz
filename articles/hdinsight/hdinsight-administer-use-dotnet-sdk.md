---
title: Správa clusterů Apache Hadoop ve službě HDInsight pomocí .NET SDK – Azure
description: Naučte se provádět úlohy správy pro Apache Hadoop clustery v HDInsight pomocí sady HDInsight .NET SDK.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-csharp
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 367b47e8dbe0e1ee56fea620d97109631ba87a06
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92484878"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Správa clusterů Apache Hadoop ve službě HDInsight pomocí sady .NET SDK

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Naučte se spravovat clustery HDInsight pomocí [sady HDInsight.NET SDK](/dotnet/api/overview/azure/hdinsight).

**Požadavky**

Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Připojení k Azure HDInsight

Potřebujete následující balíčky NuGet:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Následující ukázka kódu ukazuje, jak se připojit k Azure předtím, než budete moct spravovat clustery HDInsight v rámci vašeho předplatného Azure.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

Při spuštění tohoto programu se zobrazí výzva.  Pokud nechcete, aby se zobrazila výzva, přečtěte si téma [Vytvoření aplikací .NET HDInsight, které neumožňují interaktivní ověřování](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


## <a name="list-clusters"></a>Výpis clusterů

Následující fragment kódu obsahuje seznam clusterů a některé vlastnosti:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Odstranění clusterů

Pomocí následujícího fragmentu kódu můžete cluster synchronně nebo asynchronně odstranit: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Škálování clusterů

Funkce škálování clusteru umožňuje změnit počet pracovních uzlů používaných clusterem, který běží ve službě Azure HDInsight, aniž by bylo nutné cluster znovu vytvořit.

> [!NOTE]  
> Podporují se jenom clustery se službou HDInsight verze 3.1.3 nebo vyšší. Pokud si nejste jistí, jakou verzi clusteru máte, můžete na stránce vlastností zkontrolovat.  Viz [seznam a zobrazení clusterů](hdinsight-administer-use-portal-linux.md#showClusters).

Dopad změny počtu datových uzlů pro každý typ clusteru podporovaný službou HDInsight:

* Apache Hadoop
  
    Můžete bez problémů zvýšit počet pracovních uzlů v clusteru Hadoop, který je spuštěný, aniž by to ovlivnilo nedokončené nebo spuštěné úlohy. Nové úlohy je možné odeslat i v průběhu operace. Selhání operace škálování se řádným způsobem zpracovávají, aby byl cluster vždycky ponechán ve funkčním stavu.
  
    Pokud je cluster Hadoop škálované dolů snížením počtu datových uzlů, některé ze služeb v clusteru se restartují. To způsobí, že všechny spuštěné a nedokončené úlohy selžou při dokončení operace škálování. Po dokončení operace ale můžete úlohy znovu odeslat.
* Apache HBase
  
    Bez problémů můžete přidat nebo odebrat uzly do clusteru HBA v době, kdy je spuštěný. Regionální servery se automaticky vyrovnávají během několika minut od dokončení operace škálování. Můžete ale také ručně vyrovnávat regionální servery, a to přihlášením do hlavnímu uzlu clusteru a spuštěním následujících příkazů z okna příkazového řádku:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    Můžete hladce přidávat nebo odebírat datové uzly do clusteru s více podsystémy, když je spuštěný. Po úspěšném dokončení operace škálování ale budete muset topologii znovu vyvážit.
  
    Nové Vyrovnávání je možné dosáhnout dvěma způsoby:
  
  * Webové uživatelské rozhraní pro vyplavení
  * Nástroj rozhraní příkazového řádku (CLI)
    
    Další podrobnosti najdete v [dokumentaci k Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .
    
    Webové uživatelské rozhraní pro zaplavení je k dispozici v clusteru HDInsight:
    
    ![Rebilance škálování ve službě HDInsight](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Tady je příklad, jak pomocí příkazu CLI znovu vyrovnávat topologii zatížení:
    

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Následující fragment kódu ukazuje, jak změnit velikost clusteru synchronně nebo asynchronně:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Udělit nebo odvolat přístup

Clustery HDInsight mají následující webové služby HTTP (všechny tyto služby mají koncové body RESTful):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Ve výchozím nastavení jsou tyto služby uděleny pro přístup. Přístup můžete odvolat nebo udělit. Odvolat:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Chcete-li udělit:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> Tím, že udělíte nebo odvoláváte přístup, resetujete uživatelské jméno a heslo clusteru.

To se dá udělat taky prostřednictvím portálu. Další informace najdete v tématu [správa Apache Hadoop clusterů ve službě HDInsight pomocí Azure Portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Aktualizovat přihlašovací údaje uživatele HTTP

Stejný postup je stejný jako u udělení nebo odvolání přístupu HTTP.  Pokud byl clusteru udělen přístup HTTP, je nutné jej nejprve odvolat.  A pak udělte přístup pomocí nových přihlašovacích údajů uživatele HTTP.

## <a name="find-the-default-storage-account"></a>Najít výchozí účet úložiště

Následující fragment kódu ukazuje, jak získat výchozí název účtu úložiště a výchozí klíč účtu úložiště pro cluster.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Odeslat úlohy

**Odeslání úloh MapReduce**

Viz [spuštění ukázek MapReduce ve službě HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Odeslání Apache Hivech úloh** 

Viz [spustit Apache Hive dotazy pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Odeslání úloh Apache Sqoop**

Viz [použití Apache Sqoop se službou HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Odeslání úloh Apache Oozie**

Další informace najdete [v tématu použití Apache Oozie se systémem Hadoop k definování a spuštění pracovního postupu v HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do služby Azure Blob Storage

Viz [Nahrání dat do služby HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Viz také

* [Referenční dokumentace k sadě HDInsight .NET SDK](/dotnet/api/overview/azure/hdinsight)
* [Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Správa HDInsight pomocí rozhraní příkazového řádku][hdinsight-admin-cli]
* [Vytváření clusterů HDInsight][hdinsight-provision]
* [Nahrání dat do služby HDInsight][hdinsight-upload-data]
* [Začínáme se službou Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
