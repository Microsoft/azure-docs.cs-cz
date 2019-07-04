---
title: Přidat další Azure účty úložiště pro HDInsight
description: Zjistěte, jak přidat další služby Azure storage účty do existujícího clusteru HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 8a844465f7ba2222acd7efaf100c7b682c15adb2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433517"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Přidání dalších účtů úložiště pro HDInsight

Zjistěte, jak pomocí skriptových akcí můžete přidat další služby Azure storage *účty* pro HDInsight. Kroky v tomto dokumentu přidat úložiště *účet* do existujícího clusteru HDInsight se systémem Linux. Tento článek se týká úložiště *účty* (ne výchozí clusteru účet úložiště) a ne další úložiště, jako [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) a [Azure Data Lake Storage Gen2 ](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informace v tomto dokumentu je o přidání dalšího úložiště do clusteru po jeho vytvoření. Informace o přidání účtů úložiště při vytváření clusteru najdete v tématu [nastavení clusterů v HDInsight se Apache Hadoop, Apache Spark, Apache Kafka a další](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop v HDInsight. Zobrazit [Začínáme s HDInsight v Linuxu](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Název účtu úložiště a klíč. Zobrazit [spravovat nastavení účtu úložiště na webu Azure Portal](../storage/common/storage-account-manage.md).
* [Název clusteru správně notaci](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Pokud používáte PowerShell, bude nutné AZ modulu.  Zobrazit [Přehled prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Pokud jste nenainstalovali Azure CLI, přečtěte si téma [rozhraní příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Pokud používáte prostředí bash nebo příkazového řádku windows, budete také potřebovat **jq**, příkazového řádku procesoru JSON.  Zobrazit [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/). Bash na Ubuntu ve Windows 10 najdete v části [subsystém Windows pro Linux Instalační příručka pro Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Jak to funguje

Tento skript používá následující parametry:

* __Název účtu služby Azure storage__: Název účtu úložiště, které chcete přidat do clusteru HDInsight. Po spuštění skriptu, může HDInsight číst a zapisovat data uložená v tomto účtu úložiště.

* __Klíč účtu úložiště Azure__: Klíč, který uděluje přístup k účtu úložiště.

* __-p__ (volitelné): -Li zadán, klíče není zašifrovaný a je uložen v souboru core-site.xml jako prostý text.

Během zpracování skript provede následující akce:

* Pokud účet úložiště už existuje v core-site.xml konfiguraci clusteru, ukončí skriptu a jsou prováděny žádné další akce.

* Ověřuje, že účet úložiště existuje a je přístupný pomocí klíče.

* Klíč zašifruje pomocí přihlašovacích údajů clusteru.

* Účet úložiště se přidá do souboru core-site.xml.

* Zastaví a restartuje [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), a [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) služby. Zastavení a spuštění těchto služeb umožňuje je, aby používaly nový účet úložiště.

> [!WARNING]  
> Použití účtu úložiště v jiném umístění než HDInsight cluster se nepodporuje.

## <a name="the-script"></a>Skript

__Umístění skriptu__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Požadavky na__:  Skript se musí použít na __hlavním uzlům__. Nepotřebujete označte tento skript jako __trvalé__, jak přímo aktualizuje konfiguraci Ambari clusteru.

## <a name="to-use-the-script"></a>Pomocí skriptu

Tento skript je možné z prostředí Azure PowerShell, rozhraní příkazového řádku Azure nebo na webu Azure portal.

### <a name="powershell"></a>PowerShell

Pomocí [odeslat AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Nahraďte `CLUSTERNAME`, `ACCOUNTNAME`, a `ACCOUNTKEY` příslušnými hodnotami.

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

Pomocí [az akce skriptu hdinsight – spuštění](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Nahraďte `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`, a `ACCOUNTKEY` příslušnými hodnotami.

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

Zobrazit [použít akci skriptu pro spuštěný cluster](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Známé problémy

### <a name="storage-firewall"></a>Úložiště brány firewall

Pokud se rozhodnete k zabezpečení vašeho účtu úložiště pomocí **virtuální sítí a bran firewall** omezení **vybrané sítě**, je potřeba povolit výjimku **Povolit důvěryhodné Microsoft služby...**  tak, aby HDInsight můžete přístup k vašemu účtu úložiště.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Účty úložiště se nezobrazuje v portálu Azure portal nebo nástrojů

Při prohlížení clusteru HDInsight na webu Azure Portal, vyberte __účty úložiště__ položku __vlastnosti__ nezobrazuje úložišti účty přidané prostřednictvím tuto akci se skripty. Azure PowerShell a rozhraní příkazového řádku Azure se nezobrazují dalšího účtu úložiště buď.

Informace o úložiště není zobrazit, protože skript upravuje pouze core-site.xml konfiguraci clusteru. Tyto informace nepoužívá k načítání informace o clusteru pomocí rozhraní API pro správu Azure.

Chcete-li zobrazit informace o účtu úložiště do clusteru pomocí tohoto skriptu, použijte rozhraní Ambari REST API. Použijte následující příkazy pro načtení těchto informací pro váš cluster:

### <a name="powershell"></a>PowerShell

Nahraďte `CLUSTERNAME` s názvem clusteru správně notaci. Nejdřív zjistěte verzi konfigurace služby používá tak, že zadáte následující příkaz:

```powershell
# getting service_config_version
$clusterName = "CLUSTERNAME"

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version
```

Nahraďte `ACCOUNTNAME` skutečné názvy. Potom nahraďte `4` s skutečná verze konfigurace služby a zadejte příkaz. Po zobrazení výzvy zadejte přihlašovací heslo clusteru.

```powershell
# Update values
$accountName = "ACCOUNTNAME"
$version = 4

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$version" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>Bash
Nahraďte `myCluster` s názvem clusteru správně notaci.

```bash
export CLUSTERNAME='myCluster'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Nahraďte `myAccount` názvem skutečného úložiště účtu. Potom nahraďte `4` s skutečná verze konfigurace služby a zadejte příkaz:

```bash
export ACCOUNTNAME='"fs.azure.account.key.myAccount.blob.core.windows.net"'
export VERSION='4'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=$VERSION" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Nahraďte `CLUSTERNAME` s názvem clusteru správně malá a velká použita v obou skripty. Nejdřív zjistěte verzi konfigurace služby používá tak, že zadáte následující příkaz:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Nahraďte `ACCOUNTNAME` názvem skutečného úložiště účtu. Potom nahraďte `4` s skutečná verze konfigurace služby a zadejte příkaz:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```
---

 Podobně jako tento text se zobrazí informace vrácené z tohoto příkazu:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Tento text je příkladem šifrovaný klíč, který se používá pro přístup k účtu úložiště.

### <a name="unable-to-access-storage-after-changing-key"></a>Nelze získat přístup k úložišti po změně klíče

Pokud změníte klíč pro účet úložiště HDInsight už mít přístup k účtu úložiště. HDInsight používá kopii klíče uložené v mezipaměti v core-site.xml pro cluster. Tato kopie v mezipaměti musí být aktualizovány tak, aby odpovídaly novým klíčem.

Spuštění skriptu znovu akce nemá __není__ aktualizovat klíč, protože skript zkontroluje, jestli obsahuje položku pro účet úložiště už existuje. Pokud položka již existuje, neprovede žádné změny.

Chcete-li tento problém vyřešit, musíte odebrat existující položku pro účet úložiště. Použijte následující kroky pro odebrání existující položky:

> [!IMPORTANT]  
> Otáčení klíč úložiště pro účet primární úložiště připojené ke clusteru se nepodporuje.

1. Ve webovém prohlížeči otevřete webové uživatelské rozhraní Ambari pro váš cluster HDInsight. Identifikátor URI je `https://CLUSTERNAME.azurehdinsight.net`. Nahraďte `CLUSTERNAME` názvem svého clusteru.

    Po zobrazení výzvy zadejte přihlašovací jméno uživatele HTTP a heslo pro váš cluster.

2. V seznamu služeb na levé straně stránky vyberte __HDFS__. Vyberte __Configs__ kartu uprostřed stránky.

3. V __filtr...__  pole, zadejte hodnotu __fs.azure.account__. Vrátí záznamy pro všechny další účty úložiště, které jsou přidané do clusteru. Existují dva typy položek. __keyprovider__ a __klíč__. Oba obsahují název účtu úložiště jako součást názvu klíče.

    Tady jsou ukázkové položky pro účet úložiště s názvem __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Jakmile zjistíte, klíče pro účet úložiště, budete muset odebrat, použijte na červenou '-' ikony napravo od položky k jeho odstranění. Potom použijte __Uložit__ tlačítko uložte provedené změny.

5. Po uložení změn, použití akce skriptu k přidání účtu úložiště a nová hodnota klíče do clusteru.

### <a name="poor-performance"></a>Nízký výkon

Pokud je účet úložiště v jiné oblasti než HDInsight cluster, můžete se setkat nízký výkon. Přístup k datům v jiné oblasti automaticky odesílá síťový provoz, mimo Azure datovým centrem a přes veřejný internet, které můžou představovat latence.

### <a name="additional-charges"></a>Další poplatky

Pokud je účet úložiště v jiné oblasti než HDInsight cluster, můžete si všimnout poplatky za odchozí přenos dalších na fakturaci Azure. Poplatek za výchozí přenos dat se používá při opustí datovým centrem. Tato sazba platí i v případě, že se provoz určený pro jiného datového centra Azure v jiné oblasti.

## <a name="next-steps"></a>Další postup

Jste se naučili, jak přidat další účty úložiště do existujícího clusteru HDInsight. Další informace o akcí skriptů najdete v tématu [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md)
