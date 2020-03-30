---
title: Správa clusterů Apache Hadoop v HDInsightu pomocí sady .NET SDK – Azure
description: Naučte se provádět úlohy správy clusterů Apache Hadoop v HDInsightpomocí hdinsight .NET SDK.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 36a77d49b507d3d0158d1b4b492d0141350de50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240642"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Správa clusterů Apache Hadoop ve službě HDInsight pomocí sady .NET SDK

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Přečtěte si, jak spravovat clustery HDInsight pomocí [sady HDInsight.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).

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

Následující ukázka kódu ukazuje, jak se připojit k Azure před správou clusterů HDInsight v rámci předplatného Azure.

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

Při spuštění tohoto programu se zobrazí výzva.  Pokud nechcete zobrazit výzvu, přečtěte si téma [Vytváření neinteraktivních aplikací HDInsight .NET](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


## <a name="list-clusters"></a>Seznam clusterů

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

Následující fragment kódu slouží k synchronnímu nebo asynchronnímu odstranění clusteru: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Škálovat clustery

Funkce škálování clusteru umožňuje změnit počet pracovních uzlů používaných clusterem, který běží v Azure HDInsight, aniž byste museli cluster znovu vytvářet.

> [!NOTE]  
> Podporovány jsou pouze clustery s hdinsight verze 3.1.3 nebo vyšší. Pokud si nejste jisti verzí clusteru, můžete zkontrolovat stránku Vlastnosti.  Viz [Seznam a zobrazení clusterů](hdinsight-administer-use-portal-linux.md#showClusters).

Dopad změny počtu datových uzlů pro každý typ clusteru podporovaný hdinsight:

* Apache Hadoop
  
    Můžete bez problémů zvýšit počet pracovních uzlů v clusteru Hadoop, který je spuštěn bez dopadu na všechny čekající nebo spuštěné úlohy. Nové úlohy lze také odeslat, zatímco operace probíhá. Chyby v operaci škálování jsou řádně zpracovány tak, aby cluster je vždy ponechána ve funkčním stavu.
  
    Při škálování clusteru Hadoop snížením počtu datových uzlů, některé služby v clusteru jsou restartovány. To způsobí, že všechny spuštěné a čekající úlohy nezdaří po dokončení operace škálování. Po dokončení operace však můžete úlohy znovu odeslat.
* Apache HBase
  
    Můžete bez problémů přidávat nebo odebírat uzly do clusteru HBase, když je spuštěn. Regionální servery jsou automaticky vyváženy během několika minut po dokončení operace škálování. Regionální servery však můžete také ručně vyvážit přihlášením se do čelnolu clusteru a spuštěním následujících příkazů z okna příkazového řádku:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    Datové uzly můžete bez problémů přidávat nebo odebírat do clusteru Storm, když je spuštěn. Ale po úspěšném dokončení operace škálování, budete muset znovu vyvážit topologii.
  
    Vyvažování lze dosáhnout dvěma způsoby:
  
  * Uživatelské i uživatelské i uživatelské a uživatelské
  * Nástroj rozhraní příkazového řádku (CLI)
    
    Další podrobnosti naleznete v dokumentaci k [Apache Storm.](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)
    
    Webové uživatelské rozhraní Storm je k dispozici v clusteru HDInsight:
    
    ![Vyvážení měřítka HDInsight Storm](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Tady je příklad, jak pomocí příkazu příkazu příkazu příkazu příkazu znovu vyvážit topologii bouře:
    

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

## <a name="grantrevoke-access"></a>Udělení/odvolání přístupu

Clustery HDInsight mají následující webové služby HTTP (všechny tyto služby mají koncové body RESTful):

* ODBC
* JDBC
* Apache Ambari
* Apač Oozie
* Apache Templeton

Ve výchozím nastavení jsou tyto služby uděleny pro přístup. Přístup můžete odvolat nebo udělit. Odvolání:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Udělení:

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
> Udělením/zrušením přístupu obnovíte uživatelské jméno a heslo clusteru.

To lze provést také prostřednictvím portálu. Viz [Správa clusterů Apache Hadoop v HDInsightu pomocí portálu Azure](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Aktualizace pověření uživatele PROTOKOLU HTTP

Jedná se o stejný postup jako udělení/odvolání přístupu HTTP.  Pokud byl clusteru udělen přístup HTTP, musíte jej nejprve odvolat.  A pak udělit přístup s novými přihlašovacími údaji uživatele HTTP.

## <a name="find-the-default-storage-account"></a>Vyhledání výchozího účtu úložiště

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

Viz [Spuštění mapreduce vzorků v HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Odeslání pracovních míst Apache Hive** 

Viz [Spuštění dotazů Apache Hive pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Odeslání pracovních míst Apache Sqoop**

Viz [Použití Apache Sqoop s HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Chcete-li odeslat Apache Oozie pracovní chod**

Viz [Použití Apache Oozie s Hadoopem k definování a spuštění pracovního postupu v HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů blob Azure

Viz [Nahrání dat do služby HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Viz také

* [Referenční dokumentace hdinsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Správa clusterů Apache Hadoop v HDInsightu pomocí portálu Azure](hdinsight-administer-use-portal-linux.md)
* [Správa hdinsightu pomocí rozhraní příkazového řádku][hdinsight-admin-cli]
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
