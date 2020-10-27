---
title: Migrace na Azure Resource Manager nástroje pro HDInsight
description: Postup migrace na Azure Resource Manager vývojové nástroje pro clustery HDInsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-azurecli
ms.topic: how-to
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: deaf98077e0652dac8bb327839ffa2bf34007c9f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546207"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrace na vývojové nástroje založené na Azure Resource Manager pro clustery HDInsight

HDInsight používá nástroje pro HDInsight založené na službě Azure Service Manager (ASM). Pokud jste používali Azure PowerShell, rozhraní příkazového řádku Azure Classic nebo sadu HDInsight .NET SDK pro práci s clustery HDInsight, doporučujeme, abyste používali Azure Resource Manager verze PowerShellu, CLI a sady .NET SDK. Tento článek poskytuje odkazy na postupy, jak migrovat na nový přístup založený na Správce prostředků. Pokud je to možné, tento dokument popisuje rozdíly mezi ASM a Správce prostředků přístupy pro HDInsight.

> [!IMPORTANT]  
> Podpora PowerShellu, CLI a .NET SDK založeného na ASM skončí **1. ledna 2017** .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrace klasického rozhraní příkazového řádku Azure na Azure Resource Manager

> [!IMPORTANT]  
> Rozhraní příkazového řádku Azure neposkytuje podporu pro práci s clustery HDInsight. Azure Classic CLI můžete používat i v HDInsight, ale klasické rozhraní příkazového řádku Azure je zastaralé.

Níže jsou uvedené základní příkazy pro práci s HDInsight prostřednictvím klasického rozhraní příkazového řádku Azure:

* `azure hdinsight cluster create` – Vytvoří nový cluster HDInsight.
* `azure hdinsight cluster delete` – Odstraní existující cluster HDInsight.
* `azure hdinsight cluster show` -Zobrazit informace o existujícím clusteru
* `azure hdinsight cluster list` – vypisuje clustery HDInsight pro vaše předplatné Azure.

Pomocí `-h` přepínače zkontrolujte parametry a přepínače, které jsou k dispozici pro jednotlivé příkazy.

### <a name="new-commands"></a>Nové příkazy
K dispozici jsou nové příkazy Azure Resource Manager:

* `azure hdinsight cluster resize` -dynamicky mění počet uzlů pracovního procesu v clusteru.
* `azure hdinsight cluster enable-http-access` – povolí přístup HTTPs ke clusteru (ve výchozím nastavení zapnuté).
* `azure hdinsight cluster disable-http-access` – zakáže přístup HTTPs ke clusteru.
* `azure hdinsight script-action` -poskytuje příkazy pro vytváření a správu akcí skriptů v clusteru.
* `azure hdinsight config` -obsahuje příkazy pro vytvoření konfiguračního souboru, který lze použít s `hdinsight cluster create` příkazem k zadání informací o konfiguraci.

### <a name="deprecated-commands"></a>Zastaralé příkazy
Použijete-li `azure hdinsight job` příkazy k odeslání úloh do clusteru HDInsight, nejsou tyto příkazy k dispozici prostřednictvím příkazů Správce prostředků. Pokud potřebujete programově odesílat úlohy do služby HDInsight ze skriptů, místo toho byste měli použít rozhraní REST API poskytovaná službou HDInsight. Další informace o odesílání úloh pomocí rozhraní REST API najdete v následujících dokumentech.

* [Spouštění úloh MapReduce se systémem Hadoop ve službě HDInsight pomocí kudrlinkou](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Spouštění dotazů Apache Hive pomocí Apache Hadoop ve službě HDInsight pomocí kudrlinkou](hadoop/apache-hadoop-use-hive-curl.md)


Informace o dalších způsobech, jak spustit Apache Hadoop MapReduce, Apache Hive a Apache prasete interaktivně, najdete v tématu [použití MapReduce se systémem Hadoop ve službě HDInsight](hadoop/hdinsight-use-mapreduce.md), [použití Apache Hive se Apache Hadoop ve službě HDInsight](hadoop/hdinsight-use-hive.md)a [použití nástroje Apache prasete s Apache Hadoopm v HDInsight](./index.yml).

### <a name="examples"></a>Příklady
**Vytvoření clusteru**

* Old – příkaz (ASM) – `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nový příkaz – `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Odstranění clusteru**

* Old – příkaz (ASM) – `azure hdinsight cluster delete myhdicluster`
* Nový příkaz – `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Výpis clusterů**

* Old – příkaz (ASM) – `azure hdinsight cluster list`
* Nový příkaz – `azure hdinsight cluster list`

> [!NOTE]  
> Pro příkaz list, který určuje skupinu prostředků pomocí, `-g` vrátí jenom clustery v zadané skupině prostředků.

**Zobrazit informace o clusteru**

* Old – příkaz (ASM) – `azure hdinsight cluster show myhdicluster`
* Nový příkaz – `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrace Azure PowerShell do Azure Resource Manager
Obecné informace o Azure PowerShell v režimu Azure Resource Manager najdete v části [použití Azure PowerShell s Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

Rutiny Azure PowerShell Správce prostředků lze nainstalovat souběžně s rutinami ASM. Rutiny ze dvou režimů lze odlišit podle jejich názvů.  V režimu Správce prostředků se v názvech rutin *AzHDInsight* porovnání s *AZUREHDINSIGHT* v režimu ASM.  Například *New-AzHDInsightCluster* vs. *New-AzureHDInsightCluster* . Parametry a přepínače mohou mít názvy zpráv a k dispozici je mnoho nových parametrů při použití Správce prostředků.  Například několik rutin vyžaduje nový přepínač *s názvem-ResourceGroupName* . 

Než budete moct použít rutiny HDInsight, musíte se připojit k účtu Azure a vytvořit novou skupinu prostředků:

* [Connect – AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)

### <a name="renamed-cmdlets"></a>Přejmenované rutiny
Seznam rutin HDInsight ASM v konzole Windows PowerShellu:

```powershell
help *azurehdinsight*
```

V následující tabulce jsou uvedeny rutiny ASM a jejich názvy v režimu Správce prostředků:

| Rutiny ASM | Rutiny Správce prostředků |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke – AzHDInsightHiveJob](/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Spustit – AzHDInsightJob](/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop – AzHDInsightJob](/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Použití-AzHDInsightCluster](/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Čekání – AzHDInsightJob](/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nové rutiny
Níže jsou uvedené nové rutiny, které jsou k dispozici pouze v režimu Správce prostředků. 

**Rutiny související s akcemi skriptu:**

* **Get-AzHDInsightPersistedScriptAction** : Získá trvalé akce skriptu pro cluster a zobrazí je v chronologickém pořadí nebo získá podrobnosti o zadané trvalé akci skriptu. 
* **Get-AzHDInsightScriptActionHistory** : Získá historii akcí skriptu pro cluster a zobrazí ho v opačném chronologickém pořadí nebo získá podrobné informace o dříve provedené akci skriptu. 
* **Remove-AzHDInsightPersistedScriptAction** : Odebere trvalou akci skriptu z clusteru HDInsight.
* **Set-AzHDInsightPersistedScriptAction** : nastaví dříve spouštěnou akci skriptu jako trvalou akci skriptu.
* **Submit-AzHDInsightScriptAction** : odešle novou akci skriptu do clusteru Azure HDInsight. 

Další informace o použití najdete v tématu [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

**Rutiny související s identitou clusteru:**

* **Add-AzHDInsightClusterIdentity** : přidá identitu clusteru do objektu konfigurace clusteru, aby cluster HDInsight mohl získat přístup k Azure Data Lake Storage. Přečtěte si téma [Vytvoření clusteru HDInsight s Data Lake Storage pomocí Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Příklady
**Vytvoření clusteru**

Starý příkaz (ASM): 

```azurepowershell
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
```

Nový příkaz:

```azurepowershell
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
```

**Odstranění clusteru**

Starý příkaz (ASM):

```azurepowershell
Remove-AzureHDInsightCluster -name $clusterName 
```

Nový příkaz:

```azurepowershell
Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
```

**Seznam clusterů**

Starý příkaz (ASM):

```azurepowershell
Get-AzureHDInsightCluster
```

Nový příkaz:

```azurepowershell
Get-AzHDInsightCluster
```

**Zobrazit cluster**

Starý příkaz (ASM):

```azurepowershell
Get-AzureHDInsightCluster -Name $clusterName
```

Nový příkaz:

```azurepowershell
Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName
```

#### <a name="other-samples"></a>Další ukázky
* [Vytváření clusterů HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Odeslat Apache Hive úlohy](hadoop/apache-hadoop-use-hive-powershell.md)
* [Odeslání úloh Apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrace na novou sadu .NET SDK pro HDInsight
[Sada SDK HDInsight .NET](/previous-versions/azure/reference/mt416619(v=azure.100)) založená na službě Azure Service Management (ASM) je nyní zastaralá. Doporučujeme používat [sadu HDInsight .NET SDK](/dotnet/api/overview/azure/hdinsight)založenou na správě prostředků Azure správce prostředků. Následující balíčky HDInsight založené na ASM se už nepoužívají.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

V této části najdete odkazy na Další informace o tom, jak provádět určité úlohy pomocí sady SDK založené na Správce prostředků.

| Jak... používání sady HDInsight SDK založené na Správce prostředků | Odkazy |
| --- | --- |
| Sada Azure HDInsight SDK pro .NET|Viz [Azure HDInsight SDK pro .NET](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true) |
| Interaktivní ověřování aplikací pomocí Azure Active Directory se sadou .NET SDK |Viz [spustit Apache Hive dotazy pomocí sady .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Fragment kódu v tomto článku používá interaktivní přístup pro ověřování. |
| Ověřování aplikací, které nejsou interaktivní pomocí Azure Active Directory se sadou .NET SDK |Viz [Vytvoření neinteraktivních aplikací pro HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Odeslání úlohy Apache Hive pomocí sady .NET SDK |Viz [odeslání Apache Hive úloh](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Odeslání úlohy Apache Sqoop pomocí sady .NET SDK |Viz [Odeslání úloh Apache Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Výpis clusterů HDInsight pomocí sady .NET SDK |Viz [seznam clusterů HDInsight](hdinsight-administer-use-dotnet-sdk.md#list-clusters) . |
| Škálování clusterů HDInsight pomocí sady .NET SDK |Viz [škálování clusterů HDInsight](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Udělení nebo odvolání přístupu ke clusterům HDInsight pomocí sady .NET SDK |Viz [udělení a odvolání přístupu ke clusterům HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) . |
| Aktualizace přihlašovacích údajů uživatele HTTP pro clustery HDInsight pomocí sady .NET SDK |Viz [aktualizace přihlašovacích údajů uživatele http pro clustery HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) . |
| Vyhledání výchozího účtu úložiště pro clustery HDInsight pomocí sady .NET SDK |Viz [najít výchozí účet úložiště pro clustery HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) . |
| Odstranění clusterů HDInsight pomocí sady .NET SDK |Viz [odstranění clusterů HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Příklady
Následuje několik příkladů, jak se provádí operace pomocí sady SDK založené na ASM a ekvivalentního fragmentu kódu pro sadu SDK založenou na Správce prostředků.

**Vytvoření klienta CRUD clusteru**

* Old – příkaz (ASM)

  ```azurecli
  //Certificate auth
  //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
  const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
  var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
  var client = HDInsightClient.Connect(cred);
  ```
* Nový příkaz (autorizace instančního objektu)

  ```azurecli
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
  ```

* Nový příkaz (autorizace uživatele)

  ```azurecli
  //User auth
  //This will log the application in on behalf of the user.
  //The end-user will see a login popup.
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

**Vytvoření clusteru**

* Old – příkaz (ASM)

  ```azurecli
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
  ```

* Nový příkaz

  ```azurecli
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
  ```

**Povolení přístupu HTTP**

* Old – příkaz (ASM)

  ```azurecli
  client.EnableHttp(dnsName, "West US", "admin", "*******");
  ```

* Nový příkaz
  
  ```azurecli
  var httpParams = new HttpSettingsParameters
  {
         HttpUserEnabled = true,
         HttpUsername = "admin",
         HttpPassword = "*******",
  };
  client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);
  ```

**Odstranění clusteru**

* Old – příkaz (ASM)

  ```azurecli
  client.DeleteCluster(dnsName);
  ```

* Nový příkaz

  ```azurecli
  client.Clusters.Delete(resourceGroup, dnsname);
  ```
