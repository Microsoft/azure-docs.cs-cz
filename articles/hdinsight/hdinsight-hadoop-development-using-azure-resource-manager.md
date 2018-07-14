---
title: Migrace do nástroje Azure Resource Manageru pro HDInsight | Dokumentace Microsoftu
description: Migrace do vývojových nástrojů Azure Resource Manageru pro clustery HDInsight
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: ''
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 7234341fd63f4e841ac8d15a51293148d2c60975
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001867"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrace do nástroje pro vývoj založených na Azure Resource Manageru pro clustery HDInsight

HDInsight je ukončení podpory nástrojů založených na Azure Service Manager ASM pro HDInsight. Pokud používáte prostředí Azure PowerShell, rozhraní příkazového řádku Azure nebo sady HDInsight .NET SDK pro práci s clustery HDInsight, se doporučuje používat Azure Resource Manageru verze prostředí PowerShell, rozhraní příkazového řádku a sady .NET SDK do budoucna. Tento článek obsahuje odkazy na tom, jak migrovat na nový přístup využívající Resource Manager. Bez ohledu na to příslušné, tento dokument zdůrazňuje rozdíly mezi ASM a Resource Manageru přístupy k HDInsight.

> [!IMPORTANT]
> Podpora pro ASM na základě prostředí PowerShell, rozhraní příkazového řádku, a sady .NET SDK se ukončit na **1. ledna 2017**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Migrace rozhraní příkazového řádku Azure do Azure Resource Manageru

> [!IMPORTANT]
> Azure CLI 2.0 neposkytuje podporu pro práci s clustery HDInsight. Stále můžete Azure CLI 1.0 s HDInsight, ale příkazového řádku Azure CLI 1.0 je zastaralý.

Tady jsou základní příkazy pro práci s HDInsight prostřednictvím příkazového řádku Azure CLI 1.0:

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
* [Spouštění dotazů Hive se systémem Hadoop v HDInsight pomocí cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Spuštění úlohy Pig s Hadoop v HDInsight pomocí cURL](hadoop/apache-hadoop-use-pig-curl.md)

Informace o dalších způsobech spouštění MapReduce, Hive, Pig interaktivně, naleznete v tématu [použití MapReduce se systémem Hadoop v HDInsight](hadoop/hdinsight-use-mapreduce.md), [použití Hivu s Hadoopem v HDInsight](hadoop/hdinsight-use-hive.md), a [použití Pigu se systémem Hadoop v HDInsight](hadoop/hdinsight-use-pig.md).

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
> 
> 

**Zobrazit informace o clusteru**

* Původního příkazu (ASM)- `azure hdinsight cluster show myhdicluster`
* Nový příkaz: `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrace do Azure Resource Manageru prostředí Azure PowerShell
Obecné informace o Azure Powershellu v režimu Azure Resource Manageru najdete v [pomocí Azure Powershellu s Azure Resource Managerem](../powershell-azure-resource-manager.md).

Rutiny Azure PowerShell – Resource Manager je možné nainstalovat souběžně s rutinami ASM. Rutiny ze dvou režimů dají rozlišovat podle jejich názvy.  Do režimu Resource Manager má *AzureRmHDInsight* v porovnání s názvy rutin *AzureHDInsight* v režimu ASM.  Například *New-AzureRmHDInsightCluster* vs. *New-AzureHDInsightCluster*. Novinky názvy mohou mít parametry a přepínače, a k dispozici mnoho nových parametrů při použití Resource Manageru.  Například vyžadovat několik rutin nový přepínač s názvem *- ResourceGroupName*. 

Před použitím rutin HDInsight, musíte připojit ke svému účtu Azure a vytvořte novou skupinu prostředků:

* Connect-AzureRmAccount nebo [rutina Select-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). Zobrazit [ověřování instančního objektu pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Přejmenované rutiny
Chcete-li zobrazit seznam rutin HDInsight ASM v konzole Windows Powershellu:

    help *azurermhdinsight*

Následující tabulka uvádí rutiny ASM a jejich názvy v režimu Resource Manageru:

| Rutiny ASM | Rutiny Resource Manageru |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Nové rutiny
Toto jsou nové rutiny, které jsou dostupné jenom v režimu Resource Manageru. 

**Skript rutin týkajících se akce:**

* **Get-AzureRmHDInsightPersistedScriptAction**: získá akcí trvalého skriptu pro cluster a seznamů v chronologickém pořadí nebo získá podrobnosti zadané trvalá akce skriptu. 
* **Get-AzureRmHDInsightScriptActionHistory**: získá historii akcí skriptu clusteru a zobrazí ho v chronologickém pořadí reverzní nebo získá podrobnosti o dříve provedený skriptových akcí. 
* **Odebrat AzureRmHDInsightPersistedScriptAction**: Odebere z clusteru služby HDInsight trvalá akce skriptu.
* **Set-AzureRmHDInsightPersistedScriptAction**: Nastaví dříve provedený skript akce bude akcí trvalého skriptu.
* **Odeslat AzureRmHDInsightScriptAction**: odešle novou akci skriptu do clusteru Azure HDInsight. 

Další informace o použití, naleznete v tématu [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

**Cluster souvisejícím s identitou rutiny:**

* **Přidat AzureRmHDInsightClusterIdentity**: identitu clusteru přidá objekt konfigurace clusteru tak, aby clusteru HDInsight můžete přístup k Azure Data Lake Store. Zobrazit [vytvoření clusteru HDInsight s Data Lake Store pomocí Azure Powershellu](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

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

    New-AzureRmHDInsightCluster `
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

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Přehled clusteru**

Původního příkazu (ASM):

    Get-AzureHDInsightCluster

Nový příkaz:

    Get-AzureRmHDInsightCluster 

**Zobrazení clusteru**

Původního příkazu (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nový příkaz:

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Další ukázky
* [Vytvoření clusterů HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Odesílání úloh Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Odesílání úlohy Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Odesílání úloh Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrace na nové sady HDInsight .NET SDK
Správa služeb Azure podle [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) je nyní zastaralá. Doporučujeme použít Azure Resource Manageru podle [využívající Resource Manager sady HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight). Následující balíčky založené na ASM HDInsight jsou zastaralé.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Tato část obsahuje odkazy na další informace o tom, jak provádět určité úlohy pomocí sady SDK služby využívající Resource Manager.

| Jak... pomocí sady HDInsight SDK využívající Resource Manager | Odkazy |
| --- | --- |
| Vytvoření clusterů HDInsight pomocí sady .NET SDK |Zobrazit [HDInsight vytvářet clustery pomocí sady .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Přizpůsobení clusteru pomocí akce skriptu pomocí sady .NET SDK |Zobrazit [HDInsight Linux přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Ověření aplikace interaktivně pomocí Azure Active Directory pomocí .NET SDK |Zobrazit [spouštění dotazů Hive pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Fragment kódu v tomto článku používá přístup interaktivní ověřování. |
| Ověření aplikace neinteraktivně pomocí Azure Active Directory pomocí .NET SDK |Zobrazit [vytvořit neinteraktivní aplikace pro HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Odeslání úlohy Hive pomocí sady .NET SDK |Zobrazit [úloh odeslat Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Odeslání úlohy Pig pomocí sady .NET SDK |Zobrazit [úlohy odeslání Pig](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Odeslání úlohy Sqoop pomocí sady .NET SDK |Zobrazit [Sqoop odeslání úlohy](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
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

