---
title: Přidání dalších účtů Azure Storage do HDInsight
description: Naučte se, jak přidat další účty Azure Storage do stávajícího clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 6ad583fdb880e36e6ac9c2dfda56bb68378ea598
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313993"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Přidání dalších účtů úložiště do HDInsight

Naučte se používat akce skriptů k přidání dalších *účtů* Azure Storage do HDInsight. Kroky v tomto dokumentu přidávají *účet* úložiště do stávajícího clusteru HDInsight. Tento článek se týká *účtů* úložiště (nikoli výchozího účtu úložiště clusteru) a ne dalšího úložiště, jako je [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) a [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informace v tomto dokumentu se týkají přidání dalších účtů úložiště do clusteru po jeho vytvoření. Informace o přidávání účtů úložiště během vytváření clusteru najdete v tématu [Nastavení clusterů v HDInsight pomocí Apache Hadoop, Apache Spark, Apache Kafka a dalších](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop ve službě HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Název a klíč účtu úložiště Viz [Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md).
* Pokud používáte PowerShell, budete potřebovat AZ Module.  Další informace najdete v tématu [přehled Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Jak to funguje

Během zpracování skript provádí následující akce:

* Pokud účet úložiště již existuje v konfiguraci Core-site. XML pro cluster, skript se ukončí a neprovede se žádné další akce.

* Ověřuje, že účet úložiště existuje a je k němu možné přihlédnout pomocí klíče.

* Šifruje klíč pomocí pověření clusteru.

* Přidá účet úložiště do souboru Core-site. XML.

* Zastaví a restartuje [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop nitě](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) [Apache HADOOP MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)a služby [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) . Zastavení a spuštění těchto služeb umožňuje používat nový účet úložiště.

> [!WARNING]  
> Použití účtu úložiště v jiném umístění, než je cluster HDInsight, se nepodporuje.

## <a name="add-storage-account"></a>Přidání účtu úložiště

Použijte [akci skriptu](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) , aby se změny projevily s následujícími požadavky:

|Vlastnost | Hodnota |
|---|---|
|Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Typ (typy) uzlů|Hlavní uzel|
|Parametry|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (volitelné)|

* `ACCOUNTNAME` je název účtu úložiště, který se má přidat do clusteru HDInsight.
* `ACCOUNTKEY` je přístupový klíč pro `ACCOUNTNAME`.
* Parametr `-p` je volitelný. Je-li tento parametr zadán, klíč nebude šifrován a je uložen v souboru Core-site. XML jako prostý text.

## <a name="verification"></a>Ověření

Při zobrazení clusteru HDInsight v Azure Portal se při výběru položky __účty úložiště__ v části __vlastnosti__ nezobrazí účty úložiště přidané prostřednictvím této akce skriptu. Azure PowerShell a Azure CLI nezobrazují žádné další účty úložiště. Informace o úložišti nejsou zobrazeny, protože skript mění pouze konfiguraci `core-site.xml` clusteru. Tyto informace se nepoužívají při načítání informací o clusteru pomocí rozhraní API pro správu Azure.

Pokud chcete ověřit další úložiště, použijte jednu z níže uvedených metod:

### <a name="powershell"></a>PowerShell

Skript vrátí názvy účtů úložiště přidružené k danému clusteru. Nahraďte `CLUSTERNAME` skutečným názvem clusteru a potom spusťte skript.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Ve webovém prohlížeči přejděte na `https://CLUSTERNAME.azurehdinsight.net`, kde `CLUSTERNAME` je název vašeho clusteru.

1. Přejděte na stránku **HDFS** > **Configurations** > **Advanced** > **Custom Core-site**.

1. Sledujte klíče, které začínají na `fs.azure.account.key`. Název účtu bude součástí klíče, jak je vidět v této ukázkové imagi:

   ![ověření prostřednictvím Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Odebrat účet úložiště

1. Ve webovém prohlížeči přejděte na `https://CLUSTERNAME.azurehdinsight.net`, kde `CLUSTERNAME` je název vašeho clusteru.

1. Přejděte na stránku **HDFS** > **Configurations** > **Advanced** > **Custom Core-site**.

1. Odeberte následující klíče:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Po odebrání těchto klíčů a uložení konfigurace je potřeba restartovat Oozie, přízi, MapReduce2, HDFS a jeden podregistr jednou.

## <a name="known-issues"></a>Známé problémy

### <a name="storage-firewall"></a>Brána firewall úložiště

Pokud se rozhodnete zabezpečit svůj účet úložiště s omezeními **bran firewall a virtuální sítě** u **vybraných sítí**, Nezapomeňte povolit výjimku pro **důvěryhodné služby Microsoftu...** , aby HDInsight mohla získat přístup k vašemu účtu úložiště.

### <a name="unable-to-access-storage-after-changing-key"></a>Po změně klíče nejde získat přístup k úložišti.

Pokud změníte klíč pro účet úložiště, HDInsight už nebude moct získat přístup k účtu úložiště. HDInsight používá kopii klíče uloženou v mezipaměti Core-site. XML pro cluster. Tuto kopii v mezipaměti je potřeba aktualizovat tak, aby odpovídala novému klíči.

Když znovu spustíte akci skriptu, klíč se __neaktualizuje,__ protože skript zkontroluje, jestli už položka pro účet úložiště existuje. Pokud položka již existuje, neprovede žádné změny.

Řešení tohoto problému:  
1. Odeberte účet úložiště.
1. Přidejte účet úložiště.

> [!IMPORTANT]  
> Otočení klíče úložiště pro primární účet úložiště připojené ke clusteru se nepodporuje.

### <a name="poor-performance"></a>Špatný výkon

Pokud je účet úložiště v jiné oblasti než cluster HDInsight, může docházet ke špatnému výkonu. Přístup k datům v jiné oblasti odesílá síťový provoz mimo oblastní datové centrum Azure a přes veřejný Internet, což může zavádět latenci.

### <a name="additional-charges"></a>Další poplatky

Pokud je účet úložiště v jiné oblasti než cluster HDInsight, můžete si při fakturaci Azure všimnout dalších poplatků za výstup. Poplatek za výstupní data se použije, když data opustí místní datové centrum. Tento poplatek se uplatní i v případě, že je provoz určený pro jiné datové centrum Azure v jiné oblasti.

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak přidat další účty úložiště do stávajícího clusteru HDInsight. Další informace o akcích skriptu najdete v tématu [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md) .
