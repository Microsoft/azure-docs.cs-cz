---
title: Správa clusterů systému Hadoop v HDInsight pomocí .NET SDK - Azure | Microsoft Docs
description: Zjistěte, jak k provádění úloh správy pro clusterů systému Hadoop v HDInsight pomocí sady .NET SDK HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: ''
ms.assetid: fd134765-c2a0-488a-bca6-184d814d78e9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 0feb1e344d70207d48e4ef07f5b45790425727ad
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Správa clusterů systému Hadoop v HDInsight pomocí sady .NET SDK
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Zjistěte, jak Správa clusterů HDInsight pomocí [HDInsight.NET SDK](https://msdn.microsoft.com/library/mt271028.aspx).

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

Zobrazí se výzva a při spuštění tohoto programu.  Pokud nechcete zobrazí výzva, přečtěte si téma [vytvořit neinteraktivního ověřování aplikace .NET HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

## <a name="create-clusters"></a>Vytváření clusterů
V tématu [vytvořit systémem Linux clusterů v HDInsight pomocí sady .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

## <a name="list-clusters"></a>Seznam clustery
Následující fragment kódu obsahuje clustery a některé vlastnosti:

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
Pomocí následující fragment kódu můžete odstranit cluster synchronně nebo asynchronně: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Škálování clusterů
Funkce škálování clusteru umožňuje změnit počet uzlů pracovního procesu používá cluster, který běží v Azure HDInsight bez nutnosti znovu vytvořit cluster.

> [!NOTE]
> Pouze clustery s HDInsight verze 3.1.3 nebo vyšší nejsou podporovány. Pokud si nejste jistí na verzi vašeho clusteru, můžete zkontrolovat stránku vlastností.  V tématu [seznamu a zobrazit clustery](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
> 
> 

Dopad změny v počtu uzlů dat pro každý typ clusteru podporuje HDInsight:

* Hadoop
  
    Můžete bez problémů zvýšit počet uzlů pracovního procesu v clusteru Hadoop, který běží bez dopadu na všechny úlohy čekající na vyřízení nebo spuštěné. Nové úlohy můžete také odeslány, když probíhá operace. Selhání v rámci operace škálování pohodlné zpracování tak, aby cluster zůstane vždy ve funkčním stavu.
  
    Pokud se Hadoop cluster měřítko snížením počtu uzlů data, některé služby v clusteru restartovat. To způsobí, že všechny spuštěné a čeká se na úlohy selhání po dokončení operace škálování. Můžete, ale odešlete znovu úloh po dokončení operace.
* HBase
  
    Bezproblémově můžete přidávat nebo odebírat uzly do clusteru HBase, když je spuštěná. Místní servery jsou automaticky vyváženy během několika minut po dokončení operace škálování. Protokolování do headnode clusteru a spuštěním následujících příkazů z okna příkazového řádku však můžete také ručně vyvážit místní servery:
  
    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```
* Storm
  
    Můžete bezproblémově přidávat nebo odebírat uzly dat do clusteru Storm, když je spuštěná. Ale po úspěšném dokončení operace škálování, budete muset znovu vyvážit topologii.
  
    Vyrovnává lze dosáhnout dvěma způsoby:
  
  * Storm webového uživatelského rozhraní
  * Nástroj pro rozhraní příkazového řádku (CLI)
    
    Podrobnosti najdete [dokumentaci Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) další podrobnosti.
    
    Uživatelské rozhraní Storm webu je k dispozici v clusteru HDInsight:
    
    ![Obnovte rovnováhu škálování Storm v HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Tady je příklad jak znovu vyvážit topologie Storm pomocí rozhraní příkazového řádku příkaz:
    
    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Následující fragment kódu ukazuje, jak změnit velikost clusteru s podporou synchronně nebo asynchronně:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Udělení nebo odvolání přístupu
Clustery HDInsight mají následující webové služby HTTP (všechny tyto služby mají RESTful koncových bodů):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Ve výchozím nastavení jsou tyto služby oprávnění pro přístup. Vám může odvolání nebo udělit přístup. K odvolání:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Udělit:

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
> Pomocí udělení nebo odvolání přístupu, obnoví clusteru uživatelské jméno a heslo.
> 
> 

To lze provést také prostřednictvím portálu. V tématu [spravovat HDInsight pomocí portálu Azure][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Aktualizovat pověření uživatele HTTP
Je stejným způsobem jako [HTTP udělení nebo odvolání přístupu](#grant/revoke-access). Pokud cluster byl přidělen přístup protokolu HTTP, musí se nejdřív odvolat.  A pak udělují přístup s novými pověřeními uživatele HTTP.

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
**K odesílání úloh MapReduce**

V tématu [ukázky spustit Hadoop MapReduce v HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**K odeslání úloh Hive** 

V tématu [spouštění dotazů Hive pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**K odeslání úlohy Pig**

V tématu [úlohy spustit Pig pomocí sady .NET SDK](hadoop/apache-hadoop-use-pig-dotnet-sdk.md).

**K odesílání úloh Sqoop**

V tématu [použití nástroje Sqoop se HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**K odesílání úloh Oozie**

V tématu [Oozie použití se systémem Hadoop k definování a spuštění workflowu v HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů Blob v Azure
Viz [Nahrání dat do služby HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Viz také
* [HDInsight .NET SDK referenční dokumentaci k nástroji](https://msdn.microsoft.com/library/mt271028.aspx)
* [Spravovat HDInsight pomocí portálu Azure][hdinsight-admin-portal]
* [Spravovat HDInsight pomocí rozhraní příkazového řádku][hdinsight-admin-cli]
* [Vytvoření clusterů HDInsight][hdinsight-provision]
* [Nahrání dat do služby HDInsight][hdinsight-upload-data]
* [Začínáme se službou Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

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


