---
title: Přizpůsobení clusterů HDInsight pomocí akcí skriptů - Azure | Microsoft Docs
description: Zjistěte, jak přizpůsobit clusterů HDInsight pomocí akce skriptu.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3a63e216-4163-40c1-aa04-6b42fd0162ad
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 10/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 15fa3e7738810ada48f471a685f79a82445ad70c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Přizpůsobení clusterů HDInsight se systémem Windows pomocí akce skriptu
**Skript akce** slouží k vyvolání [vlastní skripty](hdinsight-hadoop-script-actions.md) během procesu vytváření clusteru pro instalaci další software v clusteru.

Informace v tomto článku jsou specifické pro clustery HDInsight se systémem Windows. V clusterech se systémem Linux naleznete v části [HDInsight se systémem Linux přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Clustery prostředí HDInsight lze přizpůsobit v různých další způsoby, například včetně další účty Azure Storage, změna Hadoop soubory konfigurace (core-site.xml, hive-site.xml atd.) nebo přidání sdílené knihovny (například Hive, Oozie) do společné umístění v clusteru. Toto vlastní nastavení můžete provést pomocí prostředí Azure PowerShell, .NET SDK služby Azure HDInsight nebo portálu Azure. Další informace najdete v tématu [vytvoření Hadoop clusterů v HDInsight][hdinsight-provision-cluster].

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Akce skriptu v procesu vytváření clusteru
Akce skriptu se používá pouze při clusteru je právě vytvářena. Následující diagram znázorňuje, když během procesu vytváření provedení akce skriptu:

![Přizpůsobení cluster HDInsight a fáze při vytváření clusteru][img-hdi-cluster-states]

Když je skript spuštěn, cluster zadá **ClusterCustomization** fáze. V této fázi skript běží pod účtem správce systému, paralelně na všechny zadané uzly v clusteru a poskytuje úplný správce oprávnění na uzlech.

> [!NOTE]
> Vzhledem k tomu, že máte oprávnění správce na uzlech clusteru během **ClusterCustomization** fáze, můžete použít skript k provedení operací, jako je spouštění a zastavování služeb, včetně služby související s Hadoop. Ano, v rámci skriptu je nutné zajistit, že služby Ambari a další služby související s Hadoop jsou spuštěny před dokončení spuštění skriptu. Tyto služby jsou nezbytné pro úspěšně zjištění stavu a stavu clusteru při jeho vytvoření. Pokud změníte žádnou konfiguraci v clusteru, který má vliv na tyto služby, musíte použít pomocných funkcí, které jsou k dispozici. Další informace o pomocných funkcí najdete v tématu [vyvíjet akce skriptu skripty pro HDInsight][hdinsight-write-script].
>
>

Výstup a protokoly chyb pro skript ukládají na výchozí účet úložiště, které jste zadali pro cluster. Protokoly jsou uložené v tabulce s názvem **u < \cluster-name-fragment >< \time-stamp > setuplog**. Toto jsou agregovaná protokoly ze skriptu, spusťte na všech uzlech (hlavního uzlu a pracovní uzly) v clusteru.

Každý cluster může přijmout víc akcí skriptů, které je vyvolán v pořadí, ve kterém jsou uvedené. Skript může spuštěna v hlavního uzlu, uzlů pracovního procesu nebo obojí.

HDInsight nabízí několik skriptů v clusterech HDInsight nainstalovat následující součásti:

| Název | Skript |
| --- | --- |
| **Nainstalujte Spark** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. V tématu [instalací a použitím clustery Spark v HDInsight][hdinsight-install-spark]. |
| **Nainstalujte jazyk R** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. V tématu [instalací a použitím R v clusterech HDInsight] [hdinsight-install-r]. |
| **Nainstalujte Solr** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. V tématu [instalace a použití clusterů v HDInsight Solr](hdinsight-hadoop-solr-install.md). |
| - **Nainstalujte Giraph** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. V tématu [instalace a použití clusterů v HDInsight Giraph](hdinsight-hadoop-giraph-install.md). |
| **Předběžné načtení knihovny Hive** |https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1. V tématu [přidat Hive knihovny v clusterech prostředí HDInsight](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="call-scripts-using-the-azure-portal"></a>Volání skripty pomocí portálu Azure
**Z portálu Azure**

1. Zahájení vytváření clusteru, jak je popsáno v [vytvoření Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
2. V části volitelné konfigurace pro **akcí skriptů** okně klikněte na tlačítko **přidat akce skriptu** poskytnout podrobnosti o akce skriptu, jak je uvedeno níže:

    ![Použití akce skriptu k přizpůsobení cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "použití akce skriptu k přizpůsobení clusteru")

    <table border='1'>
        <tr><th>Vlastnost</th><th>Hodnota</th></tr>
        <tr><td>Název</td>
            <td>Zadejte název akce skriptu.</td></tr>
        <tr><td>Identifikátor URI skriptu</td>
            <td>Zadejte identifikátor URI skriptu, která je volána, chcete-li přizpůsobit clusteru. s</td></tr>
        <tr><td>HEAD/pracovního procesu</td>
            <td>Zadejte uzly (**Head** nebo **pracovní**) podle kterého se spouští skript přizpůsobení.</b>.
        <tr><td>Parametry</td>
            <td>Zadejte parametry, pokud se vyžadují skriptem.</td></tr>
    </table>

    Stiskněte klávesu ENTER, chcete-li přidat více než jednu akci skriptu pro instalaci více součástí v clusteru.
3. Klikněte na tlačítko **vyberte** uložit konfiguraci akce skriptu a pokračujte vytvoření clusteru.

## <a name="call-scripts-using-azure-powershell"></a>Volání skripty pomocí Azure PowerShell
Tento následující skript prostředí PowerShell ukazuje, jak nainstalovat Spark na clusteru HDInsight založené na systému Windows.  

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

Po zobrazení výzvy zadejte přihlašovací údaje pro cluster. To může trvat několik minut, než je vytvořen cluster.

## <a name="call-scripts-using-net-sdk"></a>Volání skripty pomocí sady .NET SDK
Následující příklad ukazuje, jak nainstalovat Spark na clusteru HDInsight založené na systému Windows. Pokud chcete nainstalovat další software, potřebujete nahradit soubor skriptu v kódu.

**Vytvoření clusteru HDInsight pomocí Spark**

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
4. Umístěte kód ve třídě, s následujícími službami:

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

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Podpora pro open-source softwaru použít na clustery HDInsight
Služba Microsoft Azure HDInsight je flexibilní platforma, která vám umožní sestavovat aplikace velkých objemů dat v cloudu pomocí prostředí technologie open source vytvořen kolem Hadoop. Microsoft Azure poskytuje určitou úroveň podpory pro technologie open source, jak je popsáno v **podporu oboru** části <a href="http://azure.microsoft.com/support/faq/" target="_blank">web Azure podporují – nejčastější dotazy</a>. Služba HDInsight poskytuje další úroveň podpory pro některé součásti, jak je popsáno níže.

Existují dva typy open-source komponent, které jsou k dispozici ve službě HDInsight:

* **Integrované komponenty** -tyto komponenty jsou předinstalované na clustery HDInsight a poskytují základní funkce služby clusteru. Například YARN ResourceManager, Hive dotazovací jazyk (HiveQL) a knihovně Mahout patří do této kategorie. Úplný seznam součástí clusteru je k dispozici v [co je nového ve verzích clusterů systému Hadoop poskytovaných prostředím HDInsight?](hdinsight-component-versioning.md) </a>.
* **Vlastní komponenty** -, jako uživatel clusteru, můžete nainstalovat nebo použít v vaše úlohy žádné součásti k dispozici v komunitě nebo vytvořené vámi.

Integrované komponenty jsou plně podporované a Microsoft Support pomůže k izolování a vyřešení problémů týkajících se těchto součástí.

> [!WARNING]
> Součásti, které jsou součástí clusteru HDInsight jsou plně podporované a Microsoft Support pomůže k izolování a vyřešení problémů týkajících se těchto součástí.
>
> Vlastní komponenty získat vyvineme podporu k pomoci při další řešení problému. To může způsobit řešení problému nebo s žádostí o zapojení dostupné kanály pro technologie s otevřeným zdrojem, kterých se nachází hluboké znalosti pro tuto technologii. Například existuje mnoho komunity webů, které lze použít jako: [fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Také Apache projekty mají na projektu serverů [ http://apache.org ](http://apache.org), například: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).
>
>

Služba HDInsight poskytuje několik způsobů, jak používat vlastní komponenty. Bez ohledu na to, jak součást použít nebo nainstalované v clusteru se vztahuje stejnou úroveň podpory. Níže je seznam nejběžnější způsoby vlastní komponenty lze v clusterech HDInsight:

1. Úloha odeslání - Hadoop nebo jiné typy úloh, které spustit nebo používat vlastní komponenty lze odeslat do clusteru.
2. Přizpůsobení clusteru – při vytváření clusteru, můžete zadat další nastavení a vlastní součásti, které se nainstalují na uzlech clusteru.
3. Ukázky - oblíbených vlastní součásti, Microsoft a ostatní mohou poskytnout ukázky použití těchto součástí v clusterech HDInsight. Tyto soubory jsou uvedeny bez podpory.

## <a name="develop-script-action-scripts"></a>Vývoj skriptů akce skriptu
V tématu [vyvíjet akce skriptu skripty pro HDInsight][hdinsight-write-script].

## <a name="see-also"></a>Další informace najdete v tématech
* [Vytvoření clusterů systému Hadoop v HDInsight] [ hdinsight-provision-cluster] obsahuje pokyny k vytvoření clusteru HDInsight pomocí jiné možnosti vlastního nastavení.
* [Vývoj skriptů akce skriptu pro HDInsight][hdinsight-write-script]
* [Nainstalovat a používat Spark v HDInsight clustery][hdinsight-install-spark]
* [Nainstalovat a používat Solr v clusterech HDInsight](hdinsight-hadoop-solr-install.md).
* [Nainstalovat a používat Giraph v clusterech HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fáze při vytváření clusteru"
