---
title: Přidání dalších účtů úložiště Azure do HDInsight
description: Přečtěte si, jak přidat další účty Azure Storage do stávajícího clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: e29041942157e720cce3414f7b6e6904667c1894
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665475"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Přidání dalších účtů úložiště do HDInsight

Naučte se používat akce skriptů k přidání dalších *účtů* úložiště Azure do HDInsight. Kroky v tomto dokumentu přidávají *účet* úložiště do stávajícího clusteru HDInsight se systémem Linux. Tento článek se týká *účtů* úložiště (nikoli výchozího účtu úložiště clusteru) a ne dalšího úložiště, jako je [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) a [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informace v tomto dokumentu se týkají přidání dalších účtů úložiště do clusteru po jeho vytvoření. Informace o přidávání účtů úložiště během vytváření clusteru najdete v tématu [Nastavení clusterů v HDInsight pomocí Apache Hadoop, Apache Spark, Apache Kafka a dalších](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop ve službě HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Název a klíč účtu úložiště Viz [Správa nastavení účtu úložiště v Azure Portal](../storage/common/storage-account-manage.md).
* [Správně použita název clusteru](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Pokud používáte PowerShell, budete potřebovat AZ Module.  Další informace najdete v tématu [přehled Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Pokud jste nenainstalovali Azure CLI, přečtěte si téma [rozhraní příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Pokud používáte bash nebo příkazový řádek systému Windows, budete také potřebovat **JQ**procesor JSON na příkazovém řádku.  Viz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). Informace o bash v Ubuntu ve Windows 10 najdete v tématu [Instalační příručka k systému Windows pro Linux pro Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Jak to funguje

Tento skript má následující parametry:

* __Název účtu služby Azure Storage__: název účtu úložiště, který se má přidat do clusteru HDInsight. Po spuštění skriptu může HDInsight číst a zapisovat data uložená v tomto účtu úložiště.

* __Klíč účtu úložiště Azure__: klíč, který uděluje přístup k účtu úložiště.

* __-p__ (volitelné): Pokud je zadaný, klíč se nešifruje a uloží se do souboru Core-site. XML jako prostý text.

Během zpracování skript provádí následující akce:

* Pokud účet úložiště již existuje v konfiguraci Core-site. XML pro cluster, skript se ukončí a neprovede se žádné další akce.

* Ověřuje, že účet úložiště existuje a je k němu možné přihlédnout pomocí klíče.

* Šifruje klíč pomocí pověření clusteru.

* Přidá účet úložiště do souboru Core-site. XML.

* Zastaví a restartuje [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop nitě](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) [Apache HADOOP MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)a služby [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) . Zastavení a spuštění těchto služeb umožňuje používat nový účet úložiště.

> [!WARNING]  
> Použití účtu úložiště v jiném umístění, než je cluster HDInsight, se nepodporuje.

## <a name="the-script"></a>Skript

__Umístění skriptu__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Požadavky__: skript se musí použít na __hlavní uzly__. Tento skript není nutné označit jako __trvalý__, protože přímo aktualizuje konfiguraci Ambari pro cluster.

## <a name="to-use-the-script"></a>Použití skriptu

Tento skript lze použít z Azure PowerShell, Azure CLI nebo Azure Portal.

### <a name="powershell"></a>PowerShell

Pomocí [Submit-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Nahraďte `CLUSTERNAME`, `ACCOUNTNAME`a `ACCOUNTKEY` odpovídajícími hodnotami.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Azure CLI

Pomocí příkazu [AZ HDInsight Script-Action Execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Hodnoty `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`a `ACCOUNTKEY` nahraďte odpovídajícími hodnotami.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>portál Azure

Viz [použití akce skriptu na běžícím clusteru](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Známé problémy

### <a name="storage-firewall"></a>Brána firewall úložiště

Pokud se rozhodnete zabezpečit svůj účet úložiště s omezeními **bran firewall a virtuální sítě** u **vybraných sítí**, ujistěte se, že je **povolená výjimka Povolit důvěryhodné služby Microsoftu...** , aby HDInsight mohla získat přístup k vašemu úložišti. zohledňují.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Účty úložiště, které se nezobrazují v Azure Portal nebo nástrojích

Při zobrazení clusteru HDInsight v Azure Portal se při výběru položky __účty úložiště__ v části __vlastnosti__ nezobrazí účty úložiště přidané prostřednictvím této akce skriptu. Azure PowerShell a Azure CLI nezobrazují žádné další účty úložiště.

Informace o úložišti nejsou zobrazeny, protože skript upravuje pouze konfiguraci Core-site. XML pro cluster. Tyto informace se nepoužívají při načítání informací o clusteru pomocí rozhraní API pro správu Azure.

Pokud chcete zobrazit informace o účtu úložiště, které jste přidali do clusteru pomocí tohoto skriptu, použijte REST API Ambari. K načtení těchto informací pro cluster použijte následující příkazy:

### <a name="powershell"></a>PowerShell

Nahraďte `CLUSTERNAME` správným názvem clusteru použita. Nahraďte `ACCOUNTNAME` skutečnými názvy. Po zobrazení výzvy zadejte heslo pro přihlášení ke clusteru.

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash

Nahraďte `CLUSTERNAME` správným názvem clusteru použita. Nahraďte `PASSWORD` heslem správce clusteru. Nahraďte `STORAGEACCOUNT` skutečným názvem účtu úložiště.

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>přepsat

V obou skriptech nahraďte `CLUSTERNAME` správným názvem clusteru použita. Nejprve Identifikujte použitou verzi konfigurace služby zadáním následujícího příkazu:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

Nahraďte `ACCOUNTNAME` skutečným názvem účtu úložiště. Pak nahraďte `4` skutečnou verzí konfigurace služby a zadejte příkaz:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```

---

Informace vrácené z tohoto příkazu se zobrazí jako následující text:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Tento text je příkladem šifrovaného klíče, který se používá pro přístup k účtu úložiště.

### <a name="unable-to-access-storage-after-changing-key"></a>Po změně klíče nejde získat přístup k úložišti.

Pokud změníte klíč pro účet úložiště, HDInsight už nebude moct získat přístup k účtu úložiště. HDInsight používá kopii klíče uloženou v mezipaměti Core-site. XML pro cluster. Tuto kopii v mezipaměti je potřeba aktualizovat tak, aby odpovídala novému klíči.

Když znovu spustíte akci skriptu, klíč se __neaktualizuje,__ protože skript zkontroluje, jestli už položka pro účet úložiště existuje. Pokud položka již existuje, neprovede žádné změny.

Pokud chcete tento problém obejít, musíte odebrat existující položku pro účet úložiště. Existující položku odeberete pomocí následujícího postupu:

> [!IMPORTANT]  
> Otočení klíče úložiště pro primární účet úložiště připojené ke clusteru se nepodporuje.

1. Ve webovém prohlížeči otevřete webové uživatelské rozhraní Ambari pro váš cluster HDInsight. Identifikátor URI je `https://CLUSTERNAME.azurehdinsight.net`. Nahraďte `CLUSTERNAME` názvem svého clusteru.

    Po zobrazení výzvy zadejte přihlašovacího uživatele HTTP a heslo pro váš cluster.

2. V seznamu služeb na levé straně stránky vyberte __HDFS__. Pak vyberte kartu __Konfigurace__ ve středu stránky.

3. Do pole __Filter...__ zadejte hodnotu __FS. Azure. Account__. Tato funkce vrátí položky pro všechny další účty úložiště, které byly přidány do clusteru. Existují dva typy položek: __klíč a klíč__. Oba obsahují název účtu úložiště jako součást názvu klíče.

    Níže jsou uvedené příklady položek pro účet úložiště s názvem __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Po zjištění klíčů pro účet úložiště, který potřebujete odebrat, použijte k napravení ikony červené '-' na pravé straně položky a odstraňte ji. Pak použijte tlačítko __Uložit__ a uložte provedené změny.

5. Po uložení změn použijte akci skriptu a přidejte do clusteru účet úložiště a novou hodnotu klíče.

### <a name="poor-performance"></a>Špatný výkon

Pokud je účet úložiště v jiné oblasti než cluster HDInsight, může docházet ke špatnému výkonu. Přístup k datům v jiné oblasti odesílá síťový provoz mimo oblastní datové centrum Azure a přes veřejný Internet, což může zavádět latenci.

### <a name="additional-charges"></a>Další poplatky

Pokud je účet úložiště v jiné oblasti než cluster HDInsight, můžete si při fakturaci Azure všimnout dalších poplatků za výstup. Poplatek za výstupní data se použije, když data opustí místní datové centrum. Tento poplatek se uplatní i v případě, že je provoz určený pro jiné datové centrum Azure v jiné oblasti.

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak přidat další účty úložiště do stávajícího clusteru HDInsight. Další informace o akcích skriptu najdete v tématu [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md) .
