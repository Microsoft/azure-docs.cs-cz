---
title: Migrace do nástrojů Azure Resource Manager pro HDInsight
description: Jak migrovat do nástrojů pro vývoj Azure Resource Managerpro clustery HDInsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 76eb3a135f7a32a30cfa62546a644bc77cf39998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934590"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrace do vývojových nástrojů založených na Azure Resource Manageru pro clustery HDInsight

HDInsight zavrhuje nástroje azure service manageru (ASM) pro HDInsight. Pokud jste pro práci s clustery HDInsight používali Azure PowerShell, Azure Classic CLI nebo HDInsight .NET SDK, doporučujeme vám do budoucna používat verze Azure Resource Manageru PowerShellu, CLI a .NET SDK. Tento článek obsahuje odkazy na to, jak migrovat na nový přístup založený na Správci prostředků. Pokud je to možné, tento dokument upozorňuje na rozdíly mezi asm a resource manager přístupy pro HDInsight.

> [!IMPORTANT]  
> **1. ledna 2017**bude ukončena podpora prostředí PowerShell, CLI a .NET SDK založené na asm.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrace klasického příkazového příkazového příkazového příkazu Azure do Správce prostředků Azure

> [!IMPORTANT]  
> Rozhraní příkazového příkazu Azure neposkytuje podporu pro práci s clustery HDInsight. Azure Classic CLI s HDInsight umíte, ale azure classic CLI je zastaralé.

Následují základní příkazy pro práci s HDInsight prostřednictvím rozhraní příkazového příkazu Azure CLassic:

* `azure hdinsight cluster create`- vytvoří nový HDInsight cluster
* `azure hdinsight cluster delete`- odstraní existující cluster HDInsight
* `azure hdinsight cluster show`- zobrazení informací o existujícím clusteru
* `azure hdinsight cluster list`- seznamy clusterů HDInsight pro vaše předplatné Azure

Pomocí `-h` přepínače zkontrolujte parametry a přepínače, které jsou k dispozici pro každý příkaz.

### <a name="new-commands"></a>Nové příkazy
Nové příkazy, které jsou k dispozici ve Správci prostředků Azure, jsou:

* `azure hdinsight cluster resize`- dynamicky mění počet pracovních uzlů v clusteru
* `azure hdinsight cluster enable-http-access`- umožňuje HTTPs přístup ke clusteru (ve výchozím nastavení)
* `azure hdinsight cluster disable-http-access`- zakáže přístup httpů ke clusteru.
* `azure hdinsight script-action`- poskytuje příkazy pro vytváření/správu skriptovacích akcí v clusteru
* `azure hdinsight config`- poskytuje příkazy pro vytvoření konfiguračního souboru, který lze použít pomocí příkazu `hdinsight cluster create` k poskytnutí informací o konfiguraci.

### <a name="deprecated-commands"></a>Zastaralé příkazy
Pokud pomocí `azure hdinsight job` příkazů odesíláte úlohy do clusteru HDInsight, nebudou tyto příkazy k dispozici prostřednictvím příkazů Správce prostředků. Pokud potřebujete programově odeslat úlohy do HDInsight ze skriptů, měli byste místo toho použít rozhraní REST API poskytované HDInsight. Další informace o odesílání úloh pomocí rest API naleznete v následujících dokumentech.

* [Spuštění úloh MapReduce s Hadoopem na HDInsight pomocí cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Spouštění dotazů Apache Hive s Apache Hadoop na HDInsight pomocí cURL](hadoop/apache-hadoop-use-hive-curl.md)


Informace o dalších způsobech interaktivního spouštění Apache Hadoop MapReduce, Apache Hive a Apache Pig najdete v [tématech Použití mapreduce s Hadoopem na HDInsightu](hadoop/hdinsight-use-mapreduce.md), [Použití Apache Hive s Apache Hadoop na HDInsight](hadoop/hdinsight-use-hive.md)a [Použití Apache Pig s Apache Hadoop na HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Příklady
**Vytvoření clusteru**

* Starý příkaz (ASM) -`azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nový příkaz -`azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Odstranění clusteru**

* Starý příkaz (ASM) -`azure hdinsight cluster delete myhdicluster`
* Nový příkaz -`azure hdinsight cluster delete mycluster -g myresourcegroup`

**Seznam clusterů**

* Starý příkaz (ASM) -`azure hdinsight cluster list`
* Nový příkaz -`azure hdinsight cluster list`

> [!NOTE]  
> Pro příkaz seznamu vrátí zadání skupiny prostředků pomocí `-g` vrátí pouze clustery v zadané skupině prostředků.

**Zobrazit informace o clusteru**

* Starý příkaz (ASM) -`azure hdinsight cluster show myhdicluster`
* Nový příkaz -`azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrace Azure PowerShellu do Správce prostředků Azure
Obecné informace o Azure PowerShellu v režimu Azure Resource Manager uhlečujete na [webu Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md).

Rutiny Azure PowerShell Resource Manager lze nainstalovat vedle rutin ASM. Rutiny z těchto dvou režimů lze rozlišit podle jejich názvů.  Režim Správce prostředků má *AzHDInsight* v názvech rutiny ve srovnání s *AzureHDInsight* v režimu ASM.  Například *New-AzHDInsightCluster* vs. *New-AzureHDInsightCluster*. Parametry a přepínače mohou mít názvy zpráv a existuje mnoho nových parametrů, které jsou k dispozici při použití Správce prostředků.  Například několik rutin vyžaduje nový přepínač s názvem *-ResourceGroupName*. 

Než budete moci používat rutiny HDInsight, musíte se připojit ke svému účtu Azure a vytvořit novou skupinu prostředků:

* [Účet Connect-Az](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Přejmenované rutiny
Seznam rutin HDInsight ASM v konzole Windows PowerShell:

    help *azurehdinsight*

V následující tabulce jsou uvedeny rutiny ASM a jejich názvy v režimu Správce prostředků:

| Rutiny ASM | Rutiny Správce prostředků |
| --- | --- |
| Hodnota Add-AzureHDInsightConfigValue |[Hodnota Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Doplněk AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Akce Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Přidat-AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Vlastnost Get-AzureHDInsight |[Vlastnost Get-AzHDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Vyvolat-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[Nový-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[Nová-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[Nová-AzHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[Nová-AzHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[Nová-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[Nová-AzHDInsightSqoopJobDefinice](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingReduceJobDefinice](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Odebrat-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Velikost clusteru Set-AzHDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Použití-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nové rutiny
Následují nové rutiny, které jsou k dispozici pouze v režimu Správce prostředků. 

**Rutiny související s akcí skriptu:**

* **Get-AzHDInsightPersistedScriptAction**: Získá trvalé akce skriptu pro cluster a zobrazí seznam je v chronologickém pořadí nebo získá podrobnosti pro zadanou trvalou akci skriptu. 
* **Get-AzHDInsightScriptActionHistory**: Získá historii akcí skriptu pro cluster a zobrazí seznam v obráceném chronologickém pořadí nebo získá podrobnosti o dříve provedené akci skriptu. 
* **Remove-AzHDInsightPersistedScriptAction**: Odebere trvalou akci skriptu z clusteru HDInsight.
* **Set-AzHDInsightPersistedScriptAction**: Nastaví dříve spuštěnou akci skriptu jako trvalou akci skriptu.
* **Submit-AzHDInsightScriptAction**: Odešle novou akci skriptu do clusteru Azure HDInsight. 

Další informace o využití najdete [v tématu Přizpůsobení clusterů HDInsight založených na Linuxu pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

**Rutiny související s identitou clusteru:**

* **Add-AzHDInsightClusterIdentity**: Přidá identitu clusteru do objektu konfigurace clusteru, aby měl cluster HDInsight přístup k úložišti Azure Data Lake Storage. Viz [Vytvoření clusteru HDInsight s úložištěm datových jezer pomocí Azure PowerShellu](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Příklady
**Vytvoření clusteru**

Starý příkaz (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Nový příkaz:

    New-AzHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Odstranění clusteru**

Starý příkaz (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nový příkaz:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Seznam clusteru**

Starý příkaz (ASM):

    Get-AzureHDInsightCluster

Nový příkaz:

    Get-AzHDInsightCluster 

**Zobrazit cluster**

Starý příkaz (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nový příkaz:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Ostatní vzorky
* [Vytváření clusterů HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Pracovní příležitosti odeslat apache hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Odeslat pracovní příležitosti pro pracovní příležitosti pro Apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrace na novou sadu HDInsight .NET SDK
Sada [HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) založená na správě služeb Azure (ASM) je teď zastaralá. Doporučujeme použít [hdinsight .NET SDK založený](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)na Správci prostředků azure. Následující balíčky HDInsight založené na ASM jsou zastaralé.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Tato část obsahuje odkazy na další informace o tom, jak provádět určité úkoly pomocí sady SDK založené na Správci prostředků.

| Jak... použití sady HDInsight SDK založené na Správci prostředků | Odkazy |
| --- | --- |
| Sada Azure HDInsight SDK pro .NET|Viz [Azure HDInsight SDK pro .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet) |
| Interaktivní ověřování aplikací pomocí služby Azure Active Directory pomocí sady .NET SDK |Viz [Spuštění dotazů Apache Hive pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Fragment kódu v tomto článku používá přístup interaktivního ověřování. |
| Ověřování aplikací neinteraktivně pomocí služby Azure Active Directory pomocí sady .NET SDK |Viz [Vytváření neinteraktivních aplikací pro HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Odeslání úlohy Apache Hive pomocí sady .NET SDK |Viz [Pracovní příležitosti Odeslat Apache Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Odeslání úlohy Apache Sqoop pomocí sady .NET SDK |Viz [Odeslat pracovní příležitosti apache sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Seznam clusterů HDInsight pomocí sady .NET SDK |Viz [Seznam clusterů HDInsight](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Škálování clusterů HDInsight pomocí sady .NET SDK |Viz [Škálování clusterů HDInsight](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Udělení/odvolání přístupu ke clusterům HDInsight pomocí sady .NET SDK |Viz [Udělení/odvolání přístupu ke clusterům HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Aktualizace pověření uživatelů PROTOKOLU HTTP pro clustery HDInsight pomocí sady .NET SDK |Viz [Aktualizace přihlašovacích údajů uživatelů HTTP pro clustery HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Vyhledání výchozího účtu úložiště pro clustery HDInsight pomocí sady .NET SDK |Viz [Vyhledání výchozího účtu úložiště pro clustery HDInsight.](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Odstranění clusterů HDInsight pomocí sady .NET SDK |Viz [Odstranění clusterů HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Příklady
Následují některé příklady, jak se operace provádí pomocí sady SDK založené na ASM a ekvivalentního fragmentu kódu pro sadu SDK založené na Správci prostředků.

**Vytvoření clusteru CRUD klienta**

* Starý příkaz (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nový příkaz (Autorizace hlavního povinného servisu)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Nový příkaz (autorizace uživatele)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Vytvoření clusteru**

* Starý příkaz (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Nový příkaz
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Povolení přístupu HTTP**

* Starý příkaz (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Nový příkaz
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Odstranění clusteru**

* Starý příkaz (ASM)
  
        client.DeleteCluster(dnsName);
* Nový příkaz
  
        client.Clusters.Delete(resourceGroup, dnsname);

