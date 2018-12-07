---
title: Správa clusterů Apache Hadoop v HDInsight pomocí sady .NET SDK – Azure
description: Zjistěte, jak k provádění úloh správy pro clustery systému Apache Hadoop v HDInsight pomocí sady HDInsight .NET SDK.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 26aeb5641533125dcd909ae96d28c8274677cf30
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53013437"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Spravovat clustery systému Apache Hadoop v HDInsight pomocí sady .NET SDK
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Další informace o správě clusterů HDInsight pomocí [HDInsight.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).

**Požadavky**

Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Připojení k Azure HDInsight

Budete potřebovat následující balíčky NuGet:

```
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Následující příklad kódu ukazuje, jak se připojit k Azure, než budete moct spravovat clustery HDInsight v rámci vašeho předplatného Azure.

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

Při spuštění tohoto programu se zobrazí výzva.  Pokud nechcete zobrazí výzva, přečtěte si téma [vytváření aplikací .NET HDInsight jako neinteraktivní ověřování](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

## <a name="create-clusters"></a>Vytváření clusterů
Zobrazit [vytvoření linuxových clusterech v HDInsight pomocí sady .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

## <a name="list-clusters"></a>Výpis clusterů
Clustery a některé vlastnosti jsou uvedeny následující fragment kódu:

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
Pomocí následujícího fragmentu kódu můžete odstranit cluster synchronně nebo asynchronně: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Škálování clusterů
Funkce škálování clusteru umožňuje změnit počet uzlů pracovního procesu se používá cluster, který běží v Azure HDInsight bez nutnosti nového vytváření clusteru.

> [!NOTE]
> Pouze clustery HDInsight verze 3.1.3 nebo vyšší nejsou podporovány. Pokud si nejste jistí verze vašeho clusteru, můžete zkontrolovat na stránce Vlastnosti.  Zobrazit [výpisu a zobrazení clusterů](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
> 
> 

Dopad Změna počtu datových uzlů pro každý typ clusteru podporuje HDInsight:

* Apache Hadoop
  
    Bezproblémově můžete zvýšit počet pracovních uzlů v clusteru Hadoop, na kterém běží bez dopadu na všechny úlohy čekající na vyřízení nebo spuštěné. Nové úlohy můžete odeslat také když probíhá operace. Selhání v rámci operace škálování jsou zpracovány bez výpadku v tak, aby cluster zůstane vždy ve funkčním stavu.
  
    Pokud je Hadoop cluster je kapacitu vertikálně snížit snížením počtu datových uzlů, jsou restartovat některé ze služeb v clusteru. To způsobí, že všechny spuštěné a čekající úlohy selhání po dokončení operace škálování. Můžete, ale neúspěšné úlohy po dokončení operace.
* Apache HBase
  
    Bezproblémově můžete přidat nebo odebrat uzly do clusteru HBase během jejího běhu. Oblastní servery jsou automaticky rovnoměrně rozdělen do několika minut od dokončení operace škálování. Oblastní servery však můžete také ručně vyvážit změnou přihlášení k hlavnímu uzlu clusteru a spustíte tento příkaz z okna příkazového řádku:
  
    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```
* Apache Storm
  
    Bezproblémově můžete přidat nebo odebrat datových uzlů do clusteru Storm během jejího běhu. Ale po úspěšném dokončení operace škálování, budete muset vyrovnat topologie.
  
    Opětovné vyvážení lze provést dvěma způsoby:
  
  * Webové uživatelské rozhraní Storm
  * Nástroje rozhraní příkazového řádku (CLI)
    
    Najdete [dokumentaci Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) další podrobnosti.
    
    Webové uživatelské rozhraní Storm je k dispozici v clusteru HDInsight:
    
    ![Obnovení rovnováhy škálování HDInsight Storm](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Tady je příklad jak obnovit rovnováhu topologie Storm pomocí příkazu rozhraní příkazového řádku:
    
    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Následující fragment kódu ukazuje, jak změnit velikost cluster synchronně nebo asynchronně:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Udělení nebo odvolání přístupu
Clustery HDInsight mají následující webové služby HTTP (mít všechny tyto služby RESTful koncových bodů):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Tyto služby jsou ve výchozím nastavení oprávnění pro přístup. Vám může k nim odvolat/udělit přístup. Chcete-li odebrat:

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
> Pomocí udělení nebo odvolání přístupu, resetuje clusteru uživatelské jméno a heslo.
> 
> 

To můžete udělat také pomocí portálu. Zobrazit [Správa HDInsight pomocí webu Azure portal][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Aktualizace přihlašovacích údajů uživatele HTTP
Je stejným způsobem jako [HTTP udělení nebo odvolání přístupu](#grant/revoke-access).  Pokud cluster má udělen přístup protokolu HTTP, musí se nejprve odvolat.  A potom jim udělit přístup pomocí nových přihlašovacích údajů uživatele HTTP.

## <a name="find-the-default-storage-account"></a>Najít výchozí účet úložiště
Následující fragment kódu ukazuje, jak získat výchozí název účtu úložiště a výchozí klíč účtu úložiště pro cluster.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Odesílání úloh
**Odesílat úlohy Apache Hadoop MapReduce**

Zobrazit [ukázky spouštění Hadoop MapReduce Apache v HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Odesílat úlohy Apache Hive** 

Zobrazit [spouštění Apache dotazů Hive pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Odesílat úlohy Apache Pig**

Zobrazit [Apache Pig spuštění úlohy pomocí sady .NET SDK](hadoop/apache-hadoop-use-pig-dotnet-sdk.md).

**Odesílat úlohy Apache Sqoop**

Zobrazit [použití Apache Sqoop s HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Odesílat úlohy Apache Oozie**

Zobrazit [použití Apache Oozie s Hadoopem k definování a spuštění workflowu v HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů Blob v Azure
Viz [Nahrání dat do služby HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Viz také
* [Referenční dokumentace sady HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Správa HDInsight pomocí webu Azure portal][hdinsight-admin-portal]
* [Správa HDInsight pomocí rozhraní příkazového řádku][hdinsight-admin-cli]
* [Vytvoření clusterů HDInsight][hdinsight-provision]
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
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


