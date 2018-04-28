---
title: Migrace do nástroje Správce prostředků Azure pro HDInsight | Microsoft Docs
description: Postup migrace na vývojové nástroje Azure Resource Manageru pro clustery služby HDInsight na
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
ms.openlocfilehash: b0a73ea89bec67cbf644cce60913981a0533360a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrace na Azure Resource Manager vývojové nástroje založené pro clustery služby HDInsight

HDInsight je místo na základě Azure Service Manager ASM nástroje začne pro HDInsight. Pokud používáte prostředí Azure PowerShell, rozhraní příkazového řádku Azure nebo sady SDK rozhraní .NET HDInsight pro práci s clustery HDInsight, můžete se doporučuje používat Azure Resource Manager verze prostředí PowerShell, rozhraní příkazového řádku a .NET SDK do budoucna. Tento článek obsahuje ukazatele na tom, jak migrovat do nového přístupu na základě Resource Manager. Pokud je to potřeba, označuje tento dokument rozdíly mezi možnostmi ASM a správce prostředků pro HDInsight.

> [!IMPORTANT]
> Podpora pro ASM na základě prostředí PowerShell, rozhraní příkazového řádku, a .NET SDK se ukončí na **1. ledna 2017**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Migrace rozhraní příkazového řádku Azure do Azure Resource Manageru

> [!IMPORTANT]
> Azure CLI 2.0 neposkytuje podporu pro práci s clustery HDInsight. Azure CLI 1.0 můžete dál používat s HDInsight, ale Azure CLI 1.0 je zastaralý.

Zde jsou základní příkazy pro práci s HDInsight pomocí Azure CLI 1.0:

* `azure hdinsight cluster create` -Vytvoří nový cluster HDInsight
* `azure hdinsight cluster delete` -Odstraní stávající cluster HDInsight
* `azure hdinsight cluster show` – Zobrazí informace o stávajícího clusteru
* `azure hdinsight cluster list` -obsahuje seznam clustery HDInsight pro vaše předplatné Azure

Použití `-h` přepínač tak, aby kontrolovat parametry a přepínače, které jsou k dispozici u každého příkazu.

### <a name="new-commands"></a>Nové příkazy
Nové příkazy, které jsou k dispozici s Azure Resource Manager jsou:

* `azure hdinsight cluster resize` -dynamicky změní počet uzlů pracovního procesu v clusteru
* `azure hdinsight cluster enable-http-access` – Umožňuje HTTPs přístup ke clusteru (na ve výchozím nastavení)
* `azure hdinsight cluster disable-http-access` – Zakáže HTTPs přístup ke clusteru
* `azure hdinsight script-action` -obsahuje příkazy pro vytváření nebo správu akcí skriptů v clusteru
* `azure hdinsight config` -poskytuje příkazů pro vytvoření konfiguračního souboru, který lze použít s `hdinsight cluster create` příkaz poskytnout informace o konfiguraci.

### <a name="deprecated-commands"></a>Nepoužívané příkazy
Pokud použijete `azure hdinsight job` příkazy k odesílání úloh do clusteru HDInsight, tyto příkazy nejsou k dispozici prostřednictvím příkazy Resource Manager. Pokud potřebujete prostřednictvím kódu programu odesílání úloh do HDInsight z skriptů, měli byste místo toho použít rozhraní REST API poskytovaných v HDInsight. Další informace o odesílání úloh pomocí rozhraní REST API najdete v následujících dokumentech.

* [Spuštění úloh MapReduce s Hadoop v HDInsight pomocí cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Spouštění dotazů Hive se systémem Hadoop v HDInsight pomocí cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Spuštění úlohy Pig s Hadoop v HDInsight pomocí cURL](hadoop/apache-hadoop-use-pig-curl.md)

Informace o jiných způsobech spustit MapReduce, Hive a vepřových interaktivně, najdete v části [používání MapReduce s Hadoop v HDInsight](hadoop/hdinsight-use-mapreduce.md), [používání Hive s Hadoop v HDInsight](hadoop/hdinsight-use-hive.md), a [použijte Pig s Hadoop v HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Příklady
**Vytvoření clusteru**

* Příkaz staré (ASM)- `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nový příkaz: `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Odstranění clusteru**

* Příkaz staré (ASM)- `azure hdinsight cluster delete myhdicluster`
* Nový příkaz: `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Seznam clustery**

* Příkaz staré (ASM)- `azure hdinsight cluster list`
* Nový příkaz: `azure hdinsight cluster list`

> [!NOTE]
> Pro příkaz seznamu zadání skupiny prostředků pomocí `-g` vrátí pouze clustery v zadaná skupina prostředků.
> 
> 

**Zobrazit informace o clusteru**

* Příkaz staré (ASM)- `azure hdinsight cluster show myhdicluster`
* Nový příkaz: `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrace prostředí Azure PowerShell pro Azure Resource Manager
Obecné informace o prostředí Azure PowerShell v režimu Azure Resource Manager naleznete na [použití Azure Powershellu s Azure Resource Manager](../powershell-azure-resource-manager.md).

Rutiny Azure PowerShell Resource Manager lze nainstalovat node souběžně s rutiny ASM. Rutiny ze dvou režimů dají rozlišovat podle jejich názvy.  Režim Resource Manager má *AzureRmHDInsight* v porovnání se názvy rutin *AzureHDInsight* v režimu ASM.  Například *New-AzureRmHDInsightCluster* vs. *New-AzureHDInsightCluster*. Názvy zprávy mohou mít parametry a přepínače, a existuje mnoho nových parametrů při použití Správce prostředků.  Například několik rutin vyžadovat nového přepínače názvem *- ResourceGroupName*. 

Před použitím rutin HDInsight, musíte připojit k účtu Azure a vytvořit novou skupinu prostředků:

* Připojit AzureRmAccount nebo [vyberte AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). V tématu [ověřování hlavní název služby pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Přejmenovat rutiny
Seznam rutin HDInsight ASM v konzole Windows PowerShell:

    help *azurermhdinsight*

Následující tabulka uvádí rutiny ASM a jejich názvy v režimu Resource Manager:

| Rutiny ASM | Rutiny správce prostředků |
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
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
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
Níže jsou nové rutiny, které jsou dostupné jenom v režimu Resource Manager. 

**Rutiny související s akce skriptu:**

* **Get-AzureRmHDInsightPersistedScriptAction**: získá akcí trvalého skriptu pro cluster a zobrazí je v chronologickém pořadí nebo získá podrobnosti pro akci zadaný trvalého skriptu. 
* **Get-AzureRmHDInsightScriptActionHistory**: získá historii akcí skriptu pro cluster a seznamů v obráceném chronologickém pořadí nebo získá podrobnosti akce dříve spuštění skriptu. 
* **Odebrat AzureRmHDInsightPersistedScriptAction**: Odebere akcí trvalého skriptu z clusteru služby HDInsight.
* **Set-AzureRmHDInsightPersistedScriptAction**: Nastaví akce dříve spuštění skriptu jako akcí trvalého skriptu.
* **Odeslání AzureRmHDInsightScriptAction**: Odešle nové akce skriptu do clusteru Azure HDInsight. 

Využití Další informace najdete v tématu [HDInsight se systémem Linux přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

**Cluster rutin týkajících se identity:**

* **Přidat AzureRmHDInsightClusterIdentity**: Přidá identitu clusteru do objekt konfigurace clusteru tak, aby clusteru HDInsight můžete přístup k Azure Data Lake úložiště. V tématu [vytvoření clusteru HDInsight s Data Lake Store pomocí Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Příklady
**Vytvoření clusteru**

Příkaz staré (ASM): 

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

Příkaz staré (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nový příkaz:

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Seznam clusteru**

Příkaz staré (ASM):

    Get-AzureHDInsightCluster

Nový příkaz:

    Get-AzureRmHDInsightCluster 

**Zobrazit clusteru**

Příkaz staré (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nový příkaz:

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Další ukázky
* [Vytvoření clusterů HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Odeslání úloh Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Odeslání úlohy Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Odesílání úloh Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrace na novou sadu .NET SDK pro HDInsight
Azure Service Management základě [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) je nyní zastaralý. Doporučujeme používat správu prostředků Azure prostřednictvím [využívající Resource Manager SDK rozhraní .NET HDInsight](https://msdn.microsoft.com/library/azure/mt271028.aspx). Následující balíčky HDInsight se systémem ASM jsou zastaralá.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Tato část obsahuje odkazy na další informace o tom, jak provádět určité úlohy pomocí sady SDK využívající Resource Manager.

| Postup... pomocí sady SDK HDInsight založené na správci prostředků | Odkazy |
| --- | --- |
| Vytvoření clusterů HDInsight pomocí sady .NET SDK |V tématu [Tvorba clusterů HDInsight pomocí sady .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Přizpůsobení clusteru pomocí akce skriptu pomocí .NET SDK |V tématu [HDInsight Linux přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Ověření aplikací interaktivně pomocí .NET SDK služby Azure Active Directory |V tématu [spouštění dotazů Hive pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Fragment kódu v tomto článku používá interaktivní ověřování přístup. |
| Ověření aplikací interaktivně pomocí .NET SDK služby Azure Active Directory |V tématu [vytvořit neinteraktivní aplikace pro HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Odeslání úlohy Hive pomocí sady .NET SDK |V tématu [úlohy odeslání Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Odeslat úlohu Pig pomocí sady .NET SDK |V tématu [úlohy odeslání Pig](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Odeslání úlohy Sqoop pomocí sady .NET SDK |V tématu [Sqoop odeslání úlohy](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Seznam clusterů HDInsight pomocí sady .NET SDK |V tématu [clusterů HDInsight seznamu](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Škálování clusterů HDInsight pomocí sady .NET SDK |V tématu [clusterů HDInsight škálování](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Udělení nebo odvolání přístupu ke clusterům HDInsight pomocí sady .NET SDK |V tématu [udělení nebo odvolání přístupu ke clusterům HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Aktualizovat pověření uživatele HTTP pro clustery služby HDInsight pomocí sady .NET SDK |V tématu [přihlašovací údaje uživatele HTTP aktualizace pro clustery služby HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Najít výchozí účet úložiště pro clustery služby HDInsight pomocí sady .NET SDK |V tématu [najít výchozí účet úložiště pro clustery HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Odstranění clusterů HDInsight pomocí sady .NET SDK |V tématu [clusterů HDInsight odstranit](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Příklady
Tady jsou některé příklady na to, jak je operace provést pomocí sady SDK na základě ASM a fragmentu kódu ekvivalentní pro sadu SDK na základě Resource Manager.

**Vytvoření klienta CRUD clusteru**

* Příkaz staré (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nový příkaz (hlavní autorizace Service)
  
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

* Příkaz staré (ASM)
  
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
* Nový – příkaz
  
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

**Povolíte přístup protokolu HTTP**

* Příkaz staré (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Nový – příkaz
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Odstranění clusteru**

* Příkaz staré (ASM)
  
        client.DeleteCluster(dnsName);
* Nový – příkaz
  
        client.Clusters.Delete(resourceGroup, dnsname);

