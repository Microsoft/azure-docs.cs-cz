---
title: Migrace do nástroje Azure Resource Manageru pro HDInsight
description: Migrace do vývojových nástrojů Azure Resource Manageru pro clustery HDInsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: a3bbee9324b963bac1bb01271051011fdc9bc00b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723517"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrace do nástroje pro vývoj založených na Azure Resource Manageru pro clustery HDInsight

HDInsight je ukončení podpory nástrojů založených na Azure Service Manager ASM pro HDInsight. Pokud používáte prostředí Azure PowerShell, rozhraní příkazového řádku Azure Classic nebo sady HDInsight .NET SDK pro práci s clustery HDInsight, se doporučuje používat Azure Resource Manageru verze prostředí PowerShell, rozhraní příkazového řádku a sady .NET SDK do budoucna. Tento článek obsahuje odkazy na tom, jak migrovat na nový přístup využívající Resource Manager. Bez ohledu na to příslušné, tento dokument zdůrazňuje rozdíly mezi ASM a Resource Manageru přístupy k HDInsight.

> [!IMPORTANT]  
> Podpora pro ASM na základě prostředí PowerShell, rozhraní příkazového řádku, a sady .NET SDK se ukončit na **1. ledna 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrace klasického rozhraní příkazového řádku Azure do Azure Resource Manageru

> [!IMPORTANT]  
> Rozhraní příkazového řádku Azure neposkytuje podporu pro práci s clustery HDInsight. Stále můžete rozhraní příkazového řádku Azure Classic s HDInsight, ale rozhraní příkazového řádku Azure Classic je zastaralý.

Tady jsou základní příkazy pro práci s HDInsight pomocí rozhraní příkazového řádku Azure CLassic:

* `azure hdinsight cluster create` -Vytvoří se nový cluster HDInsight
* `azure hdinsight cluster delete` -Odstraní existující cluster HDInsight
* `azure hdinsight cluster show` – zobrazení informací o stávajícího clusteru
* `azure hdinsight cluster list` -obsahuje seznam clusterů HDInsight pro vaše předplatné Azure

Použití `-h` přepínač Zkontrolujte parametry a přepínače, které jsou k dispozici pro každý příkaz.

### <a name="new-commands"></a>Nové příkazy
Nové příkazy, které jsou k dispozici pomocí Azure Resource Manageru jsou:

* `azure hdinsight cluster resize` -dynamicky změní počet pracovních uzlů v clusteru
* `azure hdinsight cluster enable-http-access` – Povolí protokol HTTPs přístup ke clusteru (na ve výchozím nastavení)
* `azure hdinsight cluster disable-http-access` – Zakáže HTTPs přístup ke clusteru
* `azure hdinsight script-action` -poskytuje příkazy pro vytváření a správě akcí skriptů v clusteru
* `azure hdinsight config` – poskytuje pro vytvoření konfigurační soubor, který lze použít s příkazy `hdinsight cluster create` příkaz, který poskytuje informace o konfiguraci.

### <a name="deprecated-commands"></a>Zastaralé příkazy
Pokud používáte `azure hdinsight job` příkazy odesílat úlohy do clusteru HDInsight, tyto příkazy nejsou k dispozici prostřednictvím příkazy Resource Manageru. Pokud budete potřebovat programově odesílat úlohy do HDInsight pomocí skriptů, abyste používali místo toho rozhraní REST API, které poskytuje HDInsight. Další informace o odesílání úloh pomocí rozhraní REST API najdete v následujících dokumentech.

* [Spuštění úloh MapReduce s Hadoop v HDInsight pomocí cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Spuštění dotazy Apache Hive s Apache Hadoop v HDInsight pomocí cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Spuštění úlohy Apache Pig s Apache Hadoop v HDInsight pomocí cURL](hadoop/apache-hadoop-use-pig-curl.md)

Další informace o dalších způsobech interaktivní spuštění Apache Hadoop MapReduce, Apache Hivu a Apache Pig, naleznete v tématu [použití MapReduce se systémem Hadoop v HDInsight](hadoop/hdinsight-use-mapreduce.md), [použití Apache Hivu se službou Apache Hadoop v HDInsight](hadoop/hdinsight-use-hive.md), a [Apache Hadoop v HDInsight pomocí Apache Pig](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Příklady
**Vytvoření clusteru**

* Původního příkazu (ASM)- `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nový příkaz: `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Odstranění clusteru**

* Původního příkazu (ASM)- `azure hdinsight cluster delete myhdicluster`
* Nový příkaz: `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Výpis clusterů**

* Původního příkazu (ASM)- `azure hdinsight cluster list`
* Nový příkaz: `azure hdinsight cluster list`

> [!NOTE]  
> Pro příkaz seznam určující skupinu prostředků pomocí `-g` vrátí pouze clustery v zadané skupině prostředků.

**Zobrazit informace o clusteru**

* Původního příkazu (ASM)- `azure hdinsight cluster show myhdicluster`
* Nový příkaz: `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrace do Azure Resource Manageru prostředí Azure PowerShell
Obecné informace o Azure Powershellu v režimu Azure Resource Manageru najdete v [pomocí Azure Powershellu s Azure Resource Managerem](../powershell-azure-resource-manager.md).

Rutiny Azure PowerShell – Resource Manager je možné nainstalovat souběžně s rutinami ASM. Rutiny ze dvou režimů dají rozlišovat podle jejich názvy.  Do režimu Resource Manager má *AzHDInsight* v porovnání s názvy rutin *AzureHDInsight* v režimu ASM.  Například *New-AzHDInsightCluster* vs. *New-AzureHDInsightCluster*. Novinky názvy mohou mít parametry a přepínače, a k dispozici mnoho nových parametrů při použití Resource Manageru.  Například vyžadovat několik rutin nový přepínač s názvem *- ResourceGroupName*. 

Před použitím rutin HDInsight, musíte připojit ke svému účtu Azure a vytvořte novou skupinu prostředků:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Přejmenované rutiny
Chcete-li zobrazit seznam rutin HDInsight ASM v konzole Windows Powershellu:

    help *azurehdinsight*

Následující tabulka uvádí rutiny ASM a jejich názvy v režimu Resource Manageru:

| Rutiny ASM | Rutiny Resource Manageru |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzHDInsightConfigValues](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalues) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperties |[Get-AzHDInsightProperties](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperties) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nové rutiny
Toto jsou nové rutiny, které jsou dostupné jenom v režimu Resource Manageru. 

**Skript rutin týkajících se akce:**

* **Get-AzHDInsightPersistedScriptAction**: Získá akcí trvalého skriptu pro cluster a seznamů v chronologickém pořadí nebo získá podrobnosti zadané trvalá akce skriptu. 
* **Get-AzHDInsightScriptActionHistory**: Získá historii akcí skriptu clusteru a zobrazí ho v chronologickém pořadí reverzní nebo získá podrobnosti o dříve provedený skriptových akcí. 
* **Remove-AzHDInsightPersistedScriptAction**: Trvalá akce se skripty akce odebere z clusteru služby HDInsight.
* **Set-AzHDInsightPersistedScriptAction**: Nastaví dříve provedený skript akce bude akcí trvalého skriptu.
* **Submit-AzHDInsightScriptAction**: Odešle novou akci skriptu do clusteru Azure HDInsight. 

Další informace o použití, naleznete v tématu [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

**Cluster souvisejícím s identitou rutiny:**

* **Add-AzHDInsightClusterIdentity**: Identitu clusteru přidá do objekt konfigurace clusteru v clusteru HDInsight můžete přístup k Azure Data Lake Storage. Zobrazit [vytvoření clusteru HDInsight s Data Lake Storage pomocí Azure Powershellu](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Příklady
**Vytvoření clusteru**

Původního příkazu (ASM): 

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

Původního příkazu (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nový příkaz:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Přehled clusteru**

Původního příkazu (ASM):

    Get-AzureHDInsightCluster

Nový příkaz:

    Get-AzHDInsightCluster 

**Zobrazení clusteru**

Původního příkazu (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nový příkaz:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Další ukázky
* [Vytvoření clusterů HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Odeslání úlohy Apache Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Odesílání úloh Apache Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Odesílání úloh Apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrace na nové sady HDInsight .NET SDK
Správa služeb Azure podle [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) je nyní zastaralá. Doporučujeme použít Azure Resource Manageru podle [využívající Resource Manager sady HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight). Následující balíčky založené na ASM HDInsight jsou zastaralé.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Tato část obsahuje odkazy na další informace o tom, jak provádět určité úlohy pomocí sady SDK služby využívající Resource Manager.

| Jak... pomocí sady HDInsight SDK využívající Resource Manager | Odkazy |
| --- | --- |
| Vytvoření clusterů HDInsight pomocí sady .NET SDK |Zobrazit [HDInsight vytvářet clustery pomocí sady .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Přizpůsobení clusteru pomocí akce skriptu pomocí sady .NET SDK |Zobrazit [HDInsight Linux přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Ověření aplikace interaktivně pomocí Azure Active Directory pomocí .NET SDK |Zobrazit [spouštění Apache dotazů Hive pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Fragment kódu v tomto článku používá přístup interaktivní ověřování. |
| Ověření aplikace neinteraktivně pomocí Azure Active Directory pomocí .NET SDK |Zobrazit [vytvořit neinteraktivní aplikace pro HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Odeslat úlohu Apache Hive pomocí sady .NET SDK |Zobrazit [úlohy odeslání Apache Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Odeslat úlohu Apache Pig pomocí sady .NET SDK |Zobrazit [úlohy odeslání Apache Pig](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Odeslat úlohu Apache Sqoop pomocí sady .NET SDK |Zobrazit [Apache Sqoop odeslání úlohy](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Seznam clusterů HDInsight pomocí sady .NET SDK |Zobrazit [clusterů HDInsight seznamu](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Škálování clusterů HDInsight pomocí sady .NET SDK |Zobrazit [HDInsight škálování clusterů](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Udělení nebo odvolání přístupu do clusterů HDInsight pomocí sady .NET SDK |Zobrazit [udělení nebo odvolání přístupu ke clusterům HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Aktualizace přihlašovacích údajů uživatele HTTP pro clustery HDInsight pomocí sady .NET SDK |Zobrazit [přihlašovacích údajů uživatele HTTP aktualizace pro clustery HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Najít výchozí účet úložiště pro clustery HDInsight pomocí sady .NET SDK |Zobrazit [najít výchozí účet úložiště pro clustery HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Odstranění clusterů HDInsight pomocí sady .NET SDK |Zobrazit [clusterů HDInsight odstranit](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Příklady
Toto jsou některé příklady jak operace se provádí pomocí sady SDK na základě ASM a ekvivalentní kódu pro sadu SDK využívající Resource Manager.

**Vytvoření klienta CRUD clusteru**

* Původního příkazu (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nový příkaz (autorizaci instančního objektu služby)
  
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
* Nový příkaz (autorizace uživatelů)
  
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

* Původního příkazu (ASM)
  
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

**Povoluje přístup protokolu HTTP**

* Původního příkazu (ASM)
  
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

* Původního příkazu (ASM)
  
        client.DeleteCluster(dnsName);
* Nový příkaz
  
        client.Clusters.Delete(resourceGroup, dnsname);

