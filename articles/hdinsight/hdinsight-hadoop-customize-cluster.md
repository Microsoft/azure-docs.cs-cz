---
title: Přizpůsobení clusterů HDInsight pomocí skriptových akcí – Azure
description: Informace o přizpůsobení clusterů HDInsight pomocí skriptových akcí.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: ad59decab7233c74e13468b0cf0b11fdb5485d07
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53722347"
---
# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Přizpůsobení clusterů HDInsight se systémem Windows pomocí akce skriptu
**Skript akce** můžete použít k vyvolání [vlastní skripty](hdinsight-hadoop-script-actions.md) během procesu vytváření clusteru pro instalaci dalšího softwaru v clusteru.

Informace v tomto článku je specifická pro clustery HDInsight se systémem Windows. Pro clustery založené na Linuxu najdete v článku [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

> [!IMPORTANT]  
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Clustery HDInsight je možné přizpůsobit v různých způsobů, jako je například včetně další účty Azure Storage, změna [Apache Hadoop](https://hadoop.apache.org/) konfiguračních souborů (core-site.xml, hive-site.xml, atd.) nebo přidání sdílených knihovny (například [Apache Hive](https://hive.apache.org/), [Apache Oozie](https://oozie.apache.org/)) do společného umístění v clusteru. Tato přizpůsobení lze provést prostřednictvím Azure Powershellu, Azure HDInsight .NET SDK nebo na webu Azure portal. Další informace najdete v tématu [vytvořit Apache Hadoop clusterů v HDInsight][hdinsight-provision-cluster].

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Akce skriptu v procesu vytváření clusteru
Akce skriptu se používá pouze při clusteru se právě vytváří. Následující diagram znázorňuje při spuštění akce skriptu během procesu vytváření:

![Přizpůsobení clusteru HDInsight a fáze při vytváření clusteru][img-hdi-cluster-states]

Když je spuštěný skript, přejde do clusteru **ClusterCustomization** fázi. V této fázi skript je spuštěn v rámci účtu správce systému a paralelně na všechny zadané uzly v clusteru a poskytuje oprávnění správce na uzlech.

> [!NOTE]  
> Vzhledem k tomu, že máte oprávnění správce na uzlech clusteru během **ClusterCustomization** fáze, můžete použít skript k provedení operace, jako je zastavení a spuštění služeb, včetně služby související s Hadoop. Jako součást skriptu, takže je musíte ujistit, že služby Ambari a další služby související s Hadoop zprovoznění před dokončením spuštění skriptu. Tyto služby jsou nezbytné pro úspěšně zjistit stav a stav clusteru při jeho vytváření. Pokud změníte žádnou konfiguraci v clusteru, který má vliv na tyto služby, musíte použít pomocných funkcí, které jsou k dispozici. Další informace o pomocných funkcí najdete v tématu [vývoj skriptových akcí skriptů pro HDInsight][hdinsight-write-script].
>
>

Výstup a protokoly chyb pro skript jsou uloženy v výchozí účet úložiště, který jste zadali pro cluster. Protokoly se ukládají v tabulce s názvem **u < \cluster-name-fragment >< \time-stamp > setuplog**. Jedná se o agregovat protokoly ze skriptu, spusťte na všech uzlech (hlavní uzel a pracovní uzly) v clusteru.

Každý cluster může přijmout více akcí skriptů, které jsou vyvolány v pořadí, ve kterém jsou uvedeny. Skript můžete spustit na hlavní uzel a pracovní uzly.

HDInsight poskytuje několik skriptů v clusterech HDInsight nainstalovat následující komponenty:

| Název | Skript |
| --- | --- |
| **Instalace Apache Sparku** | `https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1`. Zobrazit [instalace a použití Apache Spark na HDInsight clusterů][hdinsight-install-spark]. |
| **Nainstalovat jazyk R** | `https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1`. Zobrazit [instalace a použití R na clusterech HDInsight](r-server/r-server-hdinsight-manage.md#install-additional-r-packages-on-the-cluster). |
| **Instalace Apache Solr** | `https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1`. Zobrazit [instalace a použití Apache Solr na HDInsight clusterů](hdinsight-hadoop-solr-install.md). |
| **Instalace Apache Giraph** | `https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1`. Zobrazit [instalace a použití Apache Giraph na HDInsight clusterů](hdinsight-hadoop-giraph-install.md). |
| **Přednačíst knihovny Apache Hive** | `https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1`. Zobrazit [knihovny přidat Apache Hive v clusterech HDInsight](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="call-scripts-using-the-azure-portal"></a>Volání skriptů pomocí webu Azure portal
**Na webu Azure Portal**

1. Začněte s vytvářením clusteru, jak je popsáno v [vytvořit Apache Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
2. V části volitelná konfigurace pro **akcí skriptů** okno, klikněte na tlačítko **přidání akce skriptu** k zajištění podrobných informací o akce skriptu, jak je znázorněno níže:

    ![Přizpůsobení clusteru pomocí akce skriptu](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "přizpůsobení clusteru pomocí akce skriptu")

    <table border='1'>
        <tr><th>Vlastnost</th><th>Hodnota</th></tr>
        <tr><td>Název</td>
            <td>Zadejte název akce skriptu.</td></tr>
        <tr><td>Identifikátor URI skriptu</td>
            <td>Zadejte identifikátor URI pro skript, který je vyvolán přizpůsobení clusteru. s</td></tr>
        <tr><td>HEAD/pracovního procesu</td>
            <td>Zadat uzly (**Head** nebo **pracovního procesu**) podle kterého se spouští skript vlastního nastavení.</b>.
        <tr><td>Parametry</td>
            <td>Zadejte parametry, pokud je to nutné skript.</td></tr>
    </table>

    Stisknutím klávesy ENTER přidejte více než jednu akci se skripty pro instalaci více součástí clusteru.
3. Klikněte na tlačítko **vyberte** uložit konfigurační skript akce a pokračujte vytvoření clusteru.

## <a name="call-scripts-using-azure-powershell"></a>Volání skriptů pomocí Azure Powershellu
Tento následující skript prostředí PowerShell ukazuje, jak nainstalovat Windows založené na clusteru HDInsight Spark.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Connect-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.

    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"

    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    #############################################################
    # Connect to Azure
    #############################################################

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Connect-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #############################################################
    # Prepare the dependent components
    #############################################################

    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext

    #############################################################
    # Create cluster with ApacheSpark
    #############################################################

    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey


    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `

    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


Pokud chcete nainstalovat další software, budete muset nahradit soubor skriptu ve skriptu:

Po zobrazení výzvy zadejte přihlašovací údaje pro cluster. Může trvat několik minut, než se cluster vytvoří.

## <a name="call-scripts-using-net-sdk"></a>Volání skriptů pomocí sady .NET SDK
Následující příklad ukazuje, jak nainstalovat Apache Spark v clusteru HDInsight založené na Windows. Pokud chcete nainstalovat další software, je potřeba nahradit soubor skriptu v kódu.

**Vytvoření clusteru HDInsight se Spark**

1. Vytvořte konzolovou aplikaci C# v sadě Visual Studio.
2. Z konzoly Správce balíčků Nuget spusťte následující příkaz.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight
3. Použijte následující příkazy using do souboru Program.cs:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
4. Umístěte kód ve třídě s následujícími možnostmi:

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId;
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,
                DefaultStorageInfo = new AzureStorageInfo(ExistingStorageName, ExistingStorageKey, ExistingContainer),
                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
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
5. Stisknutím klávesy **F5** spusťte aplikaci.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Podpora pro open source softwaru používaného v clusterech HDInsight
Služba Microsoft Azure HDInsight je flexibilní platforma, která vám umožní sestavovat aplikace pro velké objemy dat v cloudu s využitím ekosystém open source technologií formátovaných kolem Hadoop. Microsoft Azure poskytuje obecné úroveň podpory pro open source technologie, jak je popsáno v **rozsah podpory** část <a href="https://azure.microsoft.com/support/faq/" target="_blank">podporují nejčastější dotazy k Azure web</a>. Služba HDInsight poskytuje další úroveň podpory pro některé ze součástí, jak je popsáno níže.

Existují dva druhy opensourcové komponenty, které jsou k dispozici ve službě HDInsight:

* **Integrované komponenty** – tyto součásti jsou předem nainstalované na clusterech HDInsight a poskytuje základní funkce clusteru. Například Apache Hadoop YARN ResourceManager, jazyk dotazu Hive (HiveQL) a knihovně Apache Mahout patří do této kategorie. Úplný seznam součástí clusteru je k dispozici v [co je nového ve verzích clusterů Hadoop poskytovaných službou HDInsight?](hdinsight-component-versioning.md) </a>.
* **Vlastní komponenty** -, jako uživatel clusteru, můžete nainstalovat nebo použít ve vašich úloh žádné součásti k dispozici v komunitě nebo vytvořené vámi.

Integrované komponenty jsou plně podporované a Microsoft Support pomáhá izolovat a vyřešit problémy týkající se těchto součástí.

> [!WARNING]  
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované a Microsoft Support pomáhá izolovat a vyřešit problémy týkající se těchto součástí.
>
> Vlastní komponenty získat obchodně přiměřenou podporu můžete-li dále řešit tento problém. To může vést řeší problém nebo s výzvou k zapojení dostupné kanály pro open source technologie, ve kterých se nachází rozsáhlé znalosti pro tuto technologii. Existuje například mnoho komunitním webům, které lze použít jako: [Fórum na webu MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Také projektů Apache mít projektovým webům na [ https://apache.org ](https://apache.org), například: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).
>
>

Služba HDInsight poskytuje několik způsobů, jak používat vlastní komponenty. Bez ohledu na to, jak je komponenta použít nebo nainstalované v clusteru se vztahuje stejnou úroveň podpory. Tady je seznam nejběžnějších způsobů, aby vlastní komponenty je možné použít v clusterech HDInsight:

1. Odeslání úlohy - Hadoop nebo jiných typů úloh, které spouštění nebo použít vlastní komponenty můžete odeslat do clusteru.
2. Přizpůsobení clusteru – při vytváření clusteru, můžete zadat další nastavení a vlastních součástech, které se nainstalují na uzlech clusteru.
3. Ukázky – pro oblíbené vlastní komponenty, Microsoft a ostatní může poskytnout ukázky použití těchto komponent v clusterech HDInsight. Tyto ukázky jsou k dispozici bez podpory.

## <a name="develop-script-action-scripts"></a>Vývoj skriptových akcí skriptů
Zobrazit [vývoj skriptových akcí skriptů pro HDInsight][hdinsight-write-script].

## <a name="see-also"></a>Další informace najdete v tématech
* [Vytvořte clustery systému Apache Hadoop v HDInsight] [ hdinsight-provision-cluster] pokyny o tom, jak vytvořit HDInsight cluster s použitím jiné možnosti vlastního nastavení.
* [Vývoj skriptových akcí skriptů pro HDInsight][hdinsight-write-script]
* [Nainstalovat a používat Apache Spark v clusterech HDInsight][hdinsight-install-spark]
* [Instalace a použití Apache Solr na clusterech HDInsight](hdinsight-hadoop-solr-install.md).
* [Instalace a využít Apache Giraph u clusterů HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fáze při vytváření clusteru"
